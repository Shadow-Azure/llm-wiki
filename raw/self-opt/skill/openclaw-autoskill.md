---
title: "OpenClaw 的技能自学习（Self-learning / Skill Workshop）与第三方 AutoSkill 项目——名称归属核实"
layer: skill
source_urls:
  - https://docs.openclaw.ai/tools/self-learning
  - https://docs.openclaw.ai/tools/skills
  - https://docs.openclaw.ai/concepts/memory
  - https://github.com/ECNU-ICALK/AutoSkill
  - https://en.wikipedia.org/wiki/OpenClaw
accessed: 2026-08-26
local_pdf:
---

# OpenClaw「AutoSkill」核实：官方 Self-learning + 第三方 AutoSkill

> 一句话：核实结论是"功能存在、名称张冠李戴"——OpenClaw 官方的自学习功能叫 **Self-learning**（经 Skill Workshop 提案队列，把用户修正与成功经验结晶为 SKILL.md，补丁式语义级增量修改、哈希绑定、可回滚），而 **"AutoSkill" 是华东师大 ICALK 等机构的学术开源项目**（arXiv 2603.01145，主张"把短期交互变成长期能力资产"，提供 AutoSkill4OpenClaw 集成）；原 passage 把两者混成了一个"OpenClaw 的 AutoSkill"。

## 是什么 / 背景

