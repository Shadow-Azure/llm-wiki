---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-04-04-karpathy-llm-wiki.md"
---

# LLM Wiki

**一句话定义**：由 LLM 增量构建并持续维护的持久个人知识库——知识在摄入时被"编译"进互相链接的 markdown 页面，而不是在查询时从原始文档临时检索拼装。

提出者：[[wiki/entities/andrej-karpathy|Andrej Karpathy]]（2026-04，见 [[wiki/sources/karpathy-llm-wiki|原文摘要]]）。与 RAG 的根本区别见 [[wiki/concepts/wiki-vs-rag|编译式知识库 vs RAG]]。

## 三层架构

| 层 | 内容 | 规则 |
|---|---|---|
| Raw sources | 文章、论文、图片、数据 | 不可变，LLM 只读；唯一事实来源 |
| The wiki | 摘要页、概念页、实体页、综述 | LLM 全权生成与维护，用户只读 |
| The schema | CLAUDE.md / AGENTS.md | 告诉 LLM 结构、约定、工作流；与用户共同演化 |

## 三大操作

- **Ingest（摄入）**：LLM 读新来源 → 与用户讨论要点 → 写摘要页 → 更新 index → 更新相关概念/实体页 → 追加 log。一个来源可触及 10-15 个页面。Karpathy 偏好一次一个、人在场；也支持低监督批量。
- **Query（查询）**：先读 index 定位，再读相关页，带引用综合回答。**好答案回填为页面**，让探索像摄入一样复利。
- **Lint（体检）**：查矛盾、过时论断、孤儿页、缺失的概念页、缺失交叉引用、数据缺口。

## 导航机制

- `index.md`：内容目录，按类别列出全部页面 + 一句话摘要。查询的第一入口。在 ~100 来源、数百页面规模内表现良好，**无需 embedding/RAG 基础设施**。
- `log.md`：追加式时间线。条目以 `## [YYYY-MM-DD] 操作 | 标题` 开头，可被 `grep "^## \[" log.md | tail -5` 等简单工具解析。

## 为什么成立

知识库被放弃的原因不是阅读和思考，而是簿记——更新交叉引用、保持摘要新鲜、标记矛盾、维护一致性。人类做不来这种持续簿记，LLM 不会厌倦、不会遗漏、一次能改 15 个文件。**维护成本趋近于零，是整个模式的成立前提。**

分工：人负责收集来源、定向分析、提出好问题；LLM 负责其余一切。

## 思想渊源

精神上承接 Vannevar Bush 的 **Memex**（1945）：私人的、主动策展的知识存储，文档之间的关联轨迹与文档本身同等重要。Bush 未解决的"谁来维护"，由 LLM 补全。

## 适用场景

个人成长记录 / 单一主题深度研究（evolving thesis）/ 读书伴侣（人物、主题、情节页）/ 团队内部 wiki（Slack、会议纪要、客户访谈喂养）/ 竞品分析、尽调、旅行规划、课程笔记、爱好深挖——一切"知识随时间积累、希望有组织而非散落"的场景。

## 本库实例化

本仓库即该模式的一个实例：通用个人知识库，中文正文 + 英文 slug，schema 见 [CLAUDE.md](../../CLAUDE.md)。Obsidian 在此架构中承担人类一侧的"IDE"角色（见 [[wiki/entities/obsidian|Obsidian]] 实体页）。

## 依据来源

- [[wiki/sources/karpathy-llm-wiki|Karpathy《LLM Wiki》（2026-04-04）]]
