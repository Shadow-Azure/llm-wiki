---
type: concept
created: 2026-08-30
updated: 2026-08-30
sources:
  - "raw/2026-08-30-dsh-src-subsystem-tools.md"
  - "raw/2026-08-30-dsh-src-tool-execution-pipeline.md"
  - "raw/2026-08-30-dsh-src-capability-seams.md"
---

# dsh Tool Pipeline（工具执行管线与安全防线）

**定义**：dsh 中每次工具调用固定穿过一条**三道 waterfall + 单调 guard + 冻结通知**的管线——策略（hooks、审批、沙箱、文件系统闸门、超时重试、结果改写）全部以监听者身份挂载，不需要改循环代码；安全语义由管线的**顺序与单调性**保证，而非由某个中心权限服务保证。

## 工具定义与注册

- `defineTool` DSL（自研 schema 编译器，显式迭代任务图、编译期拒绝词汇表外的键）：模型可见面只有 `name/description/parameters` 三字段；**输出也被 schema 约束**（`output.schema` 校验通过才 `render` 成 content）。`timeoutMs`/`isConcurrencySafe` 是调度元数据，绝不发给模型。
- 注册名 `run_code` 无条件保留（即使 native 部署）——"任何 agent 都可能自选 code mode"，防止 preset 挂载后冲突。
- 工具 schema 经 `ctx.systemPrompt.tools()` 进入 prompt 装配；`tools/change` 事件故意不做 scope 过滤，让每个 agent 的下次组装感知全局变化。

## 管线四层（顺序即安全）

1. **`tools/pre-execute`** waterfall：可重排序的进入策略，三态 `allow | deny{reason} | ask{reason?}`。参数**不可改写**（"history, audit, UI, and execution must agree"）。hooks 桥（Claude Code / Codex 的 PreToolUse 直接映射到这）挂这里。
2. **monotonic guards**：`string | undefined` 返回——**没有 allow**，只能说"不"，监听者顺序无法把拒绝翻回允许。不可重排序的 owner 策略（区别于可扩展的 waterfall 策略）放这里。
3. **`tools/execute`** waterfall（around 调度）：timeout（`timeout-policy`）、重试、session checkpoint（dispatch 前 flush 持久化，失败 fail-closed）。唯一允许替换 exec.signal 的层，且 caller 信号被重新熔接——wrapper 换信号无法让取消失效。
4. **`tools/post-execute`** waterfall：`accept{content?|value?} + additionalContexts? | block{feedback}`——接受/替换单一投影/给下一轮附加上下文/block 成纠错性 isError。spill 策略、hooks 的 PostToolUse 挂这里。

之后：定义自有 `finalizeContent`（执行开始时快照——防恶意参数 getter 在校验期间偷换回调）→ `tools/result`（emit，frozen 权威结果，监听者失败被隔离）。任一 stage 抛出都归一化为结构化 `isError` 结果。

## 审批（fail-closed）

`ctx.approval` 一次性提示：pre-execute 返回 `ask` 才触发；`allowed-once` 是唯一 grant。**四层降级到 deny**：无 approval 服务 / 无 agent / 请求异常 / rejected-cancelled-unavailable——且 reason 各不相同，让模型能区分"人说不"与"审批通道缺席"。会话级策略 `'never'` 在 waterfall dispatch **之前**就拒绝（后注册的 answerer 即使 prepend 也绕不过）。`ApprovalRequest` 故意不带工具参数——answerer 用 callId 把提示贴到已流式呈现的调用上，避免第二份漂移的参数。`approval/asked|decided` 是 log-only 审计事件。

## 文件系统闸门与沙箱

