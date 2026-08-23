---
type: log
created: 2026-08-23
updated: 2026-08-23
sources: []
---

# 日志

> 追加式时间线，只增不改。条目格式：`## [YYYY-MM-DD] 操作 | 标题`，操作 ∈ init | ingest | query-filed | research | lint | schema。
> 快捷查看：`grep "^## \[" wiki/log.md | tail -5`

## [2026-08-23] init | 仓库初始化

按 Karpathy LLM Wiki 模式初始化本仓库。决策：通用个人知识库；中文正文 + 英文 slug；Obsidian 附件目录设为 raw/assets/。新建：CLAUDE.md（schema）、raw/{assets}、wiki/{sources,concepts,entities}、index.md、log.md、.gitignore、README。

## [2026-08-23] ingest | Karpathy《LLM Wiki》（gist，2026-04-04）

- 新增来源：`raw/2026-04-04-karpathy-llm-wiki.md`（首次 ingest，验证工作流）
- 新建页面：sources/karpathy-llm-wiki、concepts/llm-wiki、concepts/wiki-vs-rag、entities/andrej-karpathy、overview
- 更新：index.md（首次填充）
- 备注：Karpathy 原文 gist 的评论区含社区实践经验，登记为后续候选来源

## [2026-08-23] schema | CLAUDE.md 新增 research 工作流

- 新增 research（研究）操作：先查库 → 收集材料落 raw → 批量 ingest → 区分来源论断与 AI 推理（后者标注"待来源验证"）→ 修订综述 → 同步 index/log → 登记后续线索
- log 操作枚举新增 `research`

## [2026-08-23] query-filed | Obsidian 与笔记工具格局（含开源协议核实）

- 起因：用户 query（Obsidian 优势、与 LLM Wiki 关系、国内外同类开源情况、AI 适配性），两轮对话后回填
- 新建页面：entities/obsidian、concepts/note-tool-landscape（国内外开源/闭源盘点、AGPL/MIT/MPL/EE 协议商用含义、"Obsidian 最适合接 AI"论证——后者标注 AI 推断待验证）
- 更新：concepts/llm-wiki（"本库实例化"补 Obsidian 实体链接）、index.md
- 备注：开源协议当日经 GitHub API 逐仓库核实（思源/AppFlowy AGPL-3.0；AFFiNE 核心 MIT + 后端 EE 混合——"开源"实为 open core）；关键网页来源待剪藏为 raw，已登记于 note-tool-landscape 后续线索

## [2026-08-23] query-filed | 双向链接与关系图谱机制

- 起因：用户两轮 query（双向链接/图谱展开；反向索引作用澄清），用户示意回填
- 新建页面：concepts/bidirectional-links（三层结构祛魅：链接文本→反向索引→图谱；Obsidian 语法；linked/unlinked mentions；断链合法与多重上下文；图谱三种诊断信号；反向索引作用清单；思想谱系 Memex→Zettelkasten→Wiki→Roam→Obsidian；局限部分标注 AI 推断待验证）
- 更新：entities/obsidian（背景补机制详解链接）、concepts/note-tool-landscape（国外开源节补交叉引用）、index.md
- 关键澄清入库：图谱不需要反向索引也能画（边集合=前向链接并集）；"双向"指遍历能力而非双向存储；反向索引主战场是 backlinks 面板而非图谱
- 备注：Obsidian 官方文档待剪藏，已登记于页面"依据来源"

## [2026-08-23] schema | 新增 /backfill 回填命令

- 新建 `.claude/skills/backfill/SKILL.md`：将 query 工作流第 4 步固化为可 `/backfill` 触发的项目级技能（盘点→归类→写入→交叉引用→index/log 同步→汇报，含铁律边界）
- CLAUDE.md query 工作流第 4 步补一行命令指针（应用户要求将回填操作命令化，随仓库版本控制）

## [2026-08-23] schema | /backfill 命令更名为 /llm-wiki-backfill

- `.claude/skills/backfill/` → `.claude/skills/llm-wiki-backfill/`，SKILL.md frontmatter name 与 description 同步更新
- CLAUDE.md query 工作流指针同步更名
- 更名理由：命令名携带库标识，避免与其他项目/工具的通用 "backfill" 语义冲突
