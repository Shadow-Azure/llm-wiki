---
title: "阿里云 AgentLoop"
layer: harness
source_urls:
  - https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop
  - https://www.alibabacloud.com/help/en/agentloop/latest/core-concept
  - https://www.alibabacloud.com/help/en/agentloop/latest/experience-library
  - https://www.alibabacloud.com/blog/what-is-the-newly-released-agentloop-by-alibaba-cloud_603378
  - https://help.aliyun.com/zh/document_detail/3033860.html
  - https://help.aliyun.com/zh/document_detail/3042001.html
accessed: 2026-08-26
local_pdf:
---

# 阿里云 AgentLoop

> 一句话：阿里云把生产环境 Agent 产生的运行数据（trace）经管道清洗成 trajectory，沉淀为数据集与"经验（Experience）"两类资产，再通过评估、实验、Prompt/Skill 调优和经验注入反馈回 Agent，形成"越用越聪明、无需重训模型"的企业级自进化数据飞轮。

## 是什么 / 背景

- 官方定义（英文文档）："AgentLoop is a one-stop, self-evolving platform from Alibaba Cloud for enterprise-grade agents"——智能体的**一站式自进化平台**（https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop ）。中文文档同义："智能体的一站式自进化平台，提供 Agent 全栈观测与审计、Agent 评估与实验、Agent 资产管理与持续优化等核心能力"（https://help.aliyun.com/zh/document_detail/3033860.html ）。
- 官方博客明确其定位："it is neither an Agent development framework nor a model training platform, but an engineering platform that helps enterprises upgrade their Agents from 'usable' to 'exceptional'"（https://www.alibabacloud.com/blog/what-is-the-newly-released-agentloop-by-alibaba-cloud_603378 ）。
- 方法论前提是 "Agent = Model + Harness" 这一行业共识（同上博客），优化对象是模型之外的全部工程层。
- 官方博客还给出 **MVP 五环**叙述：①观测与审计 → ②轨迹分析 → ③性能评估 → ④实验回测 → ⑤持续优化（与文档的"数据飞轮五阶段"是同一闭环的两种讲法）。
- 类比定位：AgentSpace 类似 LangSmith / Arize / W&B Weave 的 Project，"but with stronger multi-tenant isolation and enterprise-grade access control"（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ）。
- 文档时点：核心概念页 "Updated at: 2026-07-24"，经验库页 "Updated at: 2026-07-27"。

## 机制：用户数据怎么流动

**信号来源（采集）**：通过阿里云自研探针、开源探针、OpenTelemetry 标准协议或 Python/Java SDK，**非侵入**采集生产环境 Agent 运行数据——一条 **trace** "records every step of a single agent request: user input, model inference, tool calls, retrieval-augmented generation (RAG), memory reads, and the final response"（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ）。兼容 Dify、LangChain/LangGraph、AgentScope、OpenClaw、Hermes、LlamaIndex、Claude Agent SDK，以及 Qoder、Claude Code、Codex、Cursor 等 CLI（https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop ）。

**处理/提炼**：**Pipeline** 把原始 trace 清洗、结构化为 **trajectory**（一次完整 Agent 执行的结构化记录，采用 ATIF——Agent Trajectory Interchange Format；"the standard unit of analysis across the platform"）。一条 trajectory 采集一次、复用四处：observability、audit、evaluation、optimization。trajectory 再沉淀为两类数据资产（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ）：
- **Dataset**：结构化、可查询的运行数据集合，支持 CRUD、灵活 schema、向量搜索——把"只读日志"升级为"可管理资产"。
- **Experience（经验）**："Reusable knowledge distilled from trajectories. An experience captures a proven pattern, such as an effective tool-calling sequence or an optimized prompt strategy"——官方特别注明此概念为 AgentLoop 独有，区别于强化学习的 "experience replay"。

**更新什么（驱动改进）**：数据资产经四条路径反馈（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ）：
1. **评估**：Agent-as-a-Judge（评估器本身是能用工具、多步推理的 Agent，官方称 20+ 预置评估器），低分样本自动回流 baseline 集；
2. **实验**：Playground 在线对比实验 + CI/CD 基线回归；
3. **Prompt 与 Skill 调优**：对 Agent 资产（prompt/skill）做多维调优，资产像代码一样版本管理、金丝雀发布；
4. **运行时上下文优化**：**经验库按需召回并动态注入**——"Users can automatically extract successful patterns from high-quality Trajectories to build reusable experience snippets and dynamically inject them into the Agent's context"（https://www.alibabacloud.com/blog/what-is-the-newly-released-agentloop-by-alibaba-cloud_603378 ）。

**验证与上线闭环**：评估结果用于"版本质量门禁"——A/B 实验量化新版在 accuracy/latency/cost 上的差异，"only changes that pass regression testing are released to the production environment"（https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop ）。记忆+经验库的组合构成不经重训的自进化：官方原话 "achieving a self-evolving effect of 'getting smarter with use' without needing to retrain the model"。经验库自身也是飞轮："mine, use, mine again, and use again"，且 "does not depend on modifying model weights"（https://www.alibabacloud.com/help/en/agentloop/latest/experience-library ）。

