---
type: log
created: 2026-08-23
updated: 2026-08-23
sources: []
---

# 日志

> 追加式时间线，只增不改。条目格式：`## [YYYY-MM-DD] 操作 | 标题`，操作 ∈ init | ingest | query-filed | lint | schema。
> 快捷查看：`grep "^## \[" wiki/log.md | tail -5`

## [2026-08-23] init | 仓库初始化

按 Karpathy LLM Wiki 模式初始化本仓库。决策：通用个人知识库；中文正文 + 英文 slug；Obsidian 附件目录设为 raw/assets/。新建：CLAUDE.md（schema）、raw/{assets}、wiki/{sources,concepts,entities}、index.md、log.md、.gitignore、README。

## [2026-08-23] ingest | Karpathy《LLM Wiki》（gist，2026-04-04）

- 新增来源：`raw/2026-04-04-karpathy-llm-wiki.md`（首次 ingest，验证工作流）
- 新建页面：sources/karpathy-llm-wiki、concepts/llm-wiki、concepts/wiki-vs-rag、entities/andrej-karpathy、overview
- 更新：index.md（首次填充）
- 备注：Karpathy 原文 gist 的评论区含社区实践经验，登记为后续候选来源
