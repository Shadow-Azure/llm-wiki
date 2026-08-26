---
title: raw/self-opt 索引 — "用户数据驱动 Agent 自进化"方法检索库
created: 2026-08-27
note: >
  应用户要求（2026-08-26），对一段关于"用户数据驱动自进化（model/harness/tool/skill 四层）"
  的中文分析中提到的全部方法做 live 检索核实，逐方法落盘。每个 briefing 含：机制详解、
  关键数字（附 URL）、"与原 passage 声明的核对"表（✅ 证实 / ⚠️ 部分证实或有出入 / ❌ 未能证实）。
  检索执行日 2026-08-26/27。共 17 个方法/综述文件 + 6 份论文 PDF。
---

# raw/self-opt 索引 — 自进化方法四层图谱

## 总览表

| 层 | 方法 | 判定 | 一句话 | 文件 |
|---|---|---|---|---|
| model | Grok 4.5 × Cursor | ⚠️ | 联合训练/数万亿 token/83.3%/600 亿美元收购均有一手来源；"调试轨迹/用户修正"细节仅见二手，且 83.3% 只排第三 | [[raw/self-opt/model/grok-4-5-cursor.md]] |
| model | NVIDIA NVInfo | ✅ | EACL 2026 industry paper，全部数字与论文吻合（495 负样本、70B→8B、96%、延迟-70%） | [[raw/self-opt/model/nvidia-nvinfo.md]] 📄 |
| model | GitHub Copilot | ⚠️ | 默认学习+opt-out 属实但生效日为 2026-04-24 且仅个人版；"Work IQ" 系张冠李戴（应为 Copilot Memory） | [[raw/self-opt/model/github-copilot-data.md]] |
| harness | EverMind Raven/EverMe | ✅ | HarnessBank 论文（AppWorld 41.3→56.7）+ Raven 运行时 + EverMe 内测构成完整链条；"最完整商业叙事"是原 passage 自评 | [[raw/self-opt/harness/evermind-raven-everme.md]] 📄 |
| harness | Hermes + GEPA | ✅ | Nous 开源 + GEPA（ICLR 2026 Oral）+ 华为云部署方案全部属实；"进化基因/1247 粒"是中文博主演义 | [[raw/self-opt/harness/hermes-agent-gepa.md]] 📄 |
| harness | CopilotKit Intelligence | ⚠️ | CLHF/AG-UI/自托管审计属实；"三层学习"出自 CEO X 帖而非官方页 | [[raw/self-opt/harness/copilotkit-intelligence-agui.md]] |
| harness | 阿里云 AgentLoop | ✅ | 五阶段数据飞轮/Experience/AgentSpace 与官方文档逐字对应（经验库 private preview） | [[raw/self-opt/harness/alibaba-agentloop.md]] |
| tool | EvoSOP | ✅ | arXiv:2607.07321（人大+阿里），轨迹→SOP 工具集四模块，"≤10 个工具 ~80% 成功率"附录逐字命中 | [[raw/self-opt/tool/evosop.md]] 📄 |
| tool | UCT | ✅ | arXiv:2602.01983（理想汽车），training-free 工具创建器；场景限定多模态推理而非通用 agent | [[raw/self-opt/tool/uct-tool-creator.md]] 📄 |
| tool | 飞书 aily | ⚠️ | "百余元 token→2 万元应用"数字属实但主语是飞书妙搭；aily 另有"自动学习优化"宣称 | [[raw/self-opt/tool/feishu-aily.md]] |
| skill | Dust Self-Improving Skills | ✅ | 官方文档逐字命中（夜间批量、显式+隐式信号、diff 审批，建议含工具调整/新工具） | [[raw/self-opt/skill/dust-self-improving-skills.md]] |
| skill | Warp skill 优化闭环 | ✅ | observer+grader+computer use 工程闭环属实；Code with Claude 演讲是另一条"人类反馈→PR"线 | [[raw/self-opt/skill/warp-skill-optimization-loop.md]] |
| skill | Anthropic Skills + Skill Creator | ✅ | 开放标准与 Create/Eval/Improve/Benchmark 属实；"2.0"与四模式名是社区口径，60/40+5 轮属触发描述子流程 | [[raw/self-opt/skill/anthropic-agent-skills-skill-creator.md]] |
| skill | 华为云三段式 | ✅ | jishuzhan 技术文章逐条命中（反思引擎→进化工厂→三层评估）；单一来源、无第三方复核 | [[raw/self-opt/skill/huawei-skill-patch.md]] |
| skill | OpenClaw "AutoSkill" | ⚠️ | 官方功能名是 **Self-learning**；AutoSkill 实为 ECNU+上海AI Lab 学术项目——名称张冠李戴 | [[raw/self-opt/skill/openclaw-autoskill.md]] |
| skill | dsh-skill-evolve | ✅ | 仓库存在（2026-08-14 创建，0 star/2 commits 极早期）；对照 dsh-auto-evolve | [[raw/self-opt/skill/dsh-skill-evolve.md]] |
| refs | Dynamic Agent Skills 综述 | ✅ | arXiv:2607.10113（CMU，124 篇审计）可当论文地图；**未引用** EvoSOP/UCT（负发现） | [[raw/self-opt/refs-survey-dynamic-agent-skills.md]] 📄 |

