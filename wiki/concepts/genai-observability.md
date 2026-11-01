---
type: concept
created: 2026-10-31
updated: 2026-10-31
sources:
  - raw/2026-10-31-loongsuite-announcement.md
  - raw/2026-10-31-loongsuite-pilot-blog.md
  - raw/2026-10-31-loongsuite-semconv-blog.md
  - raw/2026-10-31-loongsuite-zero-code-practice.md
---

# GenAI 可观测性（AI Agent Observability）

**定义**：让 AI Agent 的多轮决策过程（模型调用、工具调用、token 消耗）变得可采集、可还原、可审计的数据基础设施，核心是"非侵入采集 + 统一语义"两条腿。LoongSuite 是该概念目前最完整的开源落地之一。

## 关键方面

### 1. 为什么传统可观测失效
- Agent 一次任务含 10+ 轮 ReAct 循环（模型调用→工具选择→结果反思），传统 Metrics/Log/Trace 只见独立 HTTP 请求，无法还原分层决策。
- **Token 黑洞**：多轮交互中间过程 token 消耗惊人甚至死循环；Pilot 博客实证三类模式：循环试错、上下文膨胀、过度谨慎，并发现"最贵会话往往质量最差"的非线性关系。
- 数据碎片化：每家 agent 格式/存储/粒度不同；端侧（本机 IDE 历史、SQLite、session 日志）是传统方案死区。

### 2. 非侵入采集谱系（按侵入性从低到高）
| 语言/场景 | 机制 | 时机 |
|---|---|---|
| eBPF（LoongCollector） | 内核态进程外采集 | 运行时，零感知 |
| Go | toolexec 中间层改 AST，`otel go build` | 编译期 |
| Java | 字节码增强 | 运行时类加载 |
| Python | monkey patch 替换函数引用 | 运行时初始化 |
| 端侧闭源 agent | hook 回调 / SQLite rowid 游标 / session 文件增量 + Checkpoint | 进程外旁路 |

端侧原则（Pilot）："适配 Agent 而不是改造 Agent"——闭源产品不可改运行时，抽象 5 类采集基类（Hook/IDE/SQLite/Session/CLI），新 agent 只写格式转换。

### 3. 统一语义是杠杆（SemConv）
- OTel 的灵魂是语义规范而非探针（社区观点，[[raw/2026-10-31-loongsuite-semconv-blog|来源 3]]）：字段统一后，同一 SQL/同一 dashboard 吃遍所有 agent 与框架。
- LoongSuite 增量：ENTRY/STEP span（解决单 trace 数百上千 span 难读；先定位哪轮 ReAct 再定位哪步）；`gen_ai.skill.*`（业务功能域归因，缺它则 Skill 粒度 P99/成功率不可算）；token 级推理观测（per_token 调度/生成时间戳、batch 负载、Top-K 候选 logprobs——把推理引擎从黑盒拆到 token 粒度）。
- 工程化：GenAI Utils 收敛样板代码——instrumentation 层只抽数据，span/metrics/events 全由 Handler 统一产出；规范升级改一处、全部探针生效。

### 4. 观测数据的四个消费方向（Pilot 提法）
ROI 度量（任务完成率/token 效率比/人机协作比/自修正率）；跨 agent 选型（同一任务给 Claude Code/Cursor/Qoder 的行为差异实测）；成本治理（token 黑洞识别）；安全审计（六层体系：管理视图→态势→主体定位→泄露链路→实体调查→会话回放；prompt injection 使恶意操作在日志里与正常调用几乎无差别）。

## 与其他概念的关联

- [[wiki/concepts/dsh-plugin-capability-boundaries]]：dsh 的"上报打点=seam 默认关"与 dsh-plugin 默认不捕获内容互证——可观测与隐私的边界在各家都是显式设计。
- [[wiki/concepts/dsh-agent-loop]]、[[wiki/concepts/dsh-session-log]]：dsh-plugin 的 span 树（ENTRY→AGENT→STEP→LLM/TOOL）正是对 dsh turn/step 状态机的 OTel 投影。
- [[wiki/concepts/llm-wiki]]：同属"让黑盒过程可审计"的家族——wiki 审计 LLM 的知识产出，可观测审计 LLM 的执行过程。

## 已知争议或矛盾

- 厂商增强 vs 上游标准：LoongSuite SemConv 是 OTel GenAI SemConv 的厂商扩展（社区建议先开源在厂商品牌下、择机回馈上游）——标准话语权路径的典型样本；invoke_skill 提案尚在 OTel issue #86。
- "高性能低成本"为官方叙事，公开材料未给出探针开销的系统基准（go 仓有 benchmark 示例但数字未公布于 README）；AI 推断：待第三方实测验证。

## 依据来源

[[wiki/sources/loongsuite-observability-suite|LoongSuite 可观测套件（4 来源快照）]]