**OpenClaw 项目本身**：开源个人 AI 助理，运行在本机、经聊天渠道交互。改名史核实属实：2025 年 11 月以 **Clawdbot** 之名发布（作者 Peter Steinberger）→ 2026-01-27 更名 **Moltbot**（据报道因 Anthropic 就 "Claw" 提出商标请求）→ 再更名 **OpenClaw**（[Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)；[Medium 时间线](https://medium.com/data-science-collective/what-are-clawdbot-moltbot-and-openclaw-7cc9faaae6c3)；[CNBC 报道](https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook.html)）。官方文档至今保留 Clawdbot 时代痕迹（"Legacy `metadata.clawdbot` blocks are still accepted"，[Skills 文档](https://docs.openclaw.ai/tools/skills)）。故"2026 年初 renamed、前身 Clawdbot/Moltbot"属实。

**两条被混淆的线**：

1. **官方 Self-learning**（[docs.openclaw.ai/tools/self-learning](https://docs.openclaw.ai/tools/self-learning)）：OpenClaw 内建能力，"turns corrections and successful work into reusable skills"，全部经由 **Skill Workshop**（agent 与 skill 文件之间的提案队列：`openclaw skills workshop list/inspect/evaluate/apply`）完成。文档侧栏同时还有独立的 Memory 子系统（[concepts/memory](https://docs.openclaw.ai/concepts/memory)）。
2. **第三方 AutoSkill**（[github.com/ECNU-ICALK/AutoSkill](https://github.com/ECNU-ICALK/AutoSkill)）：学术项目 "AutoSkill: Experience-Driven Lifelong Learning via Skill Self-Evolution"（华东师范大学 ICALK + 上海 AI 实验室，arXiv 2603.01145），仓库含 `AutoSkill4OpenClaw/` 集成目录——"embedded-first OpenClaw integration for automatic skill extraction, maintenance, and native skill mirroring"。README 原话正是原 passage 的出处级表述："**AutoSkill turns short-term interactions into long-term capability assets**"。

## 机制：用户数据怎么流动

### 线路 A：OpenClaw 官方 Self-learning（经 Skill Workshop）

（引文均出自 [官方文档](https://docs.openclaw.ai/tools/self-learning)）

**信号来源（显式 + 隐式）**
- 前台 agent 发现正在用的 skill 有错或不完整（隐式：运行中撞上的失败）。
- 被用户打断的深度 turn："the wrong path and its correction are exactly the evidence worth keeping"（走错的路 + 修正本身就是值得保留的证据）。
- 成功但有复用价值的工作：后台 experience review 寻找"可复用的恢复技巧"或"能省掉至少两次未来模型/工具往返的稳定流程"。
- 持久的用户纠正/常设指令（"from now on / always / never / stop doing X"）。
- 明确排除：例行成功工作、个人偏好事实、瞬态环境故障、无证据的泛泛建议、密钥凭据。

**分析方式**
- 两条路径：(1) **同轮修复**——前台 agent 发现 skill 错误时当场读当前 live skill 并起草 targeted patch；(2) **延迟经验审查**——turn 结束且系统静默 30 秒后，跑一次隔离的后台 review（单次、有界；触发门槛为该 turn ≥10 次模型迭代，或同会话累计未审查工作量达标；会话中同时只跑一个，绝不拖延前台回答）。
- 审查者是"被隔离且偏向小而可证"的模型：只拿真实证据写作，无模板/模式匹配路径；收到前台实际读过/调用过的 skill 权威回执；优先更新本次用过的可写 skill，其次已有 skill，仅在无覆盖时新建。

**生成什么改进（语义级增量、不覆盖）**
- **补丁式精准修改**："A patch quotes the exact live text to change"（补丁逐字引用要改的现有文本）；如需全文重写，则"must preserve everything still useful"。
- **改前必读全文 + 哈希绑定**："Before changing an existing skill, the reviewer must read its complete current body. Both targeted patches and full-body rewrites bind the proposal to that read's content hash."——目标 skill 若在应用前已变化，提案即失效（stale），天然防覆盖冲突。超出阅读预算的 skill 不允许自主更新。

**谁审批 / 怎么上线 / 回滚**
- 三档模式：`off`（关闭）/ `propose`（每次捕获都待人工审阅）/ **`auto`（默认**：经安全扫描门控的 Workshop 路径自动应用，无需操作员审阅**）**。
- 安全护栏（auto 模式默认安全的依据）：应用前即时安全扫描（critical 发现→隔离而非应用）；Workshop 只能写自己创建的 skill 目录，手写/内置/插件 skill 一律只读；应用时记录 prior 内容作**回滚元数据**；自动应用只试一次不重试；每日一次 collection review 由隔离会话整理工作区 skill（可改进、合并、删除，保留一份可恢复备份）。
- 上线效果："Immediate repair changes the live skill for new sessions. It does not rewrite the skill snapshot already loaded into the running session."（即时修复对新会话生效，不改当前会话已加载快照——类似热更新边界。）
- 人工出口：`/learn` 显式从当前对话起草提案（永不自动应用）；`openclaw skills workshop reject/quarantine <id>` 一键拒绝/隔离。
- 成本/隐私：experience review 只在深度 turn 后增加一次有界模型运行，复用前台同 provider/model/auth；披露的轨迹上限 60,000 字符；每日 collection review 只读 skill 名录+全文。

### 线路 B：AutoSkill（ECNU-ICALK，含 OpenClaw 集成）

（引文出自 [项目 README](https://github.com/ECNU-ICALK/AutoSkill)）

- **信号来源**：真实交互经验（对话消息 + agentic 轨迹），也可离线导入 OpenAI 格式历史会话或文档提取 skill。
- **分析方式**：`Experience (messages/events) -> Skill Extraction (candidate) -> Skill Maintenance (add / merge / discard) -> Skill Store (SKILL.md + 向量索引)`。抽取器每次至多产出一个高质量候选；维护器对现有 skill 查相似度后决定新增/合并/丢弃。
- **生成什么改进**：标准 Agent Skill 产物（SKILL.md，可解释可编辑可迁移），版本化管理——用户后续反馈不新建重复 skill，而是合并更新并升版本（v0.1.0 → v0.1.1），"Merge updates preserve and improve capabilities"。README 明确策略："If a similar user skill already exists, prefer merge/update over creating a duplicate skill."
- **审批**：backend-first 自动增/合并，可选人工编辑/保存/删除 SKILL.md。
- **检索注入**：每轮查询经向量检索选 skill 注入上下文；用量计数按用户隔离，自动修剪陈旧 skill（默认 `retrieved >= 40` 且 `used <= 0`）。
- **OpenClaw 集成**（AutoSkill4OpenClaw）：以插件形式嵌入（`before_agent_start`/`agent_end` 生命周期钩子 + native skill mirroring 把进化的 skill 镜像为 OpenClaw 原生 skill），安装后重启 gateway 生效。README News 列 "2025-02-26: AutoSkill4OpenClaw 1.0 released"——按时间线（OpenClaw 2026 年初才定名）疑为 2026-02-26 之笔误，原样照录备考。
- 版本沿革：AutoSkill 1.0（对话中即时抽 skill）→ AutoSkill4Doc（文档提取）→ SkillEvo 1.0（replay/评估/变异/晋升的迭代自进化）。

### 官方 Memory 与 skill 的关系（澄清"短期→长期记忆→SKILL.md"链条）

OpenClaw 官方记忆系统是**独立链路**：每日笔记 `memory/YYYY-MM-DD.md`（工作层/短期）→ 由默认 **dreaming** 后台整合（评分/召回频率/查询多样性门控）蒸馏进 `MEMORY.md`（长期）——结晶终点是 MEMORY.md，**不是** SKILL.md（[concepts/memory](https://docs.openclaw.ai/concepts/memory)）。"短期→长期→结晶成 SKILL.md"的完整链条只在 AutoSkill 项目表述中成立（短期交互→长期能力资产=SKILL.md）；OpenClaw 官方是"对话修正/成功经验→（Self-learning）→ SKILL.md"。

## 关键事实与数字（附出处）

| 事实 | 出处 |
|---|---|
| OpenClaw 官方功能名：Self-learning（skills.workshop.autonomous.mode: off/propose/auto，默认 auto） | [官方文档](https://docs.openclaw.ai/tools/self-learning) |
| 补丁逐字引用原文 + 内容哈希绑定 + 改前必读全文 + 回滚元数据 | 同上 |
| experience review 门槛：≥10 次模型迭代、系统静默 30 秒、单会话单审查、轨迹上限 60k 字符 | 同上 |
| OpenClaw 遵循 AgentSkills 规范（"OpenClaw follows the AgentSkills spec"） | [Skills 文档](https://docs.openclaw.ai/tools/skills) |
| 记忆：daily notes → dreaming 蒸馏 → MEMORY.md（与 skill 分离） | [Memory 文档](https://docs.openclaw.ai/concepts/memory) |
| AutoSkill = ECNU-ICALK + 上海 AI Lab 学术项目，arXiv 2603.01145，AutoSkill4OpenClaw 集成 | [GitHub README](https://github.com/ECNU-ICALK/AutoSkill) |
| "turns short-term interactions into long-term capability assets"；merge 优先于新建 | 同上 |
| 改名史：Clawdbot（2025-11）→ Moltbot（2026-01-27）→ OpenClaw | [Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)、[Medium](https://medium.com/data-science-collective/what-are-clawdbot-moltbot-and-openclaw-7cc9faaae6c3)、[CNBC](https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook.html) |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 「OpenClaw 的 AutoSkill」：短期记忆→长期记忆→自动结晶成标准 SKILL.md | 功能真实存在但名称归属有误：OpenClaw 官方功能叫 **Self-learning**（[官方文档](https://docs.openclaw.ai/tools/self-learning)），文档站无 "AutoSkill" 页面；**AutoSkill 是第三方学术项目**（ECNU-ICALK，[GitHub](https://github.com/ECNU-ICALK/AutoSkill)），其对 OpenClaw 的集成即原 passage 所述机制的最接近来源——README："AutoSkill turns short-term interactions into long-term capability assets"，产物为标准 SKILL.md | ⚠️ |
| 语义级增量修改不覆盖 | 两个来源分别成立：官方——补丁逐字引用原文、哈希绑定防覆盖冲突、rewrite 须保留一切仍有用的内容（[官方文档](https://docs.openclaw.ai/tools/self-learning)）；AutoSkill——"prefer merge/update over creating a duplicate skill"（[README](https://github.com/ECNU-ICALK/AutoSkill)） | ✅ |
| （附带核实）OpenClaw 是 2026 年初 renamed 的开源个人助理，前身 Clawdbot/Moltbot | 属实：Clawdbot 2025-11 → Moltbot 2026-01-27 → OpenClaw（[Wikipedia](https://en.wikipedia.org/wiki/OpenClaw) 等） | ✅ |

结论：这条是五项中唯一需要改写的 passage 声明——应表述为"OpenClaw 官方 Self-learning/Skill Workshop（自动结晶 + 补丁式增量更新）+ 第三方 AutoSkill 项目（ECNU-ICALK，含 OpenClaw 集成，短期交互→长期能力资产）"。若 passage 本意指学术项目 AutoSkill 的 OpenClaw 集成，则机制描述基本准确，只是"的"字暗示了官方归属。另注意 OpenClaw 官方的 memory 链路（MEMORY.md）与 skill 链路（SKILL.md）是两套系统，"记忆结晶成 SKILL.md"不是官方单一管线。

## 本地文件与延伸阅读

- 本地：`raw/self-opt/skill/anthropic-agent-skills-skill-creator.md`（OpenClaw 遵循的 AgentSkills 规范来源）；`raw/self-opt/skill/huawei-skill-patch.md`（华为云文中"轨迹蒸馏"流派的 Hermes Agent 与 AutoSkill4OpenClaw 的 trajectory 提取同流）
- [OpenClaw Self-learning 官方文档](https://docs.openclaw.ai/tools/self-learning)（中文版可切 zh-CN）
- [OpenClaw Skills 总览（含 Skill Workshop / ClawHub / 安全模型）](https://docs.openclaw.ai/tools/skills)
- [OpenClaw Memory 概述（dreaming 机制）](https://docs.openclaw.ai/concepts/memory)
- [ECNU-ICALK/AutoSkill（论文 arXiv 2603.01145）](https://github.com/ECNU-ICALK/AutoSkill)
- [Wikipedia: OpenClaw（项目与改名史）](https://en.wikipedia.org/wiki/OpenClaw)
