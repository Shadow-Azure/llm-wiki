---
title: "Dynamic Agent Skills: A Lifecycle Survey and Taxonomy of Evolving Skill Libraries — 论文地图"
layer: refs
source_urls:
  - https://arxiv.org/abs/2607.10113
  - https://arxiv.org/html/2607.10113v1
  - https://openreview.net/forum?id=cjU3YbcRr8
  - https://www.cmu.edu/aimsec/publications/index.html
accessed: 2026-08-26
local_pdf: raw/self-opt/dynamic-agent-skills-survey-2607.10113.pdf
---

# Dynamic Agent Skills: A Lifecycle Survey and Taxonomy of Evolving Skill Libraries

> 一句话：一篇 TMLR 综述（arXiv:2607.10113，Yubo Li，CMU），审计 2023–2026 年 124 篇论文，把"动态 agent 技能"这类系统定义为**生命周期管理、可验证、持续演化的技能仓库（lifecycle-managed, verified, evolving artifact stores）**——可作为"用用户数据驱动 AI Agent 自进化"主题的论文索引与术语地图。

## 是什么 / 背景（验证结果）

- **存在性 ✅**：arXiv:2607.10113，2026-07-11 提交（v1），题名 *Dynamic Agent Skills: A Lifecycle Survey and Taxonomy of Evolving Skill Libraries*。https://arxiv.org/abs/2607.10113
  - 注意：完整题名比常见简称 "Dynamic Agent Skills: A Lifecycle Survey" 多出 "and Taxonomy of Evolving Skill Libraries"。
- **作者**：Yubo Li（单作者；卡内基梅隆大学，CMU-NIST AIMSEC 合作研究中心出版物页亦收录此文）。https://www.cmu.edu/aimsec/publications/index.html
- **发表信息 ⚠️**：检索结果（ResearchGate / CMU AIMSEC / Google Scholar 条目）显示发表于 **TMLR（Transactions on Machine Learning Research）**，OpenReview 论坛存在：https://openreview.net/forum?id=cjU3YbcRr8 。arXiv 摘要页本身未见 journal-ref 字段，TMLR 状态以 OpenReview 页面为准（本日 arXiv/openreview 直接抓取受限，此条以多源检索交叉为准）。
- **语料边界**：124 篇 2023–2026 审计集（audit set）；明确时间截止（temporal cutoff）为 **2026-05-31**——"Papers appearing after that date are outside the surveyed corpus"（§2.3）。https://arxiv.org/html/2607.10113v1

## 机制：它如何刻画"技能仓库的演化"

综述的三个分析工具（摘要与 §1.3 贡献清单）：

1. **六义分类（six-sense taxonomy）**：区分当前论文里结构迥异的"skill"——可执行代码（executable-code：Voyager、LATM、LIVE-SWE、AgentFactory、ASI、SkillOps 等）、自然语言启发式（NL-heuristic：ERL、RetroAgent 等）、SKILL.md 包（MetaClaw、ABSTRAL、Trace2Skill、Memento、AutoRefine 等）、工作流图、学习适配器，外加两个边界情形。
2. **八阶段生命周期架构（eight-stage lifecycle architecture）**：证据获取（evidence acquisition，从交互中收集证据）→ 提案（proposal）→ 验证/准入（verification/admission）→ 存储（storage）→ 检索/组合（retrieval/composition）→ 维护/修复（maintenance/repair）→ 蒸馏/移植（distillation/portability）→ 治理（governance，含溯源 provenance 与回滚 rollback）。
3. **技能记录 schema + 十算子词汇（ten-operator vocabulary）**：{Add, Refine, Merge, Split, Prune, Distill, Abstract, Compose, Rewrite, Rerank}——为比较不同系统的库更新操作提供公共语言。

核心论点：静态技能库把"编写、验证、检索、溯源、适配"全部压成一次性设计决策，在长期部署中会系统性失效（§4）；动态技能系统应是"演化工件存储之上的受控状态机"（§5）。

## 关键事实与数字（附出处）

| 事实 | 说法 | 出处 |
|---|---|---|
| 审计规模 | 124-paper 2023–2026 audit set | 摘要，https://arxiv.org/abs/2607.10113 |
| 核心定义 | "lifecycle-managed, verified, evolving artifact stores"（agents collect evidence from interaction, propose skill updates, verify and admit candidates, organize them for retrieval and composition, repair or prune stale entries, and govern sharing through provenance and rollback） | 同上 |
| 时间截止 | May 31, 2026（§2.3 Temporal Cutoff and Update Policy） | https://arxiv.org/html/2607.10113v1 |
| 结论要点 | 准入与修复反复被证明重要；验证器质量实质影响 skill-aware RL；平面检索随库增大而退化；现有基准少报库轨迹、用法-效用差距与安全面 | 摘要 |
| 交付物 | 六义分类 + 八阶段生命周期 + 十算子 + 七个证据分级模式（§9）+ 八个安全面（§11.1）+ 八个开放问题（§12）+ 报告标准 | §1.3 |

