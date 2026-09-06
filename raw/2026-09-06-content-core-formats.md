---
type: clip
source: "https://raw.githubusercontent.com/lfnovo/content-core/main/README.md（main 分支快照）"
title: "Content Core README：支持格式表与引擎配置（open-notebook 的视频能力边界由此库决定）"
author: Luis Novo (lfnovo)
accessed: 2026-09-06
via: web-reader 抓取 raw README 全文
clip_note: 用户追问 open-notebook 能否处理 B站/爱奇艺视频——open-notebook 自身零平台逻辑，全部提取能力委托给 content-core，故本 README 是能力边界的一手证据。关键节选（未改事实）。
---

## Supported Formats（原文表格）

| Category | Formats |
|----------|---------|
| Web | URLs, HTML pages, **YouTube videos**, Reddit posts |
| Documents | PDF, DOCX, PPTX, XLSX, EPUB, HTML, Markdown, plain text |
| Media | MP3, WAV, M4A, FLAC, OGG (audio); **MP4, AVI, MOV, MKV (video)** |

——**视频平台专门支持仅 YouTube 一家**；无 Bilibili / 爱奇艺 / Vimeo / TikTok / 抖音。

## 引擎与配置（原文要点）

- URL 引擎：`auto` / `simple` / `firecrawl` / `jina` / `crawl4ai`；文档引擎：`auto` / `simple` / `docling`（docling 未装 extras 时抛 ConfigurationError，auto 静默回退）。
- `CCORE_YOUTUBE_LANGUAGES`：YouTube transcript 偏好语言（唯一带平台名的配置项）。
- STT：`CCORE_STT_PROVIDER` / `CCORE_STT_MODEL`（经 Esperanto，例：groq/whisper-large-v3）；音频转写并发 `CCORE_AUDIO_CONCURRENCY`（1-10，默认 3）。
- 交付形态：Python API / CLI（`uvx content-core extract`）/ **MCP server**（extract_content + summarize_content 两个工具）/ **Agent Skill**（Claude Code 与 Codex 插件市场可直接安装：`/plugin install content-core@content-core`）。
- LLM 用于 summarize；代理走标准 HTTP_PROXY/HTTPS_PROXY/NO_PROXY。

## 推论边界（标注：以下两条为库内 AI 推断，待源码级验证）

- 非 YouTube 的视频站 URL（B站/爱奇艺等）在 auto 引擎下走通用网页提取链（bs4 → 需 JS 渲染则 firecrawl/jina/crawl4ai），产出是**页面文本**（标题/简介/DOM 文字），不是视频内容本身。
- 本地视频文件（MP4/AVI/MOV/MKV）= 提取音频 → STT 转写为文本。
