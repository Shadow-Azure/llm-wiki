---
title: Hermes Agent + GEPA 进化引擎（Nous Research 开源）
layer: harness
source_urls:
  - https://github.com/NousResearch/hermes-agent
  - https://github.com/NousResearch/hermes-agent-self-evolution
  - https://arxiv.org/abs/2507.19457
  - https://www.huaweicloud.com/intl/zh-cn/solution/implementations/deploying-hermes-agent.html
  - https://juejin.cn/post/7657560105181085747
  - https://github.com/gepa-ai/gepa
accessed: 2026-08-27
local_pdf: raw/self-opt/harness/gepa-2507.19457.pdf
---

# Hermes Agent + GEPA（Nous Research）

> 一句话：Nous Research 开源的 MIT 协议"自我改进 AI Agent"，内置学习闭环（经验→技能、使用中改进、跨会话用户建模），另有一个 self-evolution 子项目用 **GEPA（Genetic-Pareto 反射式提示进化，ICLR 2026 Oral）** 对 SKILL.md/工具描述/系统提示词做"进化-评测-门控-PR"闭环优化；华为云提供一键部署方案。

## 是什么 / 背景

- **Hermes Agent**：`NousResearch/hermes-agent`，MIT 协议。官方 README 定位："The self-improving AI agent built by Nous Research. It's the only agent with a built-in learning loop — it creates skills from experience, improves them during use, nudges itself to persist knowledge, searches its own past conversations, and builds a deepening model of who you are across sessions."（自建技能、使用中改进、自持记忆、FTS5 会话搜索 + LLM 摘要、Honcho dialectic 用户建模；兼容 agentskills.io 开放标准）。支持 $5 VPS 到 GPU 集群、Telegram/Discord/Slack 等多入口、任意模型后端。来源：https://github.com/NousResearch/hermes-agent
- **GEPA**：arXiv:2507.19457，"GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning"，Lakshya A Agrawal 等 17 位作者（Omar Khattab、Matei Zaharia、Ion Stoica、Dan Klein 等），v1 2025-07-25，v2 2026-02-14，**ICLR 2026 Oral**，代码 MIT（gepa-ai/gepa）。来源：https://arxiv.org/abs/2507.19457
- **Hermes Agent Self-Evolution**：`NousResearch/hermes-agent-self-evolution`——用 DSPy + GEPA 自动进化 Hermes 的 skills、工具描述、系统提示词与代码的独立子项目。来源：https://github.com/NousResearch/hermes-agent-self-evolution
- **华为云部署方案**：解决方案实践页《Hermes Agent，越用越聪明的智能体》（页面发布时间 2026-07-17），基于 Flexus 云服务器 X 实例私有化部署，预估费用 2 USD、部署时长 5 分钟。来源：https://www.huaweicloud.com/intl/zh-cn/solution/implementations/deploying-hermes-agent.html

## 机制：用户数据怎么流动

**① Hermes 主体的内置学习闭环（harness 层）**
README 表格 "A closed learning loop"：Agent 自管理记忆并周期性自我提醒沉淀知识；复杂任务后自主创建技能（skills）；**技能在使用中自我改进**；FTS5 全文检索自己的历史会话并用 LLM 摘要做跨会话回忆；Honcho 做用户认知建模（"builds a deepening model of who you are across sessions"）。另有研究向能力：批量轨迹生成 + 轨迹压缩（"trajectory compression for training the next generation of tool-calling models"——即把用户使用轨迹反哺下一代模型训练的接口）。
华为云页面口径：**"GEPA系统进化引擎，'执行-评估-优化-学习'闭环自动迭代Skills，在跨会话中持续完善用户认知智能体"**；配套**三层记忆**："通过用户记忆记录用户偏好实现上下文高度连贯，通过工作记忆常驻关键信息与经验总结，通过长期记忆进行全文检索，跨会话实现记忆不丢失。"

**② GEPA 论文本身的方法层（Genetic-Pareto 反射式提示进化）**
GEPA 是"任何含 LLM prompt 的系统"的优化器：采样轨迹（推理、工具调用、工具输出）→ 用自然语言**反思**诊断为什么失败（不只看到失败）→ 提出并测试 prompt 更新 → 在自己尝试的 **Pareto 前沿**上合并互补经验（按指标×成本双维选择候选）。关键数字（论文摘要）：六项任务上平均超 GRPO 6%、最高 20%，rollout 少至 **35×**；超主流提示优化器 MIPROv2 逾 10%（AIME-2025 +12% 准确率）。卖点：无需 GPU 训练、纯 API 文本变异。

**③ self-evolution 子项目的工程化闭环（skill 层的自动进化管道）**
README 流程：读当前 skill/prompt/tool → 生成评测数据集（`--eval-source synthetic` 或 **`--eval-source sessiondb`——直接用 Claude Code / Copilot / Hermes 的真实会话历史当评测集，这是"用户数据当进化燃料"的显式接口**）→ GEPA 优化器读执行轨迹提出候选变体 → 评测 + **约束门控**：①全测试套件 100% 通过 ②尺寸限制（SKILL.md ≤15KB、工具描述 ≤500 字符）③缓存兼容（会话中途不改）④语义保持 ⑤**一切改动走人工 PR 评审，绝不直接 commit** → 最优变体 PR 回 hermes-agent。
分阶段状态：Phase 1 SKILL.md 已实现；Phase 2 工具描述 / Phase 3 系统提示词分段（DSPy+GEPA）与 Phase 4 工具实现代码（Darwinian Evolver，外部 AGPL CLI）、Phase 5 持续改进流水线为规划中。每次优化运行成本约 $2–10。

