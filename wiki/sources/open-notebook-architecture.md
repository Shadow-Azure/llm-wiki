---
type: source
created: 2026-09-06
updated: 2026-09-06
sources:
  - "raw/2026-09-06-open-notebook-architecture.md"
  - "raw/2026-09-06-content-core-formats.md"
---

# Open Notebook（lfnovo，⭐38.3k，MIT，v1.14.0）

# Open Notebook（lfnovo，⭐38.3k，MIT，v1.14.0）

**一句话**：NotebookLM 的开源自托管替代——"多 notebook → 多模态 source → 块化向量化 → 混合搜索/对话/播客"的 Python 全栈实现，架构文档与 ADR 质量罕见地高，是"个人知识库 + 多模型 AI"最完整的开源参考实现之一。

## 核心要点

- **功能面**：多 notebook 组织；source 摄取（50+ 文件类型：PDF/Office/EPUB/URL/YouTube/音频/文本，重运行时 Docling OCR 与 Crawl4AI opt-in）；全文+向量混合搜索；chat（带精细上下文控制）与 ask（规划搜索→检索→综合，SSE 流式）；transformation（自定义 LLM 转换→insight）；1-4 说话人播客生成；18+ AI provider（LLM/embedding/STT/TTS 经 esperanto）；REST API 20+ routers + MCP 集成；字段级加密；多语言 UI（含简繁中文）。
- **五个 LangGraph 工作流**：source（摄取：提取→保存→fan-out 转换）/ chat（上下文对话）/ ask（检索问答）/ transformation / prompt；长任务一律走 surreal-commands worker（ADR-004：绝不 inline）。
- **架构**：三层（Next.js :8502 → FastAPI :5055 → SurrealDB :8000）+ DDD（domain/repository 分离）+ async-first + SSE 流式 + fire-and-forget 队列；单容器 supervisord 管 api/worker/frontend 三进程（single 变体再并入 SurrealDB）。
- **工程文化亮点**：docs/7-DEVELOPMENT/decisions/ 有 8 ADR + 2 PDR，含被否方案与后果；软失败哨兵模式（提取失败转可重试 job 而非存错误串）；capability probe 防 UI 宣传不存在的运行时；token 预算二分截断；每个目录 AGENTS.md（agent 协作开发）。

## 值得注意的细节

- 楔子级设计选择：任务队列建在 SurrealDB 上（surreal-commands）——复用已有 DB，自托管者免 Redis；代价是"忘跑 worker = 一切异步静默不跑"。
- architecture.md 写 Next.js 15 而 package.json 实为 ^16——官方文档滞后于代码的实例。

## 后续线索

- ADR-001（为何选 SurrealDB 而非 Postgres+pgvector）与 ADR-008（notebook 域搜索）未细读。
- esperanto / content-core / surreal-commands 三个作者自有库的实现值得单独研究（provider 抽象与"队列建在 DB 上"模式）。content-core 格式边界另见 [[raw/2026-09-06-content-core-formats|raw 剪藏]]：视频平台专门支持仅 YouTube，本地视频 = 音频转写；其 MCP server 与 Agent Skill（Claude Code 插件市场可装）是本库 agent 可直接接入的提取工具。
