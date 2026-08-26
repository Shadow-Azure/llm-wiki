---
title: "CopilotKit Intelligence + AG-UI 协议"
layer: harness
source_urls:
  - https://www.copilotkit.ai/copilotkit-intelligence
  - https://www.copilotkit.ai/blog/copilotkit-enterprise-intelligence-platform
  - https://www.copilotkit.ai/ag-ui
  - https://docs.ag-ui.com/introduction
  - https://cloud.tencent.com.cn/developer/article/2709351
  - https://aws.amazon.com/blogs/machine-learning/build-generative-ui-for-ai-agents-on-amazon-bedrock-agentcore-with-the-ag-ui-protocol/
  - https://blogs.oracle.com/ai-and-datascience/announcing-ag-ui-integration-for-agent-spec
  - https://pydantic.dev/docs/ai/integrations/ui/ag-ui/
accessed: 2026-08-26
local_pdf:
---

# CopilotKit Intelligence + AG-UI 协议

> 一句话：CopilotKit 用自家主导的开放协议 AG-UI 把"用户与 Agent 协同界面上发生的一切"变成带类型的实时事件流，落进持久化 thread 层，再由 Intelligence 平台的 Automatic Learning（CLHF）把这些生产交互自动转化为按容器隔离的 prompt/技能级改进——不微调模型、自托管、全程可审计。

## 是什么 / 背景

- **CopilotKit** 是一家做全栈 agentic 应用 SDK 的公司（主体 Tawkit, Inc.），也是 **AG-UI（Agent–User Interaction Protocol）** 协议的发起者。官方定义：AG-UI 是 "an open, lightweight, event-based protocol that defines how agents, users, and applications stay in sync"（https://www.copilotkit.ai/ag-ui ）。
- **CopilotKit Intelligence** 是其企业级平台，四大支柱：**Rich Threads、Automatic Learning、Channels、Product Analytics**，全部建立在 "the same durable interaction data your agents already produce over AG-UI" 之上（https://www.copilotkit.ai/copilotkit-intelligence ，2026-08-26 抓取的当前版本）。
- 命名沿革：2026-05-14 官方博文发布时叫 **"CopilotKit Enterprise Intelligence Platform"**（https://www.copilotkit.ai/blog/copilotkit-enterprise-intelligence-platform ），当前产品页已更名为 **CopilotKit Intelligence**。当时 Self-Improvement 还是 "in active development" 的路线图支柱，本日抓取的产品页已将 Automatic Learning 列为正式能力（同一页面早前缓存版本仍标 "Coming Soon"，说明近期刚转正——引用时注意时点）。
- 库内关联：腾讯云开发者社区转载的中文总结《自学习智能体？又一个热点方向》（2026-07-14，源自微信公众号，https://cloud.tencent.com.cn/developer/article/2709351 ）转述的正是 CopilotKit 团队（CEO Markus Ecker）2026 年 7 月初在 X 上的分享（@mme_xyz，https://x.com/mme_xyz ；daily.dev 上有社区帖 "Self-Improving Agents that learn from users and agent traces"，2026-07-07，https://daily.dev/posts/self-improving-agents-that-learn-from-users-and-agent-traces-pls1gmmug ）。

## 机制：用户数据怎么流动

**信号来源**（官方产品页 "Every interaction is captured. In-chat & in-app."，https://www.copilotkit.ai/copilotkit-intelligence ）：
1. **In-app interactions** — "What users do in your app: clicks, edits, and navigation outside the chat"（聊天之外的点击、编辑、导航）。
2. **Agent-user interactions** — "The back-and-forth with the agent, plus feedback signals: approvals, edits, retries"（与 Agent 的来回交互 + 批准/编辑/重试等反馈信号）。
   官方博文补充："Every user action in production is a learning event for the agent: which answers they accept, which they edit, which they ignore."（https://www.copilotkit.ai/blog/copilotkit-enterprise-intelligence-platform ）
   腾讯云转载版本把信号源表述为"Agent 轨迹（每步问了什么、调了什么工具、在哪失败、返回了什么）+ 浏览器内用户活动（点击、编辑、批准、修正）"——"用另一个 Agent 分析轨迹找失败模式"的说法出自 Ecker 的 X 帖，官方产品页未如此表述。

