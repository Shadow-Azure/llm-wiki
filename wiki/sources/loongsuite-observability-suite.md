---
type: source
created: 2026-10-31
updated: 2026-10-31
sources:
  - raw/2026-10-31-loongsuite-announcement.md
  - raw/2026-10-31-loongsuite-pilot-blog.md
  - raw/2026-10-31-loongsuite-semconv-blog.md
  - raw/2026-10-31-loongsuite-zero-code-practice.md
---

# LoongSuite 可观测套件（4 来源快照）

**一句话**：阿里云 2025-08 开源的 AI 时代可观测数据采集套件——主机层 LoongCollector + 进程层 Java/Go/Python/JS 探针 + 端侧 Pilot + GenAI 语义规范底座，全部遵循 OpenTelemetry 生态。

## 来源清单

| # | 来源 | 日期 | 性质 |
|---|---|---|---|
| 1 | [[raw/2026-10-31-loongsuite-announcement|开源公告 + Go/Python README]] | 2025-08-25（README 2026-10-31 实测） | 官方博客 + 一手 README |
| 2 | [[raw/2026-10-31-loongsuite-pilot-blog|Pilot 开源公告]] | 2026-07-23 | 官方博客（设计抉择最细） |
| 3 | [[raw/2026-10-31-loongsuite-semconv-blog|GenAI 语义规范落地]] | 2026-06-04 | 官方博客（阿里&蚂蚁联合） |
| 4 | [[raw/2026-10-31-loongsuite-zero-code-practice|零代码实践 + Dify 实战]] | 2025-09-04 | 官方博客（原理最直白） |

## 核心要点

1. **问题域**：AI Agent 非确定性多轮 ReAct 决策让传统 Metrics/Log/Trace 失效（只见独立 HTTP 请求）；多轮交互的"Token 黑洞"；多 agent/框架数据碎片化；端侧（开发者本机）是可观测死区。见 [[wiki/concepts/genai-observability]]。
2. **组件矩阵**：LoongCollector（主机级，eBPF/无锁/水位队列）+ 语言探针（Java 字节码增强 / Go 编译期插桩 `otel go build` / Python monkey patch / JS 插件）+ Pilot（端侧编程 agent 采集器）+ semantic-conventions-genai（语义规范）。
3. **非侵入是贯穿性设计**：Java 运行时字节码、Python 运行时 monkey patch、Go 编译期 AST 注入（toolexec）、端侧 hook/SQLite/session 文件增量读取——"适配 Agent 而不是改造 Agent"。
4. **语义统一是杠杆**：LoongSuite GenAI SemConv 在 OTel GenAI 基础上新增 ENTRY/STEP span、`gen_ai.skill.*`、token 级推理观测（per_token 耗时/batch/Top-K logprobs）；GenAI Utils 让规范升级一处改、全部探针生效。
5. **Pilot 的五个设计抉择**（[[raw/2026-10-31-loongsuite-pilot-blog|来源 2]]）：ALL IN ONE 声明式平台（agents.d/*.json）；5 类采集基类 + Checkpoint 断点续采；AgentActivityEntry 统一事件格式；粒度控制 + 规则式自动脱敏（4 类凭据）；多目标并行 fan-out（JSONL/SLS/HTTP/OTLP）。

## 值得注意的细节/引文

- > "token 消耗与输出质量呈明显的非线性关系：大多数情况下，消耗最多 token 的会话输出质量最差。"（Pilot 博客实测发现，配三类 token 黑洞模式：循环试错/上下文膨胀/过度谨慎）
- > "This project has been contributed upstream to the OpenTelemetry community and successfully incubated the official opentelemetry-go-compile-instrumentation project."（loongsuite-go README）
- 蚂蚁 token 级推理观测产品"经历年尾大促……问题定界效率提升 10 倍"；BOS token 案例说明引擎/网关日志里某些异常 token 只显示为空字符串。
- OTel 社区参与：1000+ PR review、400+ PR、3 Maintainers / 5 Approvers / 1 Triager / 8 Members（公告博客自述）。
- Java Agent 已在百炼（Model Studio）大规模生产；Go Agent ⭐907（2026-10-31 实测）。
- Dify 实战：Python 侧加 label、Go 侧换编译命令，即可打通 dify-api ↔ dify-plugin-daemon 全链路（注意：演示含商业版能力，逐步开源中）。
- **与本库 DSH 研究的连接**：`loongsuite/dsh-plugin`（⭐31）把 DSH 每个 turn 变成 GenAI span 树（ENTRY→AGENT→STEP→LLM/TOOL）导出 OTLP；默认不捕获内容（隐私），与 [[wiki/concepts/dsh-plugin-capability-boundaries]] 记录的"上报打点=seam 默认关"一致。另有官方博客 "Tracing DeepSeek Harness with OpenTelemetry"（2026-08-20）未剪藏。

## 后续线索

- loongsuite-java / loongsuite-js / loongsuite-pilot 的 README 逐字剪藏（本次仅 announcement/pilot 两篇）。
- loongsuite-go benchmark 示例实测开销数字；官方博客 "Zero-Code Modification in 5 minutes"（2026-04-15）、"Tracing DeepSeek Harness with OpenTelemetry"（2026-08-20）、"AI Coding Measure Practices"（2026-07-16）、"AgentLoop Audit"（2026-08-17）待剪藏。
- OTel 上游 invoke_skill 提案（issue #86）进展；opentelemetry-go-compile-instrumentation 与本仓的代码同步关系。
- star 数据会漂移：907/232/139/88/26/31 为 2026-10-31 GitHub API 实测值。
