---
title: EverMind Raven + EverMe（EverOS / HarnessBank 体系）
layer: harness
source_urls:
  - https://www.stdaily.com/web/gdxw/2026-08/25/content_569502.html
  - https://arxiv.org/abs/2607.13683
  - https://github.com/EverMind-AI/Raven
  - https://github.com/EverMind-AI/EverOS
  - https://github.com/EverMind-AI/EverMe
  - https://evermind.ai/
accessed: 2026-08-27
local_pdf: raw/self-opt/harness/evermind-harnessbank-2607.13683.pdf
---

# EverMind Raven + EverMe（EverOS / HarnessBank 体系）

> 一句话：EverMind 用 EverOS（跨 Agent 记忆底座）+ Raven（自进化 agent harness 运行时）+ EverMe（C 端个人记忆产品）+ HarnessBank（可验证 harness 进化研究框架）搭出一条"用户数据 → harness 自进化"的完整产品线，2026-08-25 获科技日报报道。

## 是什么 / 背景

- **公司**：EverMind（evermind.ai，主打 "Infinite Memory & Long-Term Consistency for AI Agents"）。HarnessBank 论文作者署名单位为 **EverMind AI + Shanda Group（盛大集团）**（论文 PDF 第 1 页：`1EverMind AI 2Shanda Group`，两人邮箱后缀 @shanda.com），且有中国科技日报（国家级科技媒体）专访式报道，可确认是**有中国资本背景的公司**（盛大集团关联），产品与开源生态以英文对外。
- **2026-08-25 科技日报报道**（记者李禾，《自进化智能体从研究概念迈向真实产品》）：EverMind"披露其围绕 HarnessBank、Raven 和 EverMe 构建的自进化技术与产品体系。其中，HarnessBank 负责解决 Agent 如何安全、可验证地持续改进，Raven 将自进化机制带入 Agent 运行时，面向 C 端的 EverMe 已上线内测"。来源：https://www.stdaily.com/web/gdxw/2026-08/25/content_569502.html
- **开源生态**（Raven README 的 "EverMind Ecosystem" 表）：
  - Raven — 自进化 agent harness（Apache-2.0，GitHub 3,640 stars，仓库创建于 2026-05-21，GitHub API 2026-08-27 读数）
  - EverOS — 记忆运行时："One portable memory layer for every AI agent: local-first, Markdown-native, user-owned, and self-evolving"（12,451 stars）
  - EverMe — 个人记忆层：开源 CLI + MCP Server + SDK + Agent 插件套件（52 stars）；托管产品另算
  - 还有 EverAlgo（算法引擎）、HyperMem（超图记忆）、EverMemBench / EvoAgentBench（评测）、MSA（长上下文）
  - 来源：https://github.com/EverMind-AI/Raven 、GitHub API 各仓库
- **注意**：Raven 仓库自述 pre-alpha（"Raven is pre-alpha and moving quickly"）。

## 机制：用户数据怎么流动

**信号来源**：用户在多个 Agent、知识工具、文件里散落的历史数据。EverMe C 端产品（已上线内测，围绕数字分身模块）"接入多个 Agent，支持将用户散落在不同 Agent、知识工具和文件中的历史数据汇集为长期记忆"（科技日报原文）。开源侧 EverMe 仓库提供把 Claude Code、Cursor、Codex、Kimi Code、Hermes、Raven、OpenClaw 等 Agent 接入记忆层的客户端工具链，后端可接 EverMe 托管服务或自托管 EverOS 端点（EverMe README.zh.md 三层表格：EverMe CLI+插件 / EverOS 记忆引擎 / EverMe 托管服务）。来源：https://github.com/EverMind-AI/EverMe

**处理/提炼**：EverOS 作为记忆引擎做长期记忆操作系统（durable user memory / agent memory / world knowledge）；Raven 的 SkillForge 把重复工作流检测为可复用程序性记忆（"detect reusable workflows, write skill files, track execution feedback, and evolve instructions when they stop working"），Sentinel 做主动巡检。科技日报的数据飞轮描述："用户持续产生任务和交互数据，系统沉淀为长期记忆，不同 Agent 在用户授权下调用这些记忆，并根据任务结果形成新的反馈和经验，让 AI 能基于用户长期记忆'越用越懂用户'。"

**更新什么（记忆 / harness / 策略）**：Raven 是"harness 即产品"的路线——README 原文："Raven helps agents improve across runs by continuously refining the systems around them: tools, skills, memory, code execution, policies, and working environment."（工具、技能、记忆、代码执行、策略、工作环境六类，全部在模型外围）。科技日报："Raven 将长期记忆、技能体系、运行策略及工具调用连接起来，使 Agent 可根据历史任务轨迹总结经验，并持续调整外围工作流，无须频繁修改底层模型参数。"

