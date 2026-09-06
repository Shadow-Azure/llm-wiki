---
type: source
created: 2026-09-06
updated: 2026-09-06
sources:
  - "raw/2026-09-06-bilinote-architecture.md"
---

# BiliNote（JefferyHcool，v2.4.5 @ 90940fd，MIT，2026-09-06 本地实读）

**一句话**：以视频为一等公民的 AI 笔记生成器——"链接→结构化 Markdown 笔记"的 Python 全栈实现，B站/YouTube 字幕优先策略 + yt-dlp 兜底 + 5 转写引擎 + chromadb RAG，四种交付形态（Web/桌面/插件/Docker）共享一个 FastAPI 后端。

## 核心要点

- **管线**（CLAUDE.md 原文）：URL → 任务队列 → 下载 → FFmpeg 抽音频 → 转写 → LLM 笔记 → 前端轮询 → Markdown + 思维导图。
- **字幕优先是编排层决策**：NoteGenerator 先试 `download_subtitles`，有字幕且不要截图/视频理解则 `skip_download=True` **完全不下载视频**。
- **B站通道三级**：①插件在浏览器内带 cookie 抓字幕（logic/bilibili-subtitle.ts）；②后端直拉 player/wbi/v2 API（后端已签 auth_key 的 subtitle_url，人工 zh-CN > AI zh-CN > 任意 zh；AI 字幕需 SESSDATA）；③yt-dlp 下载（cookie 注入 Netscape cookiefile）。
- **风控对抗自维护**：bilibili_dm_patch.py monkey patch yt-dlp——2026-06 起 B站 playurl 网关要求 dm_img_* 浏览器指纹参数（缺失即 HTTP 412），上游 yt-dlp 不发，BiliNote 注入"dummy but well-formed"值。
- **转写 5 引擎可插拔**：fast-whisper（本地）/ mlx-whisper（Apple Silicon）/ groq / bcut（必剪）/ kuaishou——后两个是白嫖大厂免费转写接口。

## 值得注意的细节

- 名实不符三连：任务执行器类名 `task_serial_executor` 实为 ThreadPoolExecutor（3 worker）；requirements 里 celery 系零引用（死依赖）；CLAUDE.md 说插件 contentScripts 是 placeholder 但字幕抓取实际在 logic/bilibili-subtitle.ts。
- 工程注释文化极佳：YDL_RETRY_OPTS（yt-dlp Python API retries 默认 0 之坑）、dm_patch 的 412 背景考据，均为一级学习材料。

## 后续线索

- video_understanding 开关的实现深度（多模态视频理解到什么程度）未读。
- gpt/request_chunker 长转写分块与 universal_gpt checkpoint 机制值得细读。