## 关键事实与数字（附出处）

以下数字均原样引自官方页面（https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop 及官方博客）：
- 定位痛点：Agent 多步推理导致质量回退时 "it takes an average of over two hours to locate the fault"；异常 token 消耗 "more than 10 times the off-peak rate"。
- 审计："100% action traceability"，每次工具调用与决策分支入防篡改证据链。
- Pipeline 产能："processing thousands of trajectory data rows per execution, with an average duration in seconds and a success rate close to 100%"，按小时级滚动窗口执行（官方博客）。
- 数据处理："saving over 90% of manual data processing effort"。
- 评估器："20+ out-of-the-box evaluator Agents"（官方博客）。
- 平台限制（英文 what-is 页 Limitations 表）：AgentSpace "The default maximum is 50"（单账号）；Trace 保留 "retained for 30 days by default"；评估并发 "default evaluation concurrency limit for a single account is 100"。
- 成熟度标注：实验功能 "scheduled for official release before June 30, 2026"（what-is 页）；经验库（experience base）在 what-is 页标注 "in private preview"。
- 上手路径官方口径："create an AgentSpace in one minute, complete data ingestion in five minutes, finish your first evaluation in one hour, and achieve your first round of optimization within a day"。
- 记忆策略四型：Facts、Episodic、Summary、Custom（what-is 页 Memory 小节）。
- 文档入口：英文 https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop ；中文产品文档首页 https://help.aliyun.com/zh/product/3033820.html ，"什么是AgentLoop" https://help.aliyun.com/zh/document_detail/3033860.html ，"核心概念" https://help.aliyun.com/zh/document_detail/3042001.html ，"智能体空间" https://help.aliyun.com/zh/document_detail/3033829.html 。注意：中文 URL 为 document_detail 数字路径，**不存在** help.aliyun.com/zh/agentloop/ 这样的路径（实测 404）。

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 企业级"智能体自进化平台" | "a one-stop, self-evolving platform from Alibaba Cloud for enterprise-grade agents"；中文"智能体的一站式自进化平台"（https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop ；https://help.aliyun.com/zh/document_detail/3033860.html ） | ✅ |
| 五阶段数据飞轮：采集 traces → 清洗成 trajectories → 沉淀为数据集和 Experience → 驱动评估/实验/Prompt-Skill 调优 → 反馈回 Agent | 核心概念页五阶段逐条对应：Collect traces / Process traces into trajectories / Build data assets (Dataset + Experience) / Drive improvement ("evaluation, experimentation, prompt and skill tuning, and runtime context optimization") / Repeat（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ；中文 https://help.aliyun.com/zh/document_detail/3042001.html ） | ✅ |
| Experience = 经验：有效的工具调用序列、优化的 prompt 策略 | 官方原话 "an effective tool-calling sequence or an optimized prompt strategy"（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ） | ✅ |
| 明确支持"记忆库与经验库上下文自进化" | "Closed-loop optimization with context engineering"小节：memory + experience base 使 Agent "automatically retrieve and inject relevant context into the runtime during each interaction, achieving a self-evolving effect of 'getting smarter with use' without needing to retrain the model"。注意：experience base 标注 private preview（https://www.alibabacloud.com/help/en/agentloop/latest/what-is-agentloop ；经验库专页 https://www.alibabacloud.com/help/en/agentloop/latest/experience-library ） | ✅（经验库尚在私测，宜加时点注） |
| 有 AgentSpace 多租户 | "AgentSpace… the fundamental unit for enterprise multi-tenant governance"，含资源隔离/访问控制/集中管理；单账号默认上限 50 个；中文"智能体空间…用于资源隔离、权限控制和计费归属"（https://www.alibabacloud.com/help/en/agentloop/latest/core-concept ；https://help.aliyun.com/zh/document_detail/3033829.html ） | ✅ |
| 中文文档在 aliyun.com/help/agentloop，英文在 alibabacloud.com/help/en/agentloop/latest/core-concept | 英文路径属实；中文实际为 help.aliyun.com/zh/document_detail/3042001.html（核心概念）等产品文档页，help.aliyun.com/zh/agentloop/ 实测 404（见"关键事实"一节各 URL） | ⚠️（中文 URL 结构与声明不符，但中文文档确实存在） |

## 本地文件与延伸阅读

- 本地 PDF：无。
- 延伸：官方博客（MVP 五环方法论全文）https://www.alibabacloud.com/blog/what-is-the-newly-released-agentloop-by-alibaba-cloud_603378 ；经验库使用指南（文档导航中有 "Experience library user guide" 子页）https://www.alibabacloud.com/help/en/agentloop/latest/experience-library ；同博客系列另有 SkillClaw × Nacos 自进化闭环 https://www.alibabacloud.com/blog/skillclaw-%25C3%2597-nacos-autonomic-evolutionary-closed-loop-from-an-agent-session-to-a-governable-skill-registry_603219 与 Hermes Agent 自改进源码分析 https://www.alibabacloud.com/blog/deep-dive-into-source-code-how-hermes-agent-achieves-self-improving_603216 ，可作 harness 层自进化的旁证。
