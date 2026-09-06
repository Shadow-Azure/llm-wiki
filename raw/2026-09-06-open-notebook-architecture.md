---
type: clip
source:
  - "本地仓 /Users/zn-ice/2026/open-notebook（lfnovo/open-notebook @ 2d2df8a，2026-09-06 实读）"
  - "https://github.com/lfnovo/open-notebook（⭐38,260，MIT，2024-10 创建，2026-09-05 仍在推送）"
  - "https://github.com/lfnovo/content-core（配套提取库：YouTube transcript / MP4·MP3 transcription）"
title: "Open Notebook 关键文件选辑：ADR-004/007 全文 + source.py 摄取图 + 容器编排 + 架构文档要点"
author: Luis Novo (lfnovo) 及贡献者
accessed: 2026-09-06
via: 本地源码实读 + 官方文档剪藏
clip_note: 分析该仓时的关键一手材料选辑。ADR（架构决策记录）原文完整剪藏，代码只留结构注释与关键常量。
---

# 1. ADR-004：长任务跑后台 worker（全文剪藏）

- **Status**: Accepted | **Date**: 2026-07（retroactive）
- **Context**：摄取/嵌入/播客生成耗时秒到分钟级，用户机器差异大；API/UI 必须保持响应（async-first 原则）。
- **Decision**：**长操作一律作为后台 job 在专用 worker 进程跑，绝不 inline 在 API 请求周期里。** 提交即返回 job id（fire-and-forget），状态可查（`/commands/{id}`），失败显式分类（permanent vs retriable），UI 轮询而非阻塞。
- 队列实现刻意作为实现细节：现用 [surreal-commands]（复用已在跑的 SurrealDB，自托管者零新增基础设施，接 ADR-001）；评估迁往 Celery（#381）——若迁移，替换的是实现不是决策。
- **否决方案**：inline 处理（锁死产品数分钟）；FastAPI BackgroundTasks/asyncio（无持久化，进程死任务丢，无状态无重试）；Celery+Redis（上线初就多两个服务）。
- **Consequences**：没有 worker 进程则一切异步任务静默不跑（silent-queue failure）；特性必须按 job 模型设计（重试下近幂等、ValueError=permanent 不重试、状态暴露给 UI）；部署比单体多一个活动部件。

# 2. ADR-007：重提取运行时 opt-in，容器首启安装（全文剪藏）

- **Status**: Accepted | **Date**: 2026-07
- **Context**：Content Core 2.x 带来两个远重于其余栈的可选运行时：**Docling**（layout 感知文档解析+OCR，torch/transformers 几百 MB~多 GB）与 **Crawl4AI local**（捆绑 Chromium ~300MB+系统库）。初版把 Crawl4AI 打进每个镜像，同时 OCR 开关依赖的 Docling 却从未安装——"默认镜像既过重（人人背 Chromium）又缺自己 UI 宣传的运行时"。默认镜像若全捆绑会到 multi-GB，违背 lean/privacy-first/自托管定位（ADR-002：本仓专注知识层，提取委托给 Content Core）。
- **Decision**：**单一精简默认镜像；Docling 与本地 Crawl4AI 经环境变量 opt-in（`OPEN_NOTEBOOK_ENABLE_DOCLING` / `OPEN_NOTEBOOK_ENABLE_CRAWL4AI`），容器 entrypoint 在 api/worker/frontend 启动前阻塞安装，下载缓存到数据卷。** 远程 Crawl4AI server（`CRAWL4AI_API_URL`）免本地安装。
- 细则：**degrade, don't die**（安装失败大声记日志继续启动）；**持久化缓存而非 venv**（UV_CACHE_DIR/PLAYWRIGHT_BROWSERS_PATH/HF_HOME 放 /app/data 数据卷，venv 留镜像层，免疫 Python 版本变更）；后端 capability probe（`GET /api/capabilities`）报告真实可导入性，Settings UI 禁用不可用引擎——UI 永不宣传不存在的运行时。
- **否决方案**：全捆绑（multi-GB）；另发 "full" 镜像 tag（翻倍发布矩阵）；把整个 venv 持久化到卷（ComfyUI 模式，与镜像 Python 版本耦合，升级即坏）；只 opt-in Docling（仍给人人留 300MB Chromium）。
- **Consequences**：默认镜像重新变小；基础提取（文本 PDF/Office/EPUB/URL bs4·Jina·Firecrawl/音频/YouTube）不变；开 flag 首启慢（分钟级）；离线/气隙环境别开 flag；验证了 ADR-002——因为所有提取都藏在 Content Core 的单一 `extract_content()` 边界后，加 opt-in 安装只动了基础设施 + 一个探测端点，没碰提取代码。

# 3. graphs/source.py：摄取管线（关键段节选）

