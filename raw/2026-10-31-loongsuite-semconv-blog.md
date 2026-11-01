---
type: clip
source:
  - "https://www.alibabacloud.com/blog/alibaba-%26-ant-group-loongsuite-genai-observability-semantics-specification-from-unified-data-language-to-large-scale-implementation_603212（2026-06-04）"
  - "https://github.com/alibaba/loongsuite-semantic-conventions-genai README（GitHub API，2026-10-31 实测 ⭐19）"
title: "阿里&蚂蚁 LoongSuite GenAI 可观测语义规范：从统一数据语言到大规模落地（ENTRY/STEP/Skill/Token 级观测 + GenAI Utils）"
author: Alibaba Cloud Native Community
accessed: 2026-10-31
via: web_fetch 博客全文 + GitHub API README
clip_note: LoongSuite 语义层的一手说明：为什么 SemConv 是 OTel 的灵魂、阿里/蚂蚁新增了什么语义、token 级观测的实战效果（10 倍定界提速）。
---

# 一、SemConv 的定位（社区视角）

- OTel 的核心价值不是各语言自动探针（那是"战术"），而是通过 SemConv 建立统一的可观测数据语言（"哲学"）。
- OTel GenAI SemConv：2024 年初启动；因社区标准强调普适性与长期稳定、人力有限，迭代偏慢。
- 阿里系时间线：2025 年阿里云、阿里集团、蚂蚁集团可观测团队联合，基于 OTel GenAI 语义对内部场景未覆盖内容做语义建模并推动内部落地；2026 年与 OTel GenAI 主维护者沟通后，按社区建议先以 LoongSuite 品牌作为 OTel GenAI SemConv 的厂商增强标准开源，后续择机回馈上游。

统一语义的三层价值：统一数据语言解决口径不一（gen_ai.system / gen_ai.request.model / gen_ai.usage.input_tokens 等标准字段）；支撑性能/成本/质量/安全统一治理（分钟级跨 agent 定位、跨业务可比、真实轨迹积累评测数据集、合规审计）；降低接入成本促进基础设施复用。

# 二、新增语义 1：Entry / Step Span

问题：长任务 agent 单条 trace 含数百上千 span，难以观察调用链轨迹。

设计：
- **ENTRY span**：在 agent 调用入口创建，还原模型与用户的原始输入输出形成对话历史；保证下游任务拿到的是最原始客户请求，不被 system prompt/框架 prompt 干扰。
- **STEP span**：对每轮 ReAct 过程（"反思→工具调用→模型调用"循环）做层级表达；排障自顶向下——先定位 10 轮 ReAct 中哪一轮出问题，再深入该轮具体哪步。

| 属性 | 说明 | 状态 |
|---|---|---|
| ENTRY | AI 应用系统入口调用标识 | Development |
| STEP | ReAct 迭代步标识 | Development |

落地：OpenClaw、QwenPaw、Hermes Agent 等场景已实现。

# 三、新增语义 2：Skill 语义

问题背景（电商导购类 agent）：用户意图被理解后路由到对应 **Skill**（最小可复用业务功能单元，内部编排一组 LLM 调用与工具调用，如搜索商品/加购/退款）。Skill 既不是单个工具调用也不是完整 agent，OTel GenAI 只覆盖 Agent/LLM/Tool span，缺 Skill 层抽象。三个痛点：性能波动时无法归因到功能域（只见一堆 execute_tool/inference span）；无法算 Skill 粒度健康指标（P99 延迟/成功率/调用频次）；多 Skill 并发时 trace 归属混乱。

建模：`gen_ai.skill.name`（add_to_cart）、`gen_ai.skill.id`（skill_7d1af98c，区分灰度/A/B）、`gen_ai.skill.description`、`gen_ai.skill.version`（1.0.0）。现阶段挂在既有 execute_tool span 上快速落地；同时已向 OTel 社区提交独立 `invoke_skill` span 提案（issue #86）覆盖 Skill 从加载到执行完成的完整生命周期。

效果：可按功能域聚合分析——快速定位"哪个 Skill 错误率最高"、对比"新版本发布后延迟是否恶化"、度量"LLM 调用时长占 Skill 总时长比例"；同一套语义覆盖 OpenClaw、Langchain、Spring AI 等框架。

# 四、新增语义 3：Token 级推理观测

背景：蚂蚁 2025 上半年建成推理云服务全链路观测（客户端到 DPI 引擎多语言多协议 trace；与阿里云协作向 vLLM/SGLang/TensorRT-LLM 三大推理引擎贡献基础 DPI 引擎观测 trace）。但 request 级 trace 无法定位更深问题：
1. 性能异常：单请求慢常因个别 token 生成慢，而 token 慢大概率由其他请求并发干扰导致。
2. 精度异常：重复/答非所问/乱码往往从某个 token 开始异常并持续影响后续。