- **read-before-edit** 不在 core 管线：`fs/write-intent` / `fs/edit-intent` waterfall 由文件工具在 execute 体内发出，`fs-observation-policy` 插件记录"本会话读过哪些文件@哪个版本"——未见过的文件 edit 抛 `FS_NOT_OBSERVED`，见过的以版本号做 CAS 基准（stale 报 `FS_STALE_VERSION`）。授权是**新鲜度制**：任何窗口化 read 都能授权写。
- **沙箱**：`ctx.sandbox` seam 只管**进程级 confinement**（Linux bwrap→landlock 链、darwin seatbelt、Windows ACL，带功能性 probe）；容器/远程执行（e2b）是**替换整个 `ctx.fs` + `ctx.subprocess` 世界**而非 sandbox provider——"one sandbox, one handle"，fs 写的文件 shell 立即可见。confine 返回值带该后端**自己的拒绝签名方言**（区分"sandbox 正确拦截"与"runner 没跑起来"——后者是基础设施故障，不得当任务失败上报）。
- **permission-presets**：把 sandbox mode + approval policy 两个独立旋钮打包成命名预设（workspace-write / danger-full-access），纯打包层、不 own 任何 enforcement。

## PTC（Programmatic Tool Calling，`run_code`）

模型写**程序**（循环/分支/fan-out）批量调工具，只 curate print/return 的内容——原生模式每个中间 tool-result 都回模型 context，多步工作 token 重且串行。关键设计：

- `run_code` 是保留 transport，不进可过滤注册层（restrict 删不掉、scoped 遮不住）；per-scope `presentAs(mode)` 让 PTC agent 与 native agent 同进程并存。
- **子调用携带父 token 完整重入管线**：每个 sub-call 走完整 pre-execute → guards → execute → post-execute（审批、沙箱、spill 对子调用照样生效），日志记 `tool/code-dispatch` 事件对；拒绝作为 binding rejection 抛给程序。
- **collapsed call 在策略管线之前就终结**：PTC scope 下直接点名普通工具的调用（非嵌套）连 pre-execute 都不观察——"pre-execute, approval ask, and guards must never observe — or worse, approve — a call that can only fail"。但真未知名仍走 UNKNOWN_TOOL 路径，策略能看到所有真实到达注册表的名字。
- prompt 侧与执行侧用**同一个** collapse 谓词函数——"prompt 不能陈述一条 registry 不执行的规则"。

## 工具面概览

生成目录 `docs/tool-catalog.md`（2279 行）：26 个工具包、64 条目、59 个唯一工具名——shell/PTY、文件、子代理编排（subagent/send_message/ralph/agent-team 10 个）、状态（todo/goal/schedule）、会话内省（session_* 5 个）、外部（web_search/web_fetch/lsp）、`run_code`，另有 7 个 `cordis_*` 动态插件工具**刻意不在任何 shipped tree**（opt-in）。

## 与其他概念的关联

- 管线挂在 [[wiki/concepts/dsh-agent-loop|agent loop]] 的 step 内执行；工具结果进 [[wiki/concepts/dsh-session-log|会话日志]]（tool/call 先记后执行、tool/result 是唯一模型可见结果）。
- 三角色分包（dsh-shell 定义 / dsh-bash-local 提供方 / dsh-tool-bash 消费方）→ [[wiki/concepts/dsh-plugin-capability-boundaries|能力边界普查]]；与 Obsidian/VSCode 的安全模型对比 → [[wiki/concepts/plugin-mechanism-comparison]]。
- 与 [[raw/self-opt/INDEX.md|self-opt 检索库]] 的连接：审批闸门 + monotonic guards + fail-closed 是"治理式自进化"（对照 [[raw/self-opt/skill/dsh-skill-evolve.md|dsh-auto-evolve]] 的沙箱验证+回滚）在**运行时层**的对应物——dsh 把安全做成管线顺序而不是外挂审查。

## 依据来源

- [[raw/2026-08-30-dsh-src-subsystem-tools.md|subsystem tools 文档剪藏]]、[[raw/2026-08-30-dsh-src-tool-execution-pipeline.md|tool-execution-pipeline 流程图剪藏]]
- 源码实读：`packages/core/tools/src/{index,schema,ptc,invariant}.ts`、`packages/interaction/user-approval/src/index.ts`、`packages/fs/fs-observation-policy/src/index.ts`、`packages/sandbox/*`、`packages/e2b/*`、`packages/guard/*`（AI 综合自实读，路径相对仓库根 @ cd5ef814）
