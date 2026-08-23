---
type: source
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-04-04-karpathy-llm-wiki.md"
---

# 来源摘要：Karpathy《LLM Wiki》

- **作者**：[[wiki/entities/andrej-karpathy|Andrej Karpathy]]
- **发布**：2026-04-04（[Gist 原文](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)）
- **一句话概括**：一份"复制给你的 LLM Agent"的 idea 文件——不要用 RAG 每次查询重新推导知识，让 LLM 增量地编译和维护一个持久的 markdown wiki，知识因维护成本趋近于零而持续复利。

## 核心要点

1. **RAG 的问题在于没有积累**：每次查询都从原始文档重新检索、重新拼装，交叉引用和综合分析从不沉淀（NotebookLM、ChatGPT 文件上传均如此）。
2. **LLM Wiki 是持久、复利的产物**：知识"编译一次、持续保鲜"；交叉引用已建好，矛盾已标记，综述反映读过的一切。详见 [[wiki/concepts/llm-wiki|LLM Wiki 概念页]]。
3. **三层架构**：raw sources（不可变）→ wiki（LLM 全权维护）→ schema（CLAUDE.md，人与 LLM 共同演化的契约）。
4. **三大操作**：ingest（摄入，一个来源可触及 10-15 个页面）、query（查询，好答案回填为新页面）、lint（定期体检：矛盾、过时、孤儿页、缺口）。
5. **两个导航文件**：`index.md`（内容目录，查询第一入口，~100 来源规模内足够，免向量检索基础设施）+ `log.md`（追加式时间线，`## [日期] 操作 | 标题` 前缀可被 grep 解析）。
6. **为什么成立**：人类放弃 wiki 是因为维护负担增长快于价值；LLM 不会厌倦、不会漏更新交叉引用。思想源头可追溯至 Vannevar Bush 的 Memex（1945）——Bush 没解决的"谁来维护"，LLM 解决了。

## 值得注意的细节

- Karpathy 的工作画面：一边开 LLM agent、一边开 Obsidian 实时浏览改动——"Obsidian 是 IDE；LLM 是程序员；wiki 是代码库"。
- 工具提示：Obsidian Web Clipper 剪藏来源；附件本地化到 `raw/assets/`；graph view 观察 wiki 形态；Marp 出幻灯片；Dataview 查 frontmatter；git 管版本。规模变大后可选 qmd（本地 BM25/向量混合搜索）。
- 文末 Note 强调：文档故意抽象，正确用法是与你的 agent 协作实例化——本仓库的实例化见 [CLAUDE.md](../CLAUDE.md)。

## 后续线索

- Gist 评论区（未剪藏）有大量社区实践经验：团队规模运行的教训（并发 ingest 去重、索引瓶颈、人工修正如何幸存再编译）等，值得作为第二个来源单独 ingest。
- 可关注 qmd、Marp、Dataview 等工具是否需要独立实体页（待内容增多后由 lint 决定）。