## 与"用户数据驱动进化"最相关的小节（论文地图用法）

1. **§5 Dynamic Skill Systems as Lifecycle-Managed Stores**（5.1 参考架构 / 5.2 准入边界）：把"从交互证据到入库"的全流程形式化——本主题（EvoSOP、UCT、dsh 插件）的通用骨架。
2. **§7.1 Edit repertoire: expansion, compression, and refactoring**：Add/Merge/Prune 等编辑算子谱系；EvoSOP 的"构建→合并→修剪"与 dsh-skill-evolve 的"提炼-入库"都能在此对号入座。文中专门记录 2026 年 5 月下旬的 "skill optimization" 波（SkillOpt、SkillGrad、SkillGen）与 SkillOps 的"库级技术债管理"（merge/repair/retire）。
3. **§7.2 Admission and verification are the selection mechanism**：验证器即选择机制（单元测试 LATM、落地 rollout SkillWeaver、集成评审 Trace2Skill/AgentSkillOS 等）——UCT 的"沙箱+评审双验证"属此类。
4. **§9 Seven Evidence-Graded Patterns**：尤其 9.5 "Focused libraries often beat comprehensive ones"（精选库常胜过大而全——与 EvoSOP "typically fewer than 10 SOPs ≈80% 成功率"直接呼应）、9.6 "维护在库变大后成为承重墙"、9.3 "平面检索在中型库即退化"。
5. **§10.3 Marketplaces, plugins, and cross-user aggregation**：技能市场/插件/跨用户聚合——产业侧"用户数据驱动进化"（飞书 aily 技能生态、dsh 插件生态）对应的论文线索。
6. **§11 Safety and Governance**（11.1 八个安全面：经准入技能的提示注入、供应链投毒、凭证泄露等；11.3 部署所需治理原语：溯源、回滚）——与 dsh-auto-evolve 的"版本化账本+自动回滚"设计互为印证。

**重要负发现（2026-08-26 核验）**：该综述 v1 全文（HTML）**未引用 EvoSOP 与 UCT**——检索论文名、作者姓（Ding/Shen）、arXiv 号（2607.07321 / 2602.01983）均为零命中。原因分析：EvoSOP 提交于 2026-07-08，晚于语料截止日 2026-05-31；UCT 提交于 2026-02-02、早于截止日，但不在 124 篇审计集内（综述自称非穷举，"does not claim an exhaustive count"）。因此**不能把这篇综述当作这两篇论文的引文来源**，但它仍是本主题最系统的论文索引。

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 综述存在，题名 "Dynamic Agent Skills: A Lifecycle Survey"（arXiv:2607.10113，TMLR 2026） | 存在 ✅；完整题名多 "and Taxonomy of Evolving Skill Libraries"；TMLR 见 OpenReview 论坛（https://openreview.net/forum?id=cjU3YbcRr8）与多源检索，arXiv 页无 journal-ref | ⚠️ 基本属实，题名与 venue 细节需按上表修正 |
| 它把这类系统定义为"生命周期管理、可验证、持续演化的技能仓库" | "lifecycle-managed, verified, evolving artifact stores"（摘要逐字对应） | ✅ |
| 可作为本主题的论文索引 | 124 篇审计集 + 六义/八阶段/十算子分类框架；但不含 EvoSOP/UCT（见上"负发现"） | ✅（带边界说明） |

## 本地文件与延伸阅读

- 本地 PDF：`raw/self-opt/dynamic-agent-skills-survey-2607.10113.pdf`（352 KB，%PDF-1.7 已验证）
- arXiv：https://arxiv.org/abs/2607.10113 ；HTML 全文：https://arxiv.org/html/2607.10113v1 ；OpenReview：https://openreview.net/forum?id=cjU3YbcRr8
- 同主题已落库文件：`raw/self-opt/tool/evosop.md`、`raw/self-opt/tool/uct-tool-creator.md`、`raw/self-opt/tool/feishu-aily.md`、`raw/self-opt/skill/dsh-skill-evolve.md`
- 综述中值得后续单独追踪的方法（举例）：LATM（工具即学习资产）、SkillWeaver（自建网站技能）、Trace2Skill、AgentSkillOS、ASI、SkillOps、SkillOpt/SkillGrad/SkillGen（skill optimization 波）、SkillFlow-Bench（动态技能基准）