判定统计：✅ 12 项，⚠️ 5 项，❌ 0 项——passage 提到的方法**全部真实存在**，但 5 处细节/归属需修正。

## 论文 PDF 清单（6 份，均 %PDF 校验通过）

- `model/nvidia-nvinfo-eacl2026.pdf` — NVInfo（EACL 2026 industry，17 页）
- `harness/evermind-harnessbank-2607.13683.pdf` — HarnessBank（arXiv:2607.13683v2）
- `harness/gepa-2507.19457.pdf` — GEPA（arXiv:2507.19457v2，ICLR 2026 Oral）
- `tool/evosop-2607.07321.pdf` — EvoSOP（arXiv:2607.07321）
- `tool/uct-2602.01983.pdf` — UCT（arXiv:2602.01983）
- `dynamic-agent-skills-survey-2607.10113.pdf` — Dynamic Agent Skills: A Lifecycle Survey

## 检索发现的关键出入汇总（原 passage 需修正处）

1. **Copilot "Work IQ"**：GitHub 的偏好记忆功能叫 **Copilot Memory**（2026-05-15）；Work IQ 是 Microsoft M365 的 CLI/MCP 产品，且不存储数据。政策生效日为 **2026-04-24**（非"3 月起"），默认学习仅限个人版 Free/Pro/Pro+。
2. **飞书"百元 token→2 万元应用"**：新华网原文主语是**飞书妙搭**（2026-03-19 发布会案例），不是 aily。
3. **OpenClaw "AutoSkill"**：官方功能叫 **Self-learning**（Skill Workshop 提案队列+补丁+回滚）；AutoSkill 是第三方学术项目（ECNU-ICALK，arXiv:2603.01145）。
4. **Hermes GEPA"进化基因/4.7 万条→1,247 粒"**：出自掘金博主自构叙事（对 GEPA 缩写展开都写错了）；官方机制是"反射式提示进化 + Pareto 选择"。
5. **CopilotKit"三层学习"**：出自 CEO 的 X 帖；官方页可见的是上下文层（CLHF）+技能层，权重层仅为"导出微调数据集"且强调 No Fine-Tuning Required。
6. **Grok×Cursor 细节**："调试轨迹、多文件 diff、用户修正"三分类仅见 apidog 二手；官方只说 "user interactions with codebases and software tools"。TB 2.1 的 83.3% 排第三（低于 Fable 84.3%、GPT 5.5 83.4%）。
7. **"最完整商业叙事"（EverMind）**：找不到第三方使用该评价，属 passage 作者自评；客观上链条完整（论文+开源+C 端内测），但 Raven pre-alpha、EverMe 仅内测。

## 对 passage 四层成熟度判断的检验

- **skill 层最活跃** ✅：6 个方法全部真实，且 Dust/Warp/Anthropic/华为云均为官方文档级一手证据，机制细节（显式+隐式信号、diff/PR 审批）完整可查。
- **harness 层产品化中** ✅：4 个平台全部存在（EverMind 内测、Hermes 开源 85k+ star、CopilotKit/AgentLoop 企业级），其中 AgentLoop/CopilotKit 是"全链路进化基础设施"的直接证据。
- **工具层论文为主** ✅：EvoSOP/UCT 两篇论文真实，但产品化案例只有 Dust 的"工具调整建议"一处。
- **模型层巨头专属** ✅：Grok×Cursor（数万亿 token 群体飞轮）、NVInfo（内部负样本微调）、Copilot（默认学习）全真实；"个体级模型自进化"确实不存在。
- **"用户修正是最强信号"**：多产品有直接呼应（Dust 的隐式信号、Warp 的 Slack 反馈回流、CopilotKit CLHF、Hermes self-evolution 的 `--eval-source sessiondb`）。

## 溯源

原始 passage 存于 [[raw/2026-08-26 self-improving.md]]（2026-08-26 对话记录的末段）。本目录由 2026-08-26/27 的 live 检索生成（5 个并行检索 agent + 人工补跑 EverMind/Hermes 路线）。