> "推理请求问题的定位与定界，必须有 Token 级可观测数据支撑。"

2025 下半年蚂蚁建成业界首个覆盖多推理引擎、支持 token 级深度 trace 的观测产品，观测：每个 token 的生成耗时与子阶段过程；同实例内多并发请求间慢 token 的相互影响；每个生成 token 背后的 Top-K 候选分布。

建模（关键属性）：
- 性能：`gen_ai.response.per_token_time_to_schedule`（每 token 进入迭代的 UNIX 时间戳，秒）、`gen_ai.response.per_token_time_to_generate`（退出迭代时间戳）、`gen_ai.iteration.per_token_batch_size`（token 所在迭代 batch 内请求总数）、`gen_ai.iteration.per_token_cumulative_count`（batch 内 token 总数）。
- 精度：`gen_ai.response.candidate.per_position_decoded_tokens` / `per_position_token_ids` / `per_position_logprobs`（每个生成位置的 Top-K 候选串/ID/logit，首位为实际选中）。

# 五、Token 级观测的实战效果

产品形态："引擎显微镜"——token 分析（高倍镜：单请求内部每 token 各步耗时 + Top-K 候选分布）+ 引擎并发剖析（广角镜：引擎内全部请求的并发竞争协作关系）。

> "产品发布后经历年尾大促，成功帮助引擎、SRE 和业务团队定位多个稳定性问题，问题定界效率提升 10 倍。"

案例 1（慢 token 定位跨请求资源干扰）：某请求 TPOT 达 54.77ms 超阈值；token 分析看到第 125 个 token 花了 6.8s；切到引擎并发剖析发现另一租户请求的 prefill（首 token 花了 6s+）打断了当前请求的 decode；结论与解法（PD 分离，prefill/decode 互不干扰）。
案例 2（答非所问根因）：医疗问题回了一个 LeetCode 题解；token 分析第一个 token 是 "begin_of_sentence"（BOS）——BOS 出现即后续与 prompt 完全无关（引擎日志/网关日志中它只显示为空字符串，不可见）；进一步定位为模型 bad case，解法是调整模型或等版本优化。

# 六、GenAI Utils：语义规范的工程化层

问题：每个框架的 instrumentation 都要重复实现"建 span、挂语义属性、记 metrics、发 events、管 context"，规范一升级（加字段/调结构）各库维护成本指数上升。

设计："分层解耦、统一收敛"——instrumentation 层只做数据抽取（hook/monkey-patch 拦截后填充 Invocation 数据对象，不直接碰 OTel API）；GenAI Utils（ExtendedTelemetryHandler）统一收敛 span 创建、属性挂载、metrics 记录、event 发送、context 管理；规范升级只改 Utils，所有下游 instrumentation 自动生效。

编程模型："填数据 + 交给 Handler"——`get_extended_telemetry_handler()` 取单例；每种操作有对应 Invocation 类与 handler 方法：LLMInvocation→handler.llm()、InvokeAgentInvocation→handler.invoke_agent()、CreateAgentInvocation、ExecuteToolInvocation→handler.execute_tool()、EmbeddingInvocation、RetrieveInvocation、RerankInvocation、MemoryInvocation；用 context manager 进出自动完成遥测输出，异常自动设 error.type 与错误状态。Handler 继承上游 OTel TelemetryHandler（保证同步不冲突），扩展 Agent/Tool/Embedding/Retrieve/Rerank/Memory 等操作类型。

Python 实现：loongsuite-otel-util-genai（导入命名空间 opentelemetry.util.genai）。基于 GenAI Utils 已实现：DashScope（Qwen）、Dify、AgentScope、Mem0、MCP、Agno、Google ADK、LangChain。

# 七、README 补充（semconv 仓库）
- 定位："an open-source GenAI semantic conventions repository from Alibaba… enhanced Generative AI observability standards, building upon OpenTelemetry GenAI Semantic Conventions."
- 特点：GenAI 专用、生产检验（源自阿里内部 AI 基础设施实战）、OTel 兼容、社区共建。YAML model 生成 Markdown docs（OTel SemConv 工具链）。

# 后续线索
- OTel 上游提案 issue #86（invoke_skill span）进展待跟踪。
- vLLM/SGLang/TensorRT-LLM 三引擎的 DPI 观测 trace 贡献具体形态未剪藏。
- "Ant 全链路观测系统"细节在蚂蚁侧文章，未获取。
