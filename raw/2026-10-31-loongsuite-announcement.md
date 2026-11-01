---
type: clip
source:
  - "https://www.alibabacloud.com/blog/602489（Alibaba Cloud Open-Sources LoongSuite，2025-08-25）"
  - "https://github.com/alibaba/loongsuite-go README（GitHub API，2026-10-31 实测 ⭐907）"
  - "https://github.com/alibaba/loongsuite-python README（GitHub API，2026-10-31 实测 ⭐139）"
title: "阿里云正式开源 LoongSuite：AI 时代高性能低成本可观测数据采集套件（开源公告 + 各 Agent README 关键事实）"
author: Alibaba Cloud Native Community
accessed: 2026-10-31
via: web_fetch 博客全文 + GitHub API README 原文
clip_note: LoongSuite 品牌的开源头声明。发音 /lʊŋ swiːt/（"龙-甜"）。逐字引文以 > 标注，其余为结构化摘录。
---

# 一、为什么开源（博客论点）

- AI Agent 架构：感知层（NLP/语音/视频）→ 决策中心（大模型 + AI 网关 Higress 统一调度）→ 记忆机制 → 工具集成（MCP 标准化 + MCP 市场）→ 多智能体协作（A2A 协议）。
- Agent 部署形态多样（桌面 Cherry Studio/DeepChat、企业云原生、Serverless 函数计算）；运行期配套：Nacos（动态 Prompt/MCP 注册中心）、Higress（模型与 MCP Server 统一代理）、RocketMQ（异步任务）、Redis（状态存储）。
- 可观测性需求来源：Agent 非确定性决策 + 动态执行，异常会链式传导；多轮交互中间过程 token 消耗惊人，"agent 可能陷入无限循环，形成所谓 Token 黑洞"；每次改 prompt/换模型都需要"对 AI agent 做回归测试"，依赖执行期可观测数据。
- OTel 社区正在推 GenAI semantic convention（跨框架跨厂商统一数据规范），LoongSuite 在此背景下开源。

> "LoongSuite 的核心数据采集引擎有效结合了主机级探针与进程级插桩。进程级探针实现应用内细粒度可观测数据采集，主机探针实现高效灵活的数据处理与上报，并通过 eBPF 等技术实现进程外采集能力。"（博客原文意译）

# 二、四大组件（博客介绍）

## LoongCollector（主机级采集引擎）
- Logs/Metrics/Traces/Events/Profiles All-in-One 统一采集；内置实时日志采集、Prometheus 指标拉取、eBPF 无侵入监控。
- 性能稳定性：事件驱动架构、时间片调度、无锁（lock-free）设计；高/低水位反馈队列 + 持久化缓存 → 削峰容错、不丢数据、采集不间断（面向 AI 训练场景的稳定性要求）。
- 部署形态：agent 模式 / 集群模式；自动容器上下文发现、K8s 元数据关联、多租户隔离；ConfigServer 中心化管控大规模 agent + 配置动态下发。
- 数据处理：原始采集→结构化转换→过滤聚合→路由分发，模块化编排可扩展；SPL 查询语言 + 多语言插件双引擎驱动。

## LoongSuite Python Agent
- 基于 OpenTelemetry Python Agent 构建（定制发行版）；OTel GenAI 语义规范的最新实现之一。
- 动机：OTel 社区 GenAI 语义规范尚未完成，官方插件当时只支持 OpenAI，与中国流行的 AI 框架差距大 → LoongSuite 补齐国内框架支持并回馈社区。
- 已支持（博客时点）：AgentScope、Agno；计划陆续开源：Dify、LangChain、MCP Client 等。

## LoongSuite Go Agent
- 编译期插桩（compile-time instrumentation）：不改源码，`go build` 加 `otel` 前缀即可。
- 原理：在 Go 编译器 AST 分析阶段注入监控逻辑（span 创建、token 消耗统计等），经预定义 tracking rule 引擎自动注入；走完整 Go 编译流程，天然支持所有 Go 运行场景。
- 覆盖 20+ 核心模块（HTTP、gRPC、数据库连接等）；AI 框架已支持 LangChainGo、MCP Server（mark3labs/mcp-go）；Eino、Ollama 等陆续发布。
- 能捕获大模型调用的输入输出特征、token 消耗模式、多轮交互过程轨迹。

## LoongSuite Java Agent
- 基于 OpenTelemetry Java Instrumentation，字节码增强（运行时动态修改 bytecode），零业务代码修改。
- 覆盖 50+ 常见组件自动埋点：Servlet/Spring/Dubbo → Redis/Kafka/MySQL → JVM 自身指标；内置采样策略与数据聚合控制数据量。
- 已在百炼（Model Studio，大模型平台）大规模生产使用；OpenAI、DashScope 等模型 SDK 自动埋点开发中。

