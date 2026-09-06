---
type: clip
source:
  - "本地仓 /Users/zn-ice/2026/BiliNote（JefferyHcool/BiliNote @ 90940fd，v2.4.5，2026-09-06 实读）"
  - "https://github.com/JefferyHcool/BiliNote"
title: "BiliNote 关键源码选辑：CLAUDE.md 架构节 + B站字幕直拉 API + yt-dlp 风控补丁 + 编排核心"
author: JefferyHcool 及贡献者
accessed: 2026-09-06
via: 本地源码实读
clip_note: 分析该仓时的关键一手材料。注释原文完整剪藏（这些注释本身就是高质量文档）。
---

# 1. 根 CLAUDE.md 架构节（节录）

- 三客户端同后端：FastAPI backend :8483；React 19 + Vite 前端 :3015（dev 代理 /api）；Vue 3 MV3 浏览器插件（连同一后端，CORS 正则接受 chrome-extension:// 与 moz-extension://）；Tauri 桌面 = PyInstaller 后端二进制 + 前端壳。
- Core Workflow（原文）："User submits URL → task queued → download video → extract audio (FFmpeg) → transcribe (Whisper/Groq/etc) → generate notes (LLM) → frontend polls for completion → display Markdown + mind map."
- services：`note.py` NoteGenerator 编排全管线；`task_serial_executor.py` 任务队列；`chat_service.py` + `chat_tools.py` + `vector_store.py` = RAG 问答 + Function Calling（索引转写与视频元数据）；`cookie_manager.py` 按平台存 cookie 注入 yt-dlp。
- downloaders：bilibili / youtube / douyin / kuaishou / local，共享 base.py 接口。transcriber：fast-whisper / groq / bcut / kuaishou / mlx-whisper，工厂 transcriber_provider.py；YouTube 路径优先既有字幕、有字幕即跳过音频下载。
- db：SQLite + SQLAlchemy DAO 模式（provider_dao / model_dao / video_task_dao），库文件 backend/app/db/bili_note.db 首启自动初始化。
- events/：Blinker 信号系统做后处理（转写后临时文件清理），lifespan 注册；ffmpeg_helper.ensure_ffmpeg_or_raise 启动强制检查。
- 前端轮询 useTaskPolling 每 3 秒；插件 popup 内 3 秒轮询（MV3 service worker 有意保持薄）。

# 2. downloaders/bilibili_subtitle.py 头注释（全文）

```
直接调用 B 站 player API 拿字幕，绕过 yt-dlp。

流程：
1. 从 URL 提 BV id（已有 utils.url_parser.extract_video_id）
2. 从 URL 提 p 参数（分 P 序号）
3. GET /x/web-interface/view?bvid=BVxxx&p=N → 拿第 N 集的 cid
4. GET /x/player/wbi/v2?bvid=...&cid=... → 返回 data.subtitle.subtitles[]
   每条带 subtitle_url（B 站后端已经签好 auth_key 的完整地址）
5. 按优先级（人工 zh-CN > AI zh-CN > 任意 zh > 任意非空）选一条
6. fetch subtitle_url → JSON {body:[{from,to,content,...}]}
7. 解析为 TranscriptResult

AI 字幕需要登录态 cookie（SESSDATA）；通过 CookieConfigManager 注入。
```

# 3. downloaders/bilibili_dm_patch.py 头注释（全文）——风控对抗鲜活样本

```
Patch yt-dlp's Bilibili extractor to inject the dm_img_* / web_location
risk-control parameters required by Bilibili's wbi/playurl gateway.

Background
----------
Around 2026-06 Bilibili's ``x/player/wbi/playurl`` gateway began rejecting
requests that omit the browser fingerprint params
``dm_img_list`` / ``dm_img_str`` / ``dm_cover_img_str`` / ``dm_img_inter`` +
``web_location`` with **HTTP 412**. Current yt-dlp (incl. the latest release)
does not send these for the playurl endpoint, so any video whose web page does
*not* inline ``playinfo`` — forcing yt-dlp onto the API path — fails with 412.
Refreshing cookies does not help; the params themselves are missing.

We inject dummy-but-well-formed values *before* wbi signing. The value shapes
deliberately mirror yt-dlp's own usage of the same fields for the
``x/space/wbi/arc/search`` endpoint (``BiliBiliSpaceIE``), which is the only
place upstream currently sends them.
```

# 4. downloaders/base.py YDL_RETRY_OPTS 注释（全文）——yt-dlp Python API 之坑

```
# yt-dlp 的 `retries` 默认值(10)是命令行参数解析器给的，Python API 不套用它：
# 不显式设置时 HttpFD 拿到的是 `self.params.get('retries')` == None，而
# `RetryManager.__init__` 做的是 `self.retries = _retries or 0`——也就是
# 一次都不重试。任何一次网络抖动（例如 B 站 CDN
# upos-sz-mirror*.bilivideo.com 读超时）都会让整个笔记任务直接失败。
#
# 这里的值偏保守：笔记任务是用户在前台等的，重试太多不如早点失败让用户重来。
YDL_RETRY_OPTS = { "retries": 3, "fragment_retries": 3, "socket_timeout": 30 }
```

# 5. downloaders/youtube_subtitle.py 头注释

```
通过 youtube-transcript-api 获取 YouTube 字幕。
优先人工字幕，其次自动生成字幕。不依赖 yt_dlp，无需下载任何文件。
```
（构造时若配置了全局代理，给 youtube-transcript-api 套带 proxies 的 requests.Session——"否则国内拉字幕同样会超时"）

# 6. 编排核心 services/note.py generate()（关键行）

- L129 `_get_downloader(platform)` → L158 `transcript = downloader.download_subtitles(video_url)`（先试字幕）→ L175 `need_full_download = not has_transcript or screenshot or video_understanding` → L188 `skip_download=not need_full_download`——**有字幕且不要截图/视频理解时完全不下载视频**。

# 7. 其它实读事实

- services/task_serial_executor.py：类名 `ConcurrentTaskExecutor`（"使用线程池并发执行任务，替代原来的串行锁"），ThreadPoolExecutor 默认 3 worker（TASK_MAX_WORKERS 可调）；`SerialTaskExecutor = ConcurrentTaskExecutor` 向后兼容别名——**名字还是"串行"，实现已是并发**。
- vector_store.py：chromadb PersistentClient，每任务一个 collection（task_id 命名），重建幂等（先 delete 再建）。
- docker-compose.yml 三容器：backend（构建 build 非 prebuilt 镜像；BASE_REGISTRY/APT_MIRROR/PIP_INDEX 国内镜像 build-args；**绑定挂载整个 ./backend:/app**——SQLite/配置/截图/上传全持久化宿主机；mem_limit 4g 防 whisper 首载 OOM；healthcheck /api/sys_health）+ frontend（expose 80 内部）+ nginx（唯一对外端口，depends_on backend service_healthy）。
- requirements.txt 含 celery/billiard/amqp，但全仓 Python 代码**零引用**——死依赖。
- 测试 24 个文件，针对性极强：test_ydl_retry_behavior / test_bilibili_dm_patch / test_youtube_metadata_only / test_whisper_models / test_request_chunker / test_model_fallback 等。
- bilibili_downloader.py：yt_dlp.YoutubeDL + cookie_manager 取 B站 cookie 写 Netscape 格式临时文件注入 cookiefile。
