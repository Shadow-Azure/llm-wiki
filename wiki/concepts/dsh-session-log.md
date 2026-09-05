---
type: concept
created: 2026-08-30
updated: 2026-08-30
sources:
  - "raw/2026-08-30-dsh-src-subsystem-session.md"
  - "raw/2026-08-30-dsh-src-subsystem-compaction.md"
  - "raw/2026-08-30-dsh-src-agent-lifecycle.md"
---

# dsh Session Log（会话日志作为单一事实源）

**定义**：dsh 把每个会话实现为 **append-only 的 SessionEvent 日志**，模型历史、fork、resume、transcript、UI、遥测全部是从这条日志投影出来的视图；"**model-visible means logged**"（凡到达模型请求的必可从日志重建）是运行时断言的硬不变量，不是文档口号。

## 事件词表与投影

- **核心事件 12 个**（`SessionEventMap`）：turn/start·end、step/start·end、user/message、assistant/chunk、assistant/message、tool/call、tool/result、request/header、request/context、session/end-seed。插件可 declaration-merge 扩展，全仓已知词表 51 个（compaction、approval、todo、goal、schedule、hook、team 等都是 log-only 事件）。
- **两层投影**：log → **surface**（模型可见顺序，由 surfaceOp 标记维护）→ `deriveMessages()`。只有 3 种事件模型可见：`user/message`、`assistant/message`（空内容返回 null）、`tool/result`。`assistant/chunk` 是回放/UI 保真数据——`assistant/message` 用 `sourceEventSeqs` 反向引用它由哪些 chunk 聚成（存储时区间编码压缩）。
- **provenance 即失效信号**：动态上下文快照靠检查 `sourceEventSeqs` 是否包含自己的 seq 来感知"被 compaction 遮蔽了，需重新投影"。
- 因 `SessionEventMap` 可扩展，switch **刻意不用 `assertNever`**——未知事件是合法扩展，与 TypeScript 穷尽性习惯相反。

## 持久化

- 默认 JSONL + zstd：`~/.dsh/sessions/<projectKey>/<sessionId 编码>/session.jsonl.zstd`（`$DSH_HOME` 可改）；首行 header（version/id/cwd/parentSession/delegationDepth…），其余每行一个事件。连续 ≥3 条同型 delta chunk 打成一行（实测体积 -60%）。SQLite 后端 opt-in。版本不合 → `SessionFormatUnsupportedError`（fail-closed，读不了 ≠ 坏了）。
- 写为 write-behind（默认 200ms 窗口），后台写失败 → 批次原序回填 + 暂停自动写，绝不把失败伪装成新 session 事件。
- `list()` 只读首行 header——会话选择器成本与会话数线性、与日志总量无关。

## compaction：日志的"原地压缩"

- 触发两条路：**pressure**（`agent/pre-step` 里按 contextWindow × 0.8 阈值 / 保留尾 0.16）与 **context-overflow**（`agent/request-error` 且错误码为 `CONTEXT_WINDOW_EXCEEDED`，绕过阈值强制缩减，默认限 1 次重试）。
- 机制：**positional replace**——先 append `compaction/prune`（工具结果剪枝：8192 字符阈值，head 4096 + tail 1024）或 `compaction/summary`（LLM 摘要，强制 8 段固定 Markdown 结构），再同步相邻地 append 一个替换型 `user/message`（`surfaceOp: replace` + sourceEventSeqs 引用被遮蔽区间）。原文永不删除——删除只是视图层的遮蔽。`replaceGeneration` 单调计数驱动 deriveMessages 缓存重建。
- 摘要输入刻意构造成上一条真实请求的**真前缀**（复用 provider KV cache）；tool/result 的替换被 core 约束**只许改 content**，其余字段必须逐字段相等。
- 锁顺序固定：start → summary → 替换 → end，最后放锁——崩溃中途留下可检测的 orphan start 而非虚假完成。

## fork / resume / 修复

- **fork**：`ctx.sessions.fork(source, boundary)` 取平衡前缀做 seed；尾端是 open turn 时抛 `OPEN_TURN` 拒绝（宁失败不静默裁剪）；子 header 记 `parentSession`。
- **resume**：读 stored log → `interruptedTurnClosers()` **合成**缺失的 closer（给未决工具补 error 结果、补 step/end、补 `turn/end {interrupted}`）——不截断、确定性（同一次崩溃重复恢复产出逐字节相同事件）。resume 后首次请求记 `request/header {reason:'resume'}`。
- **人类 transcript 是另一条投影**：只取 append-origin 事件，刻意不遮蔽（模型可见面替换掉的内容，人已经看到过，不能从人类记录里消失）。

## spill：不变量的显式豁免

超限工具结果全文写 log 之外（`ctx.spillStore` seam，本地 provider 用 0700 目录 + 0600 独占写），log 里留 preview + locator——replay 重建出的是截断引用而非原文。由 30 天清理期兜底 fork/resume 的引用寿命。

## 不变量体系

每个 workspace 包必须发布 `./invariant` companion 插件（空实现也要以注释解释为什么不需要，`verify-package-invariants` 机器校验）；检查对象**只许是权威事件流或可变数据，绝不许是服务/方法存在性**。loop 侧的断言逐字段比对请求与 `deriveMessages()` + `foldRequestHeader()`，失配即 `log-reconstruction desync`。验证采用 pre-commit 纯验证 + staging，失败不污染已提交日志。

## 与其他概念的关联

- 日志由 [[wiki/concepts/dsh-agent-loop|agent loop]] 写入——两者一体两面。
- "从成功会话提炼知识"类插件（[[raw/self-opt/skill/dsh-skill-evolve.md|dsh-skill-evolve]]）的全部原料就是这个日志：结构化、可重放、带来源标注。session-query 子系统提供 event_search/event_trace 等会话内省工具。
- 与 [[wiki/concepts/llm-wiki|LLM Wiki]] 的类比：dsh 的 session log 之于 agent，正如 raw/ 之于本 wiki——不可变事实层，视图全部可重建。

## 依据来源

- [[raw/2026-08-30-dsh-src-subsystem-session.md|subsystem session 文档剪藏]]、[[raw/2026-08-30-dsh-src-subsystem-compaction.md|compaction 文档剪藏]]
- 源码实读：`packages/core/session/src/{index,types,surface,repair,chunk-rows,invariant}.ts`、`packages/session/session-persistence-jsonl/src/{index,format}.ts`、`packages/compaction/compaction-basic/src/{index,region,summarizer}.ts`、`packages/spill/*`（AI 综合自实读，路径相对仓库根 @ cd5ef814）