**验证与上线闭环**：这是 HarnessBank 论文（arXiv:2607.13683）解决的部分——如何证明 harness 改进是真的：
1. 双 Agent 非对称结构：任务 Agent（底层模型**冻结**，默认 Qwen3.6-27B）执行任务；独立且更强的 Evolver Agent（论文中为 **Claude Opus 4.8**）读执行轨迹、诊断失败模式、生成子代 harness，"without modifying the task model's parameters"。
2. Harness Gene Bank（基因库）：受 MAP-Elites 质量-多样性启发的分格存档，每格用二维语义坐标索引——改的是哪个 harness 组件（where）× 针对哪种失败病理（why），防止贪心搜索坍缩到保守小改动。子代可"reinvented, recombined, screened, and selected"。
3. Gated Harness Screening（四道顺序门控）：validity gate（排除基础设施故障污染的结果）→ activation gate（验证修改真的被执行，"An evolved but never-triggered harness is treated as inert"）→ paired significance gate（配对检验 z ≥ 1.96，即双侧 5% 显著水平）→ gain gate（必须胜过父代）。过门后才在完整训练集评估、竞争入库。
4. 论文还给出跨模型实验结论："并不存在一套能够跨模型通用的最优智能体框架"（科技日报转述；论文原文 "the improvements come from the model-specific self-evolving process, instead of a universally optimal harness"）。

⚠️ 体系接线注意：HarnessBank 与 Raven/EverMe 同属 EverMind（论文署名 + 科技日报"围绕三者构建的体系"表述），但 **Raven README 与 EverOS/EverMe 仓库均未提及 HarnessBank**，Raven 运行时与 HarnessBank 框架的工程级接线（Raven 是否内嵌 HarnessBank 门控）无公开文档，属"同一公司的体系叙事"而非"已公开集成的产品"。

## 关键事实与数字（附出处）

| 事实/数字 | 出处 |
|---|---|
| HarnessBank 论文：arXiv:2607.13683，v1 提交 2026-07-15，v2 2026-07-30；9 页 4 图 3 表 | https://arxiv.org/abs/2607.13683 |
| 作者：Xiaotian Luo, Dizhan Xue, Fengxingyu Wang, Chuanrui Hu, Yafeng Deng；单位 EverMind AI + Shanda Group | 论文 PDF 第 1 页（本地 evermind-harnessbank-2607.13683.pdf） |
| 冻结 backbone 默认 Qwen3.6-27B，Evolver Agent 为 Claude Opus 4.8；指标 Pass@1、K=3 次尝试 | 论文 §4.1 |
| 七个基准：Terminal-Bench-2 + EvoAgentBench 五项（LiveCode、Omni-MATH、BrowseComp+、GDPval、SWE-bench）+ AppWorld；总体提升 **5.1%–15.4%** | 论文摘要与 §4.1 |
| AppWorld 测试 Pass@1：vanilla **41.3** → HarnessBank **56.7**（GEPA 44.0，DGM 52.0）；AppWorld 最强，z=6.44，n=168 | 论文图 4（PDF 第 7 页）与 §4.4 |
| SWE-bench 测试 +5.1% 但 n=26 未过 2σ 门（z=0.78），作者自标 "preliminary—small-n noise, not overfitting"；训练集 101 任务 +13.9% | 论文 §4.2 |
| 对比方法：GEPA（prompt-only）与 DGM（Darwin Gödel Machine）；"GEPA finds no variant beating its seed on LiveCode in 47 iterations"；GEPA 在 AppWorld 训练增益在封存测试上消失（+2.8, z=0.97）；DGM 仅 AppWorld 过门（+10.7%, z=3.00），Omni-MATH 上线了比 vanilla 更差的 harness（−1.1%） | 论文 §4.3 |
| EvoAgentBench（论文五个域的基准来源）本身是 EverMind 自己的仓库 | Raven README 生态表；https://github.com/EverMind-AI/EvoAgentBench |
| EverMe 已上线内测（数字分身模块、多 Agent 接入）；数据飞轮表述 | 科技日报 2026-08-25 |
| Raven：Apache-2.0，"The Self-Improving Agent Harness, built on EverOS"，pre-alpha；GitHub 描述 "The Harness of Harnesses" | https://github.com/EverMind-AI/Raven |
| EverOS：记忆运行时，Raven 的 Memory Engine 后端 | Raven README 架构图（Memory Engine: EverOS / local skills / SkillForge） |
| Raven 发布通稿（自称 "L3-level digital life"）见 Yahoo Finance / USA Today press-release 版位（企业新闻稿，可信度低于官方文档与论文） | https://www.usatoday.com/press-release/story/37979/evermind-launches-raven-agent-the-self-improving-harness-that-defines-l3-level-digital-life/ |

