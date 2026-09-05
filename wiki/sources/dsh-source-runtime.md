---
type: source
created: 2026-08-30
updated: 2026-08-30
sources:
  - "raw/2026-08-30-dsh-src-agent-lifecycle.md"
  - "raw/2026-08-30-dsh-src-tool-execution-pipeline.md"
  - "raw/2026-08-30-dsh-src-event-producer-consumer.md"
  - "raw/2026-08-30-dsh-src-capability-seams.md"
  - "raw/2026-08-30-dsh-src-subsystem-core.md"
  - "raw/2026-08-30-dsh-src-subsystem-session.md"
  - "raw/2026-08-30-dsh-src-subsystem-compaction.md"
  - "raw/2026-08-30-dsh-src-subsystem-skills.md"
  - "raw/2026-08-30-dsh-src-subsystem-tools.md"
---

# dsh 源码仓运行时架构（本地实读，v0.1.2-alpha.1）

**来源信息**：本地 clone `/Users/zn-ice/2026/deepseek-harness`，commit `cd5ef814`，tag `dsh-v0.1.2-alpha.1`（2026-08-30 实读分析）。TypeScript / pnpm monorepo：约 50 个 packages + apps/{cli,web} + python SDK，4700+ TS 文件。仓库自带三套文档：根 `docs/`（架构）、`docs/subsystems/`（45 份子系统文档，多数为生成物）、`docs/cookbook/`（操作手册）；本页依据的 9 份已剪藏，其余论断以本地路径溯源。

**一句话概括**：插件生态文档（[[wiki/sources/dsh-official-docs|官方文档 32 页]]）之外的另一半——**运行时如何真正转起来**：turn/step 循环、append-only 会话日志作为单一事实源、三道 waterfall 的工具管线、以及 skill/subagent/goal 等协调子系统。

## 核心要点

1. **turn/step 状态机**：`ReactLoopAgent`（`packages/core/agent-loop/src/agent.ts`）以三态 `Phase`（idle/maintenance/running）驱动；turn = 一次输入的排空（0+ step），step = 一次模型请求 + 其工具执行。inbox 是 durable 投影（先 append `agent/inbox/spliced` 再改内存）；`followup`（下个 turn）/`steer`（step 边界）立即唤醒，`inject` 只入队不唤醒。（[[wiki/concepts/dsh-agent-loop]]）
2. **会话日志是唯一事实源**：`deriveMessages()` 从 append-only log 投影出模型历史；"model-visible means logged" 是运行时断言的不变量（`agent-loop/src/invariant.ts` 对每个请求做 `JSON.stringify` 级比对，失配即 "log-reconstruction desync"）。事件词表 51 个（12 核心 + 39 插件扩展），surface 投影只有 3 种事件可见于模型。resume 有崩溃修复合成 closer（不截断、确定性）。compaction 经 positional replace + `replaceGeneration` 计数实现"原地压缩"。（[[wiki/concepts/dsh-session-log]]）
3. **工具管线四层防线**：`tools/pre-execute`（可重排序策略：hooks/审批 ask）→ monotonic guards（只许收紧、identity 由铸币 token 保护）→ `tools/execute`（around 调度：timeout/retry/checkpoint）→ `tools/post-execute`（接受/替换/block + additionalContexts）。审批 fail-closed（无服务/无 agent/无应答一律 deny 且 reason 各异）。PTC（`run_code` 代码模式）让模型写程序批量调工具，子调用携带父 token 完整重入管线。（[[wiki/concepts/dsh-tool-pipeline]]）
4. **协调子系统自带"反失控"预算**：goal 的 armed/disarmed 进程内激活（resume/fork 后必须人工 re-arm）、jobs 的 `maxConsecutiveWakes`（默认 3，人类输入重置）、goal round 与 Ralph 的 256 轮上限——自治循环全部被显式限幅。（[[wiki/concepts/dsh-coordination-subsystems]]）
5. **skill 系统与 Anthropic Agent Skills 同构**：SKILL.md + frontmatter + 渐进披露（catalog 只发 name/description 摘要，digest 变更才整条替换注入）；写入 `.dsh/skills` 等发现根**同一会话内即可生效**——这是 dsh 上"会话→经验提炼"自进化插件（如 [[raw/self-opt/skill/dsh-skill-evolve.md|dsh-skill-evolve]]）的机制地基。
6. **防御性工程是显性纪律**：`docs/defensive-patterns.md` 7 条模式；每个 workspace 包必须发布 `./invariant` companion（空实现也要注释解释，`verify-package-invariants` 机器校验）；`.agents/notes/` 有 1646 个工程决策笔记，每个非平凡 PR 强制新增——dsh 用自己的 skill 机制沉淀开发经验（`.agents/skills` 正是 skill 发现根之一）。

## 值得注意的细节

- 事件三域是扩展点选择的第一个决策：**session events**（durable 事实，经 `session/event` 广播）/ **agent events**（`agent/*`，活体协调：inbox/status/pre-step/request）/ **capability events**（`fs/*`、`tools/*`、`telemetry/*` 挂策略与适配器）。四类调度：emit 观察 / waterfall 可短路 around / parallel / serial（`agent/turn-stopping`）。全表见剪藏 event-producer-consumer。
- `docs/architecture.md` 给出 19 行"目标 → 机制"对照表（加 provider / 加工具 / 加后台任务 / fork 会话……各对应哪个 ctx 键或事件），是找扩展点的地图。
- `agent/pre-step` 是"模型看到什么"的总闸：compaction（压力检查）、skill catalog 注入、subagent/persona 上下文、hooks 的 UserPromptSubmit 全部挂这一个 waterfall 上。
- `request-error` 重试只有两种结局：listener 不调 `next()` 返回 `{kind:'retry'}`（它拥有恢复），或一路委托到底（失败终局）。官方执行者 `dsh-llm-retry` 用持久化 `llm/retry` 事件做**跨崩溃的重试计数**。
- compaction 摘要输入刻意构造成"上一条真实请求的真前缀"以复用 provider KV cache；摘要输出强制 8 段固定 Markdown 结构。
- spill 是 "model-visible means logged" 的唯一显式豁免：超限工具结果全文落盘 log 之外，log 里留 preview + locator。
- fork 拒绝在 open turn 边界裁剪（`OPEN_TURN` 错误，宁失败不静默）；fork child 目前实际是 one-shot（continuable fork 有已知 issue #2124）。
- 委派即收紧：child agent 创建时父会话显式以 `source:'delegation'` 追加 sandbox override + 审批策略 `'never'` 进 child 日志，使 child 的有效权限可从其日志重建。

## 后续线索

- 未剪藏的大目录：`docs/config-catalog.md`、`docs/module-graph.md`（1958 行）、`docs/persistence-catalog.md`、`docs/tool-catalog.md`（2279 行，64 工具条目/59 唯一名）——均生成物，深挖对应域时再取。
- 未剪藏的子系统文档（45 份中本次只剪 5 份）：goal、subagent、agent-team、workflow、typert、spill、llm-streaming、system-prompt、sandbox、approval 等；分析中的论断以本地路径溯源。
- `docs/postmortem/`、`docs/cordis-api/` 未剪藏。
- [[wiki/sources/dsh-official-docs|官方文档来源页]]后续线索中登记的 vendor/README、cordis.patch.yml 等仍待剪。
- 与 [[raw/self-opt/INDEX.md|self-opt 检索库]] 的连接：dsh 运行时的 goal/continuable subagent/skill 写入即生效机制，是"用户数据驱动自进化"四层图谱中 harness 层+skill 层的一个**开源、可自托管的实现样本**，值得单独做对照页。