**处理/提炼**：所有交互 "flows through AG-UI as typed events and lands in the thread layer"（博文原话）。学习机制名为 **CLHF（Continuous Learning from Human Feedback）**，属 in-context 强化学习：在运行时做 "prompt-level adjustments at runtime, per user and per context"，产品页另列 **Prompt Mutation**（"Automatic prompt augmentation based on recent interactions and outcomes"，见早前缓存版本）。博文的对照修辞："This is the loop Cursor used to build Composer. Every developer interaction was implicit training data."

**更新什么**（三层去向，官方页面可见两层半）：
- **上下文层**：运行时 prompt 级调整（CLHF，per user / per context）——官方明示 ✅
- **技能/Harness 层**："Self-improving skills - automatically adopted by your agents；New skills created from real usage"；**Skill recording**："Users show the agent a task once. It learns the skill."——官方明示 ✅
- **模型权重层**：仅提供 "**Export datasets to fine-tune custom models**"（导出微调数据集），并强调 "No Fine-Tuning Required"（无需微调管道）——即权重层是"导出供你自行微调"，不是平台内置能力 ⚠️
- 腾讯云文中"学习落在模型权重/Harness/上下文三层"的完整三分法未在官方页面检索到原文，与其最接近的官方表述是上述产品页能力清单；该 taxonomy 出自 Ecker 的 X 帖（"Procedural memory is what many of us include in skills or agents.md files…"，https://x.com/mme_xyz ）。

**验证与上线闭环**：
- **学习容器（Learning containers）**："Learning is scoped to containers you define: per user, per group of users, or per organization."（https://www.copilotkit.ai/copilotkit-intelligence ）
- **可审计**："Fully auditable — Every learned behavior traces back to its source."；企业安全：SOC 2 Type II、SSO/SAML、RBAC、audit logs。
- **数据主权**：自托管（Self-Hosted）在自家 Kubernetes，"Full data sovereignty""Air-gapped deployments supported""Bring your own database"；或 CopilotKit Cloud 全托管（99.9% uptime SLA available）。

## 关键事实与数字（附出处）

