---
type: entity
created: 2026-10-31
updated: 2026-10-31
sources:
  - raw/2026-10-31-loongsuite-announcement.md
  - raw/2026-10-31-loongsuite-pilot-blog.md
  - raw/2026-10-31-loongsuite-semconv-blog.md
  - raw/2026-10-31-loongsuite-zero-code-practice.md
---

# LoongSuite

**身份一句话**：阿里云 2025-08-25 开源的 AI 时代可观测数据采集套件（发音 /lʊŋ swiːt/，"龙-甜"），以 OpenTelemetry 为底座，覆盖主机层、进程层、端侧三层采集 + GenAI 语义规范。

## 背景

- 由阿里云可观测团队主导，阿里集团与蚂蚁集团联合参与语义规范建设；依托其在 OTel 社区的深度参与（1000+ PR review、400+ PR、3 Maintainers 等，公告博客自述）。
- 品牌核心引擎 **LoongCollector** 前身为阿里云 SLS 采集体系（其本身是独立开源仓 alibaba/loongcollector，本次未单独剪藏）。
- 开源策略：部分能力先商业版（ARMS）后开源（zero-code 实践博客自注）。

## 组件矩阵（2026-10-31 实测 star）

| 层 | 组件 | 仓 | ⭐ | 技术路线 |
|---|---|---|---|---|
| 主机 | LoongCollector | alibaba/loongcollector | 未实测 | eBPF + 无锁管道 + 水位队列，All-in-One |
| 进程 | Java Agent | alibaba/loongsuite-java | 88 | 运行时字节码增强，50+ 组件埋点 |
| 进程 | Go Agent | alibaba/loongsuite-go | 907 | 编译期插桩（`otel go build`，toolexec+AST） |
| 进程 | Python Agent | alibaba/loongsuite-python | 139 | monkey patch 定制发行版，13+ GenAI 框架 |
| 进程 | JS 插件 | alibaba/loongsuite-js | 26 | Claude Code/OpenClaw 零改动插桩 |
| 端侧 | Pilot | alibaba/loongsuite-pilot | 232 | 编程 agent 本机采集器（hook/SQLite/session 五基类） |
| 语义 | GenAI SemConv | alibaba/loongsuite-semantic-conventions-genai | 19 | OTel GenAI 厂商增强（ENTRY/STEP/Skill/token 级） |
| DSH | dsh-plugin | loongsuite/dsh-plugin | 31 | DSH turn → GenAI span 树 → OTLP |

## 与本库主题的关联

- **dsh-plugin 是 LoongSuite 与 DeepSeek Harness 的官方对接**：观测 DSH 原生 session/agent loop/LLM stream/tool 生命周期，导出标准 OTLP；默认关闭内容捕获——与 [[wiki/concepts/dsh-plugin-capability-boundaries]] 的"上报打点=seam 默认关"互证。见 [[wiki/entities/deepseek-harness]]。
- Go Agent 已孵化 OTel 官方项目 opentelemetry-go-compile-instrumentation（"贡献上游"路线的实例）。
- Python Agent 覆盖的 AgentScope/QwenPaw 等阿里系框架，与 [[wiki/concepts/ai-video-assistant-landscape]] 一类"生态观测"议题同构：大厂用基础设施卡位生态。

## 相关页面

- [[wiki/sources/loongsuite-observability-suite|来源快照页]]（本实体页的依据）
- [[wiki/concepts/genai-observability|GenAI 可观测性]]（问题域与方法论）
- [[wiki/concepts/dsh-plugin-capability-boundaries]]、[[wiki/entities/deepseek-harness]]

*库外常识性背景（SLS/ARMS 产品关系、团队沿革）待补充库内来源。*
