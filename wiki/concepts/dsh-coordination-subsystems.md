---
type: concept
created: 2026-08-30
updated: 2026-08-30
sources:
  - "raw/2026-08-30-dsh-src-subsystem-skills.md"
  - "raw/2026-08-30-dsh-src-subsystem-compaction.md"
---

# dsh 协调子系统（skill / subagent / goal / jobs 与自治限幅）

**定义**：agent loop 之外，dsh 用一组子系统回答"工作如何被拆分、延续、限制与复用"——skill 管**经验复用**，subagent 管**分解与委派**，goal/Ralph/jobs/schedule 管**持续目标与长任务**。共同设计基调：durable 状态进会话日志、进程内激活状态（armed/activation）刻意不持久化、自治循环全部带显式预算限幅。

## skill 系统（经验复用层）

- **形态**：`<name>/SKILL.md` 或 `<name>.md`，frontmatter 必填 `name`（kebab-case）+ `description`；可选 `whenToUse` / `disable-model-invocation` / `user-invocable`。与 Anthropic Agent Skills 同构：渐进披露——catalog 只注入 `[name, description≤500]` 摘要（对规范形做 sha256，**digest 变了才整条替换**注入 user-role 消息），body 按需经 `skill` 工具加载。
- **发现根分层**（低 rank 赢重名）：`.dsh/skills`(100) → `.agents/skills`(200) → 自定义目录 → `~/.dsh/skills` → bundled(600)；chokidar 监视 + 模型 write/edit 观察同步失效——**写入即生效，同一会话内模型即可见**。
- **三条触发路**：自动 catalog 注入；用户 `/name` 手势（唯一能调"仅人可调用"skill 的入口）；`skill({name})` 工具。
- **自进化接口**：`ctx.skills.register()` + `skills/change` 事件 = 任何插件都能在本会话内把经验写成 skill 文件并立即被模型看见——这是 [[raw/self-opt/skill/dsh-skill-evolve.md|dsh-skill-evolve]]（session→skill 提炼）与 [[raw/self-opt/skill/dsh-skill-evolve.md|dsh-auto-evolve]]（提案-验证-回滚）的机制地基。

## subagent（分解与委派）

- **`SubagentProvider` 接口**：声明能力集（agentOptions/outputSchema/depthLimit/toolFilter/persona），缺能力 loud fail。变体：`spawn`（全新 child 零父上下文）、`fork`（seed = 父日志到最后一个 turn/end 的平衡前缀，在途轮排除）、进程外后端（acp/codex/claude-code/dsh-sdk）。
- **continuable child**：持久 child session + 至多一个进程内 Activation；跨进程存活、可冷恢复。**depth 记账**：durable `delegationDepth`（header，单调下限）与运行时 `subagentDepth` 取 max——恢复后的 child 不能因新 options 冒充 top-level；默认 maxDepth 3。
- **委派即收紧**：child 创建时固定注入 `subagent:delegation` 上下文（"你的权限已固定、需审批的操作会自动拒绝"），父会话以 `source:'delegation'` 显式把 sandbox override + 审批 `'never'` 追加进 child 日志——child 的有效权限可从**它自己的日志**重建。
- 已知缺口：fork child 实际是 one-shot（continuable fork 的 report 工具会先于继承历史出现，issue #2124）。

## goal（会话内持续目标）

- durable 状态机 `active/paused/blocked/complete`（每次变更写完整快照 `goal/change` v1 事件 + CAS revision）；**activation（armed/disarmed）永不持久化**。
- **goal round**：armed 且 idle 时 driver 投递 `<goal_round>` 提示开新 turn；`maxGoalRounds` 默认 256，耗尽自动 block（code `round-limit`）。
- **resume/fork 后必须人工 re-arm**：goal 工具的 edit/pause/resume 要求"runtime root + 当前 open turn 内存在真正 `source.kind==='user'` 的消息"。代码级理由：`followup()/steer()` 不带 source 时默认解析为人类——若允许任意消息自动 re-arm，等于恢复后的会话无声重启自治循环。整个系统的安全依赖一个隐式契约：**每个程序化生产者（goal driver、jobs 通知、schedule、team message）都诚实自带 source**。

## Ralph loop 与 workflow（fresh-agent 工作流）