## 与原 passage 声明的核对（表格：原声明 | 检索到的说法（附 URL） | 判定）

| 原声明 | 检索到的说法 | 判定 |
|---|---|---|
| 用户跨 Agent 的长期记忆汇入 EverOS | EverMe 把多 Agent/工具/文件历史数据汇为长期记忆（科技日报）；EverMe CLI 接多个 Agent，记忆引擎为 EverOS（可自托管）或 EverMe 托管服务（EverMe README.zh.md）；EverOS = "portable memory layer for every AI agent"。https://www.stdaily.com/web/gdxw/2026-08/25/content_569502.html ; https://github.com/EverMind-AI/EverMe | ✅ |
| Raven 依据历史轨迹持续调整"技能体系、运行策略、工具调用"这套 Harness | Raven README："continuously refining … tools, skills, memory, code execution, policies"；科技日报："将长期记忆、技能体系、运行策略及工具调用连接起来……持续调整外围工作流"。https://github.com/EverMind-AI/Raven | ✅ |
| HarnessBank 负责验证改进是否真实有效 | 论文核心即 Gated Verification：四道门控（validity/activation/significance/gain）+ 全训练集复评 + 基因库准入竞争；科技日报："HarnessBank 负责解决 Agent 如何安全、可验证地持续改进"。https://arxiv.org/abs/2607.13683 | ✅ |
| 冻结权重 Qwen3.6-27B 仅靠 Harness 进化，AppWorld Pass@1 41.3%→56.7% | 论文 §4.1"All experiments evolve the harness only, with the backbone frozen; by default Qwen3.6-27B"；图 4 AppWorld 41.3→56.7（vanilla→HarnessBank） | ✅ |
| 双 Agent 非对称结构（冻结任务 Agent + 更强 Evolver Agent） | 论文：task agent（冻结 Qwen3.6-27B）+ evolver agent（Claude Opus 4.8）；科技日报："能力更强的 Evolver Agent 读取任务执行轨迹，诊断失败模式" | ✅ |
| 基因库分格存档 + 四道门控 | 论文：HGB 语义分格（组件 × 失败病理，MAP-Elites 启发）；"applies four sequential gates: validity / activation / paired significance / gain" | ✅ |
| 7 个基准 Pass@1 全部正向 +5.1 到 +15.4 个百分点 | 摘要 "improvements from 5.1% to 15.4%"；§4.2 测试集 +9 到 +15.4%；**但 SWE-bench +5.1% 一项 n=26 未过作者自己的 2σ 显著门，作者标注 preliminary**——"全部正向"成立，"全部统计显著"不成立 | ✅（附保留） |
| 被称为"用户数据→harness 自进化"最完整的商业叙事 | 未找到任何独立第三方使用该评价；这是 passage 作者自己的概括。客观依据：该公司确实同时握有论文（HarnessBank）+ 开源运行时（Raven/EverOS）+ C 端产品（EverMe）+ 自有基准（EvoAgentBench），链条完整度高，但 Raven pre-alpha、EverMe 仅内测、HarnessBank 与产品线工程接线未公开 | ⚠️ |

## 本地文件与延伸阅读

- 本地 PDF：`raw/self-opt/harness/evermind-harnessbank-2607.13683.pdf`（arXiv:2607.13683v2，%PDF-1.7 校验通过）
- 论文：https://arxiv.org/abs/2607.13683 （HarnessBank: Semantic Gene-Bank Search with Gated Verification for Agent-Harness Self-Evolution）
- 科技日报报道：https://www.stdaily.com/web/gdxw/2026-08/25/content_569502.html
- Raven：https://github.com/EverMind-AI/Raven ；EverOS：https://github.com/EverMind-AI/EverOS ；EverMe：https://github.com/EverMind-AI/EverMe ；官网 https://evermind.ai/
- 值得跟进的线索：① Raven 运行时是否/如何内嵌 HarnessBank 门控（未见公开文档）；② EvoAgentBench 作为 EverMind 自有基准，论文五域结果的外部可复现性；③ EverMe 内测的数据授权与隐私模型（"用户授权下调用"仅有口号级描述）。