- 自托管集群要求：Kubernetes 1.28+、Helm 3.12+、Postgres 14+、Redis 7+（Valkey 兼容）、OIDC issuer（Keycloak/Okta/Azure AD/Auth0/Google Workspace）；license-gated Helm chart（https://www.copilotkit.ai/blog/copilotkit-enterprise-intelligence-platform ）。
- 2026-05 定价四档（同上博文）：Developer 免费（1 seat、200 threads、3-day retention、1GB）；Pro（最多 5 seats、5,000 threads、5-day retention、10GB）；Team（5 seats、25,000 threads、14-day retention、100GB）；Enterprise（unlimited threads、SLA、每周 5 小时工程支持）。
- 营销对比数字：产品页称不采用其方案则 "Pay Scale.ai or Mercor $30M and spend months collecting thousands of hours of labeling data"（https://www.copilotkit.ai/copilotkit-intelligence ）。
- AG-UI 生态采用（腾讯云文所列名单逐一可证）：AWS（Bedrock AgentCore 官方博客，https://aws.amazon.com/blogs/machine-learning/build-generative-ui-for-ai-agents-on-amazon-bedrock-agentcore-with-the-ag-ui-protocol/ ）；Oracle（2025-12-18 官方博客，https://blogs.oracle.com/ai-and-datascience/announcing-ag-ui-integration-for-agent-spec ）；Microsoft（Teams 成为 AG-UI client，Ecker 2026-06-18 帖，https://x.com/mme_xyz ）；Pydantic AI（官方文档，https://pydantic.dev/docs/ai/integrations/ui/ag-ui/ ）；CrewAI（2026-08-18 官方博客 "CrewAI is now fully AG-UI compatible"，https://www.copilotkit.ai/blog ）；LangChain/LangGraph、Mastra、LlamaIndex、AG2、Agno、GenKit 在发布数周内宣布集成（https://www.copilotkit.ai/blog/introducing-ag-ui-the-protocol-where-agents-meet-users ）。协议仓库：https://github.com/ag-ui-protocol/ag-ui ；规范文档：https://docs.ag-ui.com/introduction 。
- AG-UI 页面将 Copilot Cloud 的相关能力称为 "Support for self-improving agents through implicit human feedback (RLiHF)"（https://www.copilotkit.ai/ag-ui ）。
- Brex 案例（"观察分析师工作、把人工修正作为训练信号"）：仅见于 Ecker X 帖及 daily.dev 转述，官方文档/博客未检索到——引用时须标注为二手。

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 用 AG-UI 协议实时捕获用户与 Agent 协同的每个事件 | "an open, lightweight, event-based protocol…agents, users, and applications stay in sync"；"All of it flows through AG-UI as typed events and lands in the thread layer"（https://www.copilotkit.ai/ag-ui ；https://www.copilotkit.ai/blog/copilotkit-enterprise-intelligence-platform ） | ✅ |
| 学习明确落在三个层面：模型权重、Harness（循环、工具许可、行动前检查）、上下文（prompt 注入） | 官方可见：上下文层（CLHF 运行时 prompt 调整）与技能层（self-improving skills / skill recording）✅；权重层仅为"导出微调数据集"且官方强调 No Fine-Tuning Required；"权重/Harness/上下文"三分法及"循环、工具许可、行动前检查"的 Harness 定义未在官方页面找到，出自 Ecker X 帖（https://www.copilotkit.ai/copilotkit-intelligence ；https://x.com/mme_xyz ） | ⚠️ |
| 学习容器按用户/团队/应用划分；支持自托管和审计 | 官方三档为 "per user, per group of users, or per organization"——第三档是"组织"而非"应用"；自托管（K8s、air-gapped、SOC 2 Type II）与 "Fully auditable: Every learned behavior traces back to its source" 均属实（https://www.copilotkit.ai/copilotkit-intelligence ） | ⚠️（容器分档表述有出入；自托管/审计 ✅） |
| （腾讯云文）两类信号源：Agent 运行轨迹 + 用户协同界面活动（点击、编辑、批准、修正，尤其"用户把输出改成了什么"） | 官方现页两分法："In-app interactions"（clicks, edits, navigation）+ "Agent-user interactions"（approvals, edits, retries）；博文："which answers they accept, which they edit, which they ignore"。"用另一个 Agent 分析轨迹重写提示词/工具"出自 Ecker X 帖（https://www.copilotkit.ai/copilotkit-intelligence ；https://www.copilotkit.ai/blog/copilotkit-enterprise-intelligence-platform ） | ✅（框架一致，措辞来源为 X 帖） |
| （腾讯云文）三种学习类型：程序性/情景性/语义性 | 该记忆三分法出自 Ecker X 帖（"Procedural memory is what many of us include in skills or agents.md files"），官方产品页/文档未检索到该 taxonomy（https://x.com/mme_xyz ） | ⚠️ |
| （腾讯云文）已被 AWS、Google、Microsoft、Oracle、LangChain、Mastra、Pydantic AI、CrewAI、LlamaIndex 等采用 | AWS/Oracle/Microsoft/Pydantic/CrewAI 均有官方一手来源；LangChain、Mastra、LlamaIndex 见于发布期集成公告与报道（见"关键事实"一节各 URL） | ✅ |
| （腾讯云文）Brex 观察分析师、人工修正回流为训练信号 | 仅见 Ecker X 帖与 daily.dev 转述（https://daily.dev/posts/self-improving-agents-that-learn-from-users-and-agent-traces-pls1gmmug ），CopilotKit 官方页面未检索到 | ⚠️ |
| SOC 2 Type II、气隙部署、数据主权 | 产品页与博文原话一致（https://www.copilotkit.ai/copilotkit-intelligence ） | ✅ |

## 本地文件与延伸阅读

- 本地 PDF：无。
- 时点提醒：本 briefing 依据 2026-08-26 抓取的 copilotkit.ai 页面；该站产品页近期刚把 Self-Improvement 从 "Coming Soon" 转正为 Automatic Learning，且同日不同缓存版本内容不一致，后续复查以官方页为准。
- 延伸：AG-UI 规范 https://docs.ag-ui.com/introduction ；协议仓库 https://github.com/ag-ui-protocol/ag-ui ；自托管指南 https://docs.copilotkit.ai/premium/self-hosting ；AG-UI 发布博文 https://www.copilotkit.ai/blog/introducing-ag-ui-the-protocol-where-agents-meet-users ；MarkTechPost 对 Enterprise Intelligence Platform 的第三方报道 https://www.marktechpost.com/2026/05/06/copilotkit-introduces-enterprise-intelligence-platform-that-gives-agentic-applications-persistent-memory-across-sessions-and-devices/ 。