- Ralph：**脚本固定、模型只传数据**——部署方拥有 JS 脚本，模型调 `ralph({objective, maxRounds})`；每轮 `agent(prompt, {schema: reportSchema})` 起**全新** child（provider 强制 `inheritsParentContext === false`），轮间唯一载体是有界结构化 handoff（status/summary/evidence/nextSteps/blocker，默认 16384 字符），共享工作区才是真源。宿主侧对返回值再做逐 key 校验——不信任 provider 边界。

## jobs / schedule（后台工作与定时）

- **jobs**：`ctx.jobs` 注册后台工作，`job_output/job_list/job_kill` 工具收口；**反 runaway 预算**：完成通知默认 `wakeup`（followup 开新轮），但 `maxConsecutiveWakes` 默认 3——按"自该 owner 最近一次人类输入以来本插件开过的轮数"计，人类发言即重置；超预算降级为 `inject`（只入队不唤醒）。
- **schedule**：session-local 定时提醒（after/at/every≥300s），durable 权威是 `schedule/change` 事件；投递永远走 `followup`（不 steer），at-least-once 语义。

## agent-team（experimental 协作）

`ctx.agentTeams`：单一持久源 = Lead Session 日志上的四个整快照事件（`team/member|task|message/queued|message/delivered`），fold 重放出 roster（上限 8）+ task board（CAS revision、blockedBy 无环校验、writeScopes 只是 advisory）+ mailbox（先持久化后确认）。10 个 team_* 工具。

## 反馈面（自进化的原料，尚未接消费者）

- `feedback/record`（会话级，`/feedback` 命令）与 `ctx.messageFeedback`（消息级 rating+note sidecar）都是**持久可查询的人类判断**，但"never enters model context"——目前**没有任何在树内消费者**把它变成 prompt/skill/优化。这是留给自进化插件的现成接口（对照 [[raw/self-opt/harness/hermes-agent-gepa.md|Hermes 的 --eval-source sessiondb]]：同类信号，dsh 官方留白、社区插件补位）。
- hooks 桥（Claude Code/Codex hooks.json 直接复用）：SessionStart→agent/session-start、UserPromptSubmit→agent/pre-step、PreToolUse→tools/pre-execute、PostToolUse→tools/post-execute、Stop→agent/turn-stopping——不写插件也能做"会话结束自动提炼/注入"。

## 元级自证：dsh 用自己的机制开发自己

`.agents/notes/` 有 **1646 个工程决策笔记**（proposed/implemented/rejected/archived × feature/bug-fix/architecture/…，"每个非平凡 PR 必须新增或更新至少一个 note"，机器校验）；`.agents/skills/` 有 10 个仓库自用 skill（dsh-doc、dsh-code-review、dsh-pre-push-checks…）——而 `.agents/skills` 正是 skill 发现根 rank 200。**开发 agent 的经验沉淀与运行时 agent 用的是同一套 skill 机制**。这与本库 [[wiki/concepts/llm-wiki|LLM Wiki]] 的 log.md（追加式时间线）+ skill 沉淀思路同构。

## 与其他概念的关联

- skill 注入与 compaction 压力检查都挂在 [[wiki/concepts/dsh-agent-loop|agent/pre-step]] 上；goal round 就是 turn 层级之上的 round。
- 与 [[raw/self-opt/INDEX.md|self-opt 四层图谱]] 对照：skill=skill 层、subagent/goal/Ralph=harness 层、feedback=用户数据信号——dsh 是该图谱中唯一**开源可自托管**的完整实现样本（AI 推断，待专页对照验证）。

## 依据来源

- [[raw/2026-08-30-dsh-src-subsystem-skills.md|subsystem skills 文档剪藏]]
- 源码实读：`packages/skill/{skill,tool-skill,skill-filesystem}/src`、`packages/subagent/subagent/src/{types,depth,child-agent}.ts`、`packages/goal/{goal,goal-round-driver,tool-goal}/src`、`packages/workflow/tool-ralph/src/index.ts`、`packages/jobs/tool-jobs/src/index.ts`、`packages/schedule/schedule/src/runtime.ts`、`packages/experimental/agent-team/src`、`.agents/notes/README.md`（AI 综合自实读，路径相对仓库根 @ cd5ef814）
