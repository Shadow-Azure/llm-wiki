---
type: entity
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-04-04-karpathy-llm-wiki.md"
---

# Obsidian

**一句话身份**：本地 Markdown 知识库（vault）的查看器与编辑器——[[wiki/concepts/llm-wiki|LLM Wiki]] 模式中人类一侧的"IDE"。

## 背景

- 本质是把一个本地文件夹渲染成图谱化知识库：`[[wikilink]]` 双向链接、关系图谱、frontmatter、2000+ 社区插件。机制详解（反向索引、图谱诊断信号等）见 [[wiki/concepts/bidirectional-links|双向链接与关系图谱]]。
- 闭源软件：个人使用免费、商用付费；Sync / Publish 为订阅服务（[官方 roadmap](https://obsidian.md/roadmap/)）。
- 2025 年 1.10 引入 **Bases**（类 Notion 数据库视图，含 Bases API）；**官方 Web Clipper**（开源）剪藏网页直接落为 Markdown，支持自定义 AI prompt。
- 创始团队为华人夫妻档（此前做 Dynalist），公司注册于加拿大蒙特利尔。※ 库外常识，待补充库内来源。

## 与本库主题的关联

- Karpathy 原文的工作画面：一边开 LLM agent、一边开 Obsidian 实时浏览改动——"Obsidian 是 IDE，LLM 是程序员，wiki 是代码库"（见 [[wiki/sources/karpathy-llm-wiki|Karpathy《LLM Wiki》]]）。
- 关键认识：LLM Wiki 模式**不依赖 Obsidian 本身，依赖的是"纯文本本地文件"**——LLM 直接读写文件系统，不经任何 API；Obsidian 是恰好把 wikilink + frontmatter 渲染得最好的查看器。
- 本库工具链约定（见 [CLAUDE.md](../../CLAUDE.md)）：Web Clipper 剪藏来源入 `raw/`、附件本地化到 `raw/assets/`、graph view 观察 wiki 形态、Dataview 查 frontmatter。

## 生态位

- **最重要的单一特性**：笔记 = 本地纯 Markdown 文件。数据开放、零锁定、git/脚本/agent 可直接操作；双链与插件都只是这层之上的视图。"你的数据是开放的，软件是闭源的"是社区共识性表述（※ 检索来源为 HN/Reddit 讨论，待剪藏）。
- AI 插件生态（官方本体至今无内置 AI）：Smart Connections（本地 embedding，相关笔记浮现，数据不出机器）、Copilot（vault 问答 + agent 工作流，可配 LM Studio/Ollama 全本地）、Smart Composer、Text Generator（[2026 插件盘点](https://www.shadow.do/blog/best-ai-plugins-for-obsidian-2026)）。
- 国内外同类与开源协议盘点见 [[wiki/concepts/note-tool-landscape|笔记工具格局]]。

## 相关页面

- [[wiki/sources/karpathy-llm-wiki]]
- [[wiki/concepts/llm-wiki]] · [[wiki/concepts/wiki-vs-rag]]
- [[wiki/concepts/note-tool-landscape]]