## 关键事实与数字（附出处）

| 事实/数字 | 出处 |
|---|---|
| Hermes Agent：MIT、Nous Research 出品、内置学习闭环五要素 | https://github.com/NousResearch/hermes-agent README |
| `hermes claw migrate` 支持从 OpenClaw 迁移（记忆/技能/密钥）——与 [[raw/self-opt/skill/openclaw-autoskill.md|OpenClaw]] 同生态 | 同上 README "Migrating from OpenClaw" |
| GEPA：ICLR 2026 Oral；超 GRPO 平均 6%/最高 20%，rollout 少 35×；超 MIPROv2 10%+（AIME-2025 +12%） | https://arxiv.org/abs/2507.19457 摘要 |
| GEPA 机制三要素：自然语言反思、遗传式 prompt 变异、Pareto 候选选择 | 论文摘要 + https://github.com/gepa-ai/gepa |
| self-evolution：五阶段计划（Phase 1 SKILL.md 已实现）、五道 guardrail、$2-10/次、无 GPU | https://github.com/NousResearch/hermes-agent-self-evolution README |
| 华为云方案页："执行-评估-优化-学习"闭环、三层记忆（用户/工作/长期）、Flexus X 实例、2 USD、5 分钟、发布 2026-07-17 | https://www.huaweicloud.com/intl/zh-cn/solution/implementations/deploying-hermes-agent.html |
| 掘金博主系列（"模块十六"）声称：GEPA 四阶段管线（轨迹收集→目标提取→模式分析→基因编码）从 47,382 条轨迹提炼 1,247 粒"进化基因"（524 策略/386 边界/337 优化，压缩 38×），30 天报告含 Python 任务成功率 71%→89% 等 | https://juejin.cn/post/7657560105181085747 （2026-07-02 发布）——**该博客把 GEPA 展开为 "Goal Extraction & Pattern Analysis"/"Generative Evolutionary Prompt Adaptation"，与论文官方缩写 Genetic-Pareto 不符，数字与 Schema 均为博主自构叙事，官方仓库/论文中不存在"进化基因（Evolution Gene）"概念** |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| Hermes Agent 由 Nous Research 开源 | 官方仓库 "Built by Nous Research"，MIT | ✅ |
| 华为云提供部署方案 | 解决方案实践页（Flexus X、2 USD、5 分钟，2026-07-17 发布）。注意：与 [[raw/self-opt/skill/huawei-skill-patch.md|华为云三段式]] 是华为云技术栈两条不同触点（部署方案页 ≠ 三段式技术文章） | ✅ |
| GEPA 进化引擎"执行-评估-优化-学习"闭环自动迭代 Skills | 华为云页面原话逐字对应；self-evolution 仓库的实现是"进化→评测→门控→PR"闭环，语义一致 | ✅ |
| 配套用户记忆/工作记忆/长期记忆三层结构 | 华为云页面原话；官方 README 提"agent-curated memory / session search / user modeling"但未逐层命名 | ✅（三层表述出自华为云口径） |
| GEPA 从**每条执行轨迹**自动提炼"进化基因" | **与官方不符**：GEPA 论文机制是"采样轨迹→自然语言反思→prompt 变异→Pareto 选择"，产物是优化后的提示词/技能文本，论文与两个官方仓库均无"从每条轨迹提炼基因"的表述 | ⚠️ |
| 有博主用 4.7 万条执行记录提炼出 1,247 粒基因 | 博主（掘金"Hermes 深度解析"系列）确实如此声称（47,382→1,247，38× 压缩），但其对 GEPA 的缩写展开、四阶段管线、Evolution Gene Schema 均为自构内容，与论文/官方仓库对不上，可信度存疑（更像演示性叙事而非官方数据） | ⚠️（博客存在且如此声称；内容未获官方佐证） |
| 跨会话持续完善"用户认知智能体" | 华为云页面原话；官方 README "builds a deepening model of who you are across sessions" | ✅ |

## 本地文件与延伸阅读

- 本地 PDF：`raw/self-opt/harness/gepa-2507.19457.pdf`（arXiv:2507.19457v2，2.97MB，%PDF-1.7 校验通过）
- GEPA 论文：https://arxiv.org/abs/2507.19457 ；代码 https://github.com/gepa-ai/gepa （MIT，已并入 DSPy 生态）
- Hermes 主仓库：https://github.com/NousResearch/hermes-agent ；文档 https://hermes-agent.nousresearch.com/docs/ ；自进化子项目：https://github.com/NousResearch/hermes-agent-self-evolution
- 华为云部署方案：https://www.huaweicloud.com/intl/zh-cn/solution/implementations/deploying-hermes-agent.html
- 掘金"进化基因"叙事原文（供批判性对照）：https://juejin.cn/post/7657560105181085747
- 后续线索：①检索结果中见 21 财经等报道称中国团队 EvoMap 指控 Hermes Agent"架构级抄袭"其开源引擎 Evolver（未核对原文，待查证）；②官方 Memory 文档页（persistent memory / user profiles）可补三层记忆的官方口径；③`--eval-source sessiondb` 支持导入 Claude Code/Copilot 真实会话当评测集，是"用户修正数据→skill 进化"跨产品证据，值得深挖