## 与 Spring AI Alibaba 的协同
- Spring AI 原生 OTel：自动追踪 LLM 调用/prompt/流式响应、Trace 上下文传播、指标导出（延迟/token/响应长度）、MDC 日志关联。
- Spring AI Alibaba + LoongSuite Java Agent 组合：字节码增强 Spring 框架/DB/HTTP 等通用组件，进一步降低接入成本。

# 三、未来规划（博客列的五条）
1. 覆盖主流 AI Agent 框架（Python: Dify/AgentScope/Agno/OpenAI Agent；Java: Spring AI Alibaba/JManus；Go: Eino/Langchain4go）。
2. 消除 MCP 与多智能体通信的观测盲区，解决"MCP token 黑洞"，覆盖 MCP 与 A2A 协议。
3. 联合 Spring AI Alibaba/AgentScope 开源可观测追踪与评测控制台（采集→存储→评测全生命周期）。
4. 端到端可观测：从端侧 agent 到模型内部全链路打通。
5. LoongCollector 通过 eBPF 支持 CPU/GPU profiling；与 SysOM 社区合作 AI 场景 profiling。

# 四、OTel 社区参与度（博客数字）
> "累计合并超过 1,000 个 PR Review、向社区提交超过 400 个 Pull Request；培育出 3 位 Maintainer、5 位 Approver、1 位 Triager、8 位 Member。"

参与领域：Semantic Conventions、Java Instrumentation、Go Instrumentation、Profiling；KubeCon / OTel Community Day 分享；发起亚太区交流专场。

# 五、loongsuite-go README 关键事实（2026-10-31 实读）

- 定位句："provides an automatic solution for Golang applications that want to leverage OpenTelemetry… No code changes are required in the target application, the instrumentation is done at compile time. Simply adding `otel` prefix to `go build`."
- 用法：`otel go build`（安装 otel 二进制后仅改构建命令）；要求 Go 1.25+；提供 Linux/macOS/Windows 预编译产物。
- 官方示例目录：demo（端到端 tracing+metrics）、zap 日志、benchmark（性能与开销测量）、sqlinject（自定义注入）、nethttp、kratos-demo、kafka-demo。
- 支持库清单（README 表格，60+）：含 Web 框架（gin/echo/fiber/hertz/kratos/kitex/dubbo-go/trpc-go）、中间件（kafka 三家/rocketmq/redis 三家/elasticsearch/clickhouse）、日志（zap/logrus/zerolog/slog）、AI 相关（eino v0.7.13+、langchaingo、mcp-go、mcp go-sdk、ollama、openai-go、go-openai、anthropic-sdk-go、deepseek-go、google-genai、trpc-agent-go、adk-go）。
- > "This project has been contributed upstream to the OpenTelemetry community and successfully incubated the official opentelemetry-go-compile-instrumentation project."
  （已贡献上游，孵化出 OTel 官方 opentelemetry-go-compile-instrumentation 项目）

# 六、loongsuite-python README 关键事实（2026-10-31 实读）

- 自述："Loongsuite Python Agent is a key component of LoongSuite, Alibaba's unified observability data collection suite… also a customized distribution of upstream OTel Python Agent, with enhanced support for popular AI agent framework. The implementation follows the latest GenAI semantic conventions."
- LoongSuite 官方组件清单（README 版）：LoongCollector（universal node agent：日志/Prometheus 指标/基于 eBPF 的网络与安全采集）+ Python/Go/Java 进程探针。
- LoongSuite 自有 instrumentation（instrumentation-loongsuite/）：AgentScope、Agno、Claude Agent SDK、QwenPaw、CrewAI、DashScope、Dify（in dev）、Google ADK、LangChain、LangGraph、LiteLLM、MCP Python SDK（in dev）、Mem0。
- 发行物：loongsuite-distro（loongsuite-instrument / loongsuite-bootstrap 命令）、loongsuite-otel-util-genai（GenAI 工具库，Python 导入命名空间仍为 opentelemetry.util.genai）、loongsuite-site-bootstrap。
- 另含大量上游 OTel instrumentation（django/flask/fastapi/grpc/httpx/kafka 等）。

# 后续线索
- loongsuite-java（⭐88）、loongsuite-js（⭐26，Claude Code/OpenClaw 的 JS 插桩）README 未逐字剪藏，需要时补。
- benchmark 示例（loongsuite-go/example/benchmark）可实测开销数字。
- 博客中"评测控制台"与"GPU profiling"两项规划后续开源状态待跟踪。
