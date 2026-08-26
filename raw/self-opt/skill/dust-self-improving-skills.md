---
title: "Dust (Multiplayer AI)：Self-Improving Skills——用户对话数据驱动的技能自改进"
layer: skill
source_urls:
  - https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills
  - https://dust.tt/blog/introducing-self-improving-skills-reusable-ai-capabilities
  - https://docs.dust.tt/docs/changelog
accessed: 2026-08-26
local_pdf:
---

# Dust（Multiplayer AI）Self-Improving Skills

> 一句话：Dust 平台每晚批量分析用户使用某个 skill 的真实对话，从显式/隐式反馈信号中提炼规律，生成"最小可用 diff"形式的改进建议（含指令修改、工具调整、新增工具），由技能维护者逐条审批后才生效——把 skill 指令层（而非模型权重）变成可学习的对象。

## 是什么 / 背景

Dust（公司名义 Multiplayer AI）是企业级 agent 平台，skill 是其封装团队流程与知识的基本单元：指令 + 工具 + 知识源 + 示例 + 升级规则 + 格式偏好等。官方博客指出，skill 的第一版几乎从不是终版，而需要改进的地方"只有通过反复使用才会显现"（用户问模糊问题、工具返回意外结果、政策变化、团队发现"显而易见"的上下文从未被写进文档）。此前这个综合工作全靠 builder 手工完成：收集反馈、翻看对话、识别模式、决定怎么改。

Self-improving skills 于 2026-07-07 由官方博客官宣（"Introducing self-improving skills: reusable AI capabilities that learn from usage"），官方 changelog 在 2026-08-14 记录 "Self Improving Skills Capabilities added"。功能定位原文：

> "Self-improving skills is a feature that automatically analyzes how users make use of skills in their conversations, and surfaces targeted suggestions to improve the skills over time."（[官方文档](https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills)）

官方博客对其本质的定性值得记录："system prompt learning"（系统提示词学习）——"Nothing happens to the model's weights, and the skill doesn't rewrite itself. Rather, the learnable layer is the skill instructions itself, which stays explicit, inspectable, and in the hands of the people who own it." 官方强调 **"Self-improving does not mean self-mutating"**（自改进 ≠ 自动变异）："The system proposes, and the human decides."

## 机制：用户数据怎么流动

