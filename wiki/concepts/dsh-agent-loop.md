---
type: concept
created: 2026-08-30
updated: 2026-08-30
sources:
  - "raw/2026-08-30-dsh-src-agent-lifecycle.md"
  - "raw/2026-08-30-dsh-src-subsystem-core.md"
  - "raw/2026-08-30-dsh-src-event-producer-consumer.md"
  - "raw/2026-08-30-dsh-src-subsystem-session.md"
---

# dsh Agent Loop（turn/step 状态机）

**定义**：dsh 的 agent 循环是一个显式的双层级状态机——**turn**（一次已准入输入的排空，含 0 个或多个 step）与 **step**（一次模型请求 + 该请求引发的全部工具执行）——所有拦截、取消、恢复、续接都表达为这条状态机上的事件钩子，而非对循环代码的修改。

> 层级：`round`（外层策略迭代，如 goal round / Ralph attempt）→ `turn` → `step`。round 计数属于各策略，不等于 turn 数。（glossary，剪藏 core.md）

## 循环本体

- **`Agent` 接口**与 **`ReactLoopAgent` 实现**分离：接口在 `packages/core/agent`（字段 id/options/session/inbox/status/ctx；方法 cancel/send/followup/steer/inject），唯一实现在 `packages/core/agent-loop`（`agent.ts`），loop 外的代码不得依赖实现。"agentLoop 是唯一具体循环"——对应 [[wiki/concepts/dsh-plugin-capability-boundaries|能力边界普查]] 的 core 结论。
- **Phase 三态**：`idle | maintenance | running`。`kick()` = `while (await this.turn())`——driver 跨连续排队 turn 存活；返回 true 表示同一 driver 立即开下一个 turn。
- **每 turn**：append `turn/start` → 循环 { claim inbox → `agent/pre-step` waterfall → `step/start` → 逐条 `user/message` → step() → `step/end` } → （自然停止且 next-step 空）`agent/turn-stopping`（serial）→ `turn/end`。
- **每 step**：`buildRequest()`（prompt 装配结果 + 工具 schema + `agent/request` waterfall）→ `llm/stream` → `assistant/chunk*` → `assistant/message` → 工具批执行 → 有工具续接则再一步。

## inbox：输入如何进入

inbox 不是内存队列，而是 **durable 投影**：每次变更先 append `agent/inbox/spliced` 事件再改内存（事件先于内存提交，同步观察者看到的是拼接前列表），构造时从日志重放。两条有序列表 `next-turn` / `next-step`：

| 入口 | 目标列表 | 唤醒 driver | 语义 |
|---|---|---|---|
| `followup(msg)` | next-turn | 是 | 下一个独立 turn 的普通消息 |
| `steer(msg)` | next-step | 是 | 运行中在下一个 step 边界被领取（转向） |
| `inject(msg)` | next-step | **否** | 只入队不唤醒；等到下次 followup/steer 才随整批被领取 |

工具的 `additionalContexts` 也走 inbox：暂存 next-step，在下一个 step 边界被 claim。

## agent/pre-step：模型看到什么的总闸

waterfall，listener 的返回值即**权威决策**：`{kind:'reject'}`（本步不发生，已 claim 的消息既不重现也不丢弃，turn 以 0 step 关闭但日志记录了尝试）或 `{kind:'enter', messages, startsRequestSeries?}`（可改写消息集）。挂在它上面的官方 listener：compaction 压力检查、skill catalog 注入、plan-mode、subagent/persona/time-context 等上下文注入、hooks 桥的 UserPromptSubmit。这是一个插件能重排"模型输入"的全部权力所在，也是理解 dsh 上下文工程的钥匙。

## 取消与错误恢复

- `cancel(cause)`：cause（user/parent/hook/disposed）直接成为 `AbortSignal.reason`；inbox 按 next-step → next-turn 顺序清空；流式中止时已流出的前缀被固化为 `assistant/message {interrupted:true}`；未派发的工具补写合成错误结果（`TOOL_ABORTED_BEFORE_DISPATCH`），保证 replay 平衡。
- **`agent/request-error` waterfall**（在失败的 step 已关闭、turn 未关闭之间）：listener 不调 `next()` 返回 `{kind:'retry'}` = 它拥有恢复；委托到底 = 失败终局。官方执行者 [[wiki/entities/deepseek-harness|dsh]] 自带的 `llm-retry` 插件：指数退避 + 对称 jitter、优先 provider 的 `retryAfter`、**每次调度先持久化 `llm/retry` 事件再等待**（重试计数跨崩溃可恢复）。
- compaction 的上下文溢出恢复也挂这里（见 [[wiki/concepts/dsh-session-log]]）。

## 续接（continuation）三层

1. **step 内**：`step()` 返回"模型还要继续"则同 turn 再开一步——由工具结果驱动而非 listener。
2. **消息系列**：`startsRequestSeries` + `request/header {reason:'series'}`，在同一模型配置下开启显式独立的消息系列。
3. **跨 turn 策略**：goal round / continuable subagent / Ralph 都是通过 `followup()/steer()` 投递带结构化 source 的消息实现续接（见 [[wiki/concepts/dsh-coordination-subsystems]]）。`agent/turn-stopping` 的 listener 想反对停止也只能 `steer()` 一条消息——"数据决定，listener 顺序不能改变结果"。

## scope：per-agent 注册

- **scope key** = live agent 对象自身（按对象同一性比较）；`agent.ctx` 上的注册**同时**获得 scope 可见性与 scope 生命周期。
- **shadowing**：同名注册最近 scope 赢（per-agent persona / 工具变体机制）；**restriction** 只过滤继承面（global + 祖先链），本 scope 自己注册的工具豁免；两层扁平、不向 subagent 继承，子树行为用 lineage 数据表达。
- 事件路由 "events flow up, never down"：scoped dispatch 的 carrier 放行未标记 listener 与祖先链上的 listener——常驻组合能观察其下每个 agent。
- preset 的 standing mount 机制：agent 的 scope key 父链到常驻组合子树，从链上继承工具/提示段，实现"单个会话不同能力集"（isolate realm 防服务泄漏）。

## 与其他概念的关联

- 事件全景表（谁发谁听）→ 剪藏 [[raw/2026-08-30-dsh-src-event-producer-consumer.md|event-producer-consumer]]；四类调度模式与 [[wiki/concepts/dsh-plugin-development|插件事件机制]] 相接。
- 状态机写下的每个 durable 事实都进 [[wiki/concepts/dsh-session-log|会话日志]]——循环与日志是一体两面。
- 工具批调度（barrier + 有界滚动池，默认并行上限 10，start 前重分类）细节在 [[wiki/concepts/dsh-tool-pipeline]]。

## 依据来源

- [[raw/2026-08-30-dsh-src-agent-lifecycle.md|agent-lifecycle 时序图]]、[[raw/2026-08-30-dsh-src-subsystem-core.md|subsystem core 文档]]（均为源码仓 docs 剪藏 @ cd5ef814）
- 源码实读：`packages/core/agent-loop/src/{index,agent,tool-calls,runtime-context,invariant}.ts`、`packages/core/agent/src/{index,inbox,dispatch,runtime-types}.ts`、`packages/core/scope/src/{index,store}.ts`（AI 综合自实读，路径相对仓库根）
