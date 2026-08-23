---
type: overview
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-04-04-karpathy-llm-wiki.md"
---

# 综述

本库是一个**通用个人知识库**，按 [[wiki/concepts/llm-wiki|LLM Wiki]] 模式运行：人收集来源和提问，LLM 编译并维护全部 wiki 页面。

## 当前图景（2026-08-23）

- **规模**：1 个来源，5 个 wiki 页面。处于最早期。
- **已确立的核心认识**：知识管理的胜负手不在检索而在**维护**——RAG 每次查询重新推导、没有积累；把知识编译进互相链接的持久页面，维护成本由 LLM 压到趋近于零，知识才能复利。见 [[wiki/concepts/wiki-vs-rag|编译式知识库 vs RAG]]。
- **本库即方法的自证**：仓库自身就是该模式的实例，方法论源头是 [[wiki/sources/karpathy-llm-wiki|Karpathy 的原文]]。

## 正在形成的主题

- 知识管理 / LLM 与个人工具（目前唯一主题，等待第一批真实领域来源进来后自然分化）

## 开放问题

- Karpathy 原文评论区有大量社区实践（团队规模经验、并发 ingest、人工修正的存活机制），尚未剪藏——值得作为第二个来源。
- Karpathy 提到 "the wiki is a persistent, compounding artifact"，但**复利何时开始递减**（规模、矛盾积累、维护成本回升）原文未展开，暂无库内答案。

## 下一步

1. 剪藏并 ingest 第一篇非方法论来源（任意你感兴趣的领域）
2. 之后每 ~10 次摄入跑一次 lint