（依据 [官方用户文档](https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills) 与 [官宣博客](https://dust.tt/blog/introducing-self-improving-skills-reusable-ai-capabilities)）

**信号来源（显式 + 隐式）**
- 每晚批量分析使用过该 skill 的用户对话（"Every night, Dust analyzes user conversations that make use of skills"，走 LLM Batch Mode 异步批处理）。
- 显式信号：thumbs up/down 反馈、文字评论（"User feedback (thumbs up/down and written comments)"）。
- 隐式信号（文档原话三项）："a user correcting the agent's output"（用户修正 agent 输出）、"a tool that failed to be invoked when it should have been"（该触发的工具没触发）、"a conversation that went off track"（对话跑偏）。
- 处理的数据范围：对话转录（消息、工具调用、agent 回复）+ 用户反馈 + 当前 skill 配置（指令与工具）。

**分析方式**
- 先对单条对话生成"合成反馈"（synthetic feedback）：这里是否发生了有意义的事？是失败模式、缺失指令、缺上下文、用错工具，还是该问澄清问题没问？
- 再跨对话聚合到当前 skill 版本上——目标是发现**重复出现的**问题模式（"the same ambiguity trips people up repeatedly, a tool keeps getting skipped"），而不是追逐单次投诉。官方明确反对过拟合："overfitting a skill to every one-off failure creates contradictory directives that actually degrade outcomes"。

**生成什么改进**
- 输出为带证据的针对性建议，三类（文档原话）："better instructions, adjustments to existing tools, or new tools to add to the skill"——即 **指令修改 / 对现有工具的调整 / 建议新增工具**。"工具调整/新增工具"这一项是它区别于纯 prompt 优化产品的关键：用户数据可以回流为工具层变更（例如研究类 skill 学到更好的工具排序：文档源优先、Slack 仅用于核实近期决策）。
- 博客称学习单元是 diff："A diff is the unit of learning"——目标是最小可用 diff（"minimal useful diff"），小到可评审、具体到能解决观察到的问题、泛化到不针对单次对话过拟合。博客给了三个 diff 示例：支持 skill 学会先区分三种计费口径再升级；数据 skill 学会先澄清 revenue 指 booked ARR / recognized revenue / cash collected；研究 skill 学会调整文档/Slack 检索顺序。

**谁审批**
- skill 的 editors（维护者）：在 Skill Builder 右栏以语法高亮 diff 形式逐条审阅，Approve（立即应用）或 Decline（忽略）。"Nothing is applied to your skill without your explicit approval."

**怎么上线 / 关闭**
- Approve 即时生效写入 skill；无独立的自动回滚机制，但 diff 本身可审计、可由人再改回。
- 开关粒度两层：workspace 级（设置里整体关闭，关闭后不做对话分析、不产生建议）与 skill 级（每个 skill 独立 toggle）。
- 计费：按 credits pool 计费，同 programmatic usage；夜间批处理用 Anthropic/OpenAI/Google/Mistral 的异步 Batch API（比同步接口便宜 50%），折扣让利给用户——分析成本约为等价实时调用的一半。
- 隐私边界：不训练/微调任何模型；无跨 workspace 数据共享；对话只用已配置并批准的 LLM 提供商处理；对 ZDR 严格要求的企业可关闭 batch 模式改用流式（更贵但受 ZDR 保障）。

## 关键事实与数字（附出处）

| 事实 | 出处 |
|---|---|
| 功能官宣 2026-07-07 | [Dust 官方博客](https://dust.tt/blog/introducing-self-improving-skills-reusable-ai-capabilities)（publishedTime: 2026-07-07） |
| changelog "Self Improving Skills Capabilities added"（2026-08-14） | [docs.dust.tt/docs/changelog](https://docs.dust.tt/docs/changelog) |
| 每晚分析：LLM Batch Mode，批处理最长 24 小时出结果，价格 = 实时调用的 50% | [官方文档 Pricing 节](https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills) |
| 三类建议：指令 / 工具调整 / 新增工具 | 同上 How It Works 节 |
| 审批界面为语法高亮 diff，Approve/Decline 二选一 | 同上 Reviewing Suggestions 节 |
| "不训练任何模型"、"无跨 workspace 共享" | 同上 Privacy and Data Usage 节 |
| 定性为 "system prompt learning"、"A diff is the unit of learning"、"Self-improving does not mean self-mutating" | [官宣博客](https://dust.tt/blog/introducing-self-improving-skills-reusable-ai-capabilities) |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 每晚批量分析使用某 skill 的用户对话 | "Every night, Dust analyzes user conversations that make use of skills"（[docs](https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills)） | ✅ |
| 显式信号：thumbs up/down、文字评论 | "explicit signals (thumbs up/down feedback left by users)"；数据清单含 "User feedback (thumbs up/down and written comments)"（同上） | ✅ |
| 隐式信号：用户修正输出、工具该触发没触发、对话跑偏 | 原文三项逐一命中："a user correcting the agent's output, a tool that failed to be invoked when it should have been, a conversation that went off track"（同上） | ✅ |
| 生成带证据的改进建议：指令修改、工具调整、新工具 | "These can include better instructions, adjustments to existing tools, or new tools to add to the skill"（同上）；博客强调最小 diff + 证据评审 | ✅ |
| 工具调整/新增工具支撑"用户数据驱动工具更新"的说法 | 明确成立：工具层变更是建议的一等公民（同上 How It Works） | ✅ |
| 由技能维护者审批后以 diff 形式应用 | "The skill's editors review each suggestion"；"syntax-highlighted diffs"；Approve 即应用（同上） | ✅ |

结论：原 passage 对 Dust 的描述与官方文档/博客完全一致，且该案例确实是"用户数据驱动 AI Agent 自进化"在 skill 层产品化的最直接样本——尤其"工具调整/新增工具"直接支撑了"用户数据驱动工具更新"的产业叙事。

## 本地文件与延伸阅读

- 本地：`raw/self-opt/`（同目录下其余 skill/harness/model/tool 层 briefing）
- [官方用户文档：Self Improving Skills](https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills)（含 .md 直出版本：`.../self-improving-skills.md`）
- [官宣博客（2026-07-07）](https://dust.tt/blog/introducing-self-improving-skills-reusable-ai-capabilities)
- [Dust changelog](https://docs.dust.tt/docs/changelog)
- 背景语境：Dust 于 2026-05 宣布 4000 万美元 Series B，主打 "multiplayer AI"（[dust.tt/blog/series-b-multiplayer-ai](https://dust.tt/blog/series-b-multiplayer-ai)；[SiliconANGLE 报道](https://siliconangle.com/2026/05/18/multiplayer-ai-startup-dust-swipes-40m-funding-help-enterprises-move-beyond-isolated-ai-assistants/)）