- LangGraph 图：`START → content_process → save_source →（条件边 fan-out）→ transform_content* → END`；transformation 用 `Send` 做 map（每条规则一个并行分支，结果 `Annotated[list, operator.add]` 归并）。
- **YouTube 语言偏好常量**（源码原文）：默认 11 种——en, pt, es, de, nl, en-GB, fr, hi, ja, **zh-CN, zh-TW**；注释说明 content-core 自身默认只有 en/es/pt，Open Notebook 特意放宽让非英文视频也能解析到 transcript。
- **引擎选择**：URL 引擎 auto/simple/firecrawl/jina/crawl4ai；文档引擎 auto/docling/simple；docling_ocr/formulas/vision 可配；被持久化的引擎在运行时缺失时回退 "auto" 并大声警告（`_usable_engine`，防"数据库里存了不存在的引擎导致全部提取失败"）。
- **音频转写**：从 ModelManager 取默认 STT 模型，把 provider/name 传给 content-core（`audio_provider`/`audio_model`）。
- **软失败哨兵**：content-core 提取失败不抛异常而是返回 `title="Error"` + `content` 以 "Failed to extract content:" 开头——source.py 检测哨兵转抛，让 job 标记失败、source 可重试，而不是把错误串存成"已完成"的 source。
- **YouTube 无字幕报错文案**（原文）："No transcript or subtitles are available. Try configuring a Speech-to-Text model in Settings to transcribe the audio instead."——证实视频处理 = 转录文本，无字幕时靠 STT 兜底。
- content-core 2.x 不再自动删除上传的源文件，delete_source 行为由本仓自己补（os.unlink）。

# 4. 容器编排

- **supervisord.conf**（单容器多进程）：`[api]` uvicorn api.main:app :5055（priority 10）→ `[worker]` `uv run surreal-commands-worker --import-modules commands --max-tasks ${OPEN_NOTEBOOK_WORKER_MAX_TASKS:-5}`（priority 20）→ `[frontend]` bash wait-for-api.sh && node server.js :8502（priority 30；注释解释必须强制 HOSTNAME，因容器运行时注入的 HOSTNAME 会让 Next.js 绑错地址，#994）。
- **supervisord.surrealdb.conf**：single 镜像变体在构建时把 SurrealDB 也并入同一容器（Dockerfile `single` stage）。
- **docker-compose.yml**（默认双容器）：surrealdb service（rocksdb 后端，127.0.0.1:8000 仅本机暴露，注释警告 0.0.0.0 + 默认 root:root 的风险）+ open_notebook service（8502 Web UI / 5055 REST；`OPEN_NOTEBOOK_ENCRYPTION_KEY` 加密数据库里的 API key；数据卷 ./notebook_data:/app/data）。

# 5. docs/7-DEVELOPMENT/content-processing.md 要点（全文剪藏）

- **切块**：内容类型感知的 LangChain splitter（HTML/Markdown 头切分 + RecursiveCharacterTextSplitter）；默认 400 token——刻意低于 BERT 系 embedder 的 512 上限约 20%，缓冲三类误差源（tokenizer 不一致 o200k_base vs WordPiece、splitter 超冲、特殊 token）；大窗口 embedder（OpenAI 8191）可调高；token 基非字符基，overlap 默认 15%。
- **嵌入**：短文本直嵌；长文本切块各嵌后**均值池化**合并（normalize→mean→normalize，numpy）；批量路径每批 50 带重试；空输入 ValueError = permanent 不重试。
- **触发矩阵**：Note.save() 自动提交 embed_note；insight 由命令提交；source 靠显式 `vectorize()`（save 不自动）；全部 fire-and-forget 走 worker——**worker 不跑就什么都不嵌入**。
- **上下文构建**（chat 面板/播客/source-chat 三者共用一个实现）：无缓存层每次重取；token 计数用 tiktoken o200k_base 估算（±5-10%）；超长 source 保留至多 20% 预算给 insights，前缀选择用**缓存二分搜索 + 有界前向验证**对付 BPE 非单调性，装不下则整个省略该项并报告 `source_text_status="omitted_budget"` 而非把提示文本冒充内容。
- **加密**：字段级 Fernet（AES-128-CBC+HMAC-SHA256），密钥来自 `OPEN_NOTEBOOK_ENCRYPTION_KEY`（无默认，未设则凭据存储不可用）；**密钥轮换未实现**——换密钥会孤儿化已加密值。

# 6. 其他实读事实

- 技术栈：后端 Python 3.11（uv + uv.lock）FastAPI/Pydantic v2/Loguru；前端 Next.js ^16.3.4 + React 19 + TS 5 + Tailwind 4（注意：architecture.md 写 "Next.js 15"，package.json 实为 ^16——文档滞后于代码）；编排 langchain 1.x + langgraph 1.x；作者自有依赖四件套 esperanto（18+ provider 抽象）/content-core（提取，另有独立 CLI 与 MCP server）/podcast-creator/surreal-commands + ai-prompter。
- 数据模型（SurrealDB）：notebook / source / source_embedding（分块向量）/ note / chat_session / transformation / source_insight + reference（source→notebook）与 artifact（note→notebook）两种图边；软删（archived）；迁移 .surrealql 编号 + 显式注册进 AsyncMigrationManager，启动自动跑。
- api/routers 20+ 个（notebooks/sources/notes/chat/search/podcasts/transformations/models/credentials/episode_profiles/speaker_profiles/insights/capabilities/commands…）。
- 每个主要目录带 AGENTS.md（agent 协作开发约定）；决策记录 docs/7-DEVELOPMENT/decisions/：ADR-001~008 + PDR-001（single-user-first）+ PDR-002（provider-agnostic-core）。
- 测试 53 个文件（pytest + pytest-asyncio）；质量工具 mypy + ruff + pre-commit。
