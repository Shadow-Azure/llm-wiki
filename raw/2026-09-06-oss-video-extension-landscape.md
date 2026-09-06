---
type: clip
source:
  - "https://api.github.com/repos/{JefferyHcool/BiliNote, JimmyLv/BibiGPT-v1, gaogaotiantian/biliscope, ChatGPTBox-dev/chatGPTBox, the1812/Bilibili-Evolved, wendy7756/AI-Video-Transcriber, Rimagination/bili-note, Yuiffy/BiliGPT, kazuki-sf/YouTube_Summary_with_ChatGPT, 2951121599/Bili-Insight} (star/license/pushed 实测于 2026-09-06)"
  - "https://github.com/JefferyHcool/BiliNote README.md（zread 全文抓取，2026-09-06）"
title: "开源浏览器扩展/工具态在线视频分析项目格局（B站/YouTube 方向，star 数实测）"
author: 各 repo（见正文）
accessed: 2026-09-06
via: GitHub API 实测 + zread README 全文 + web 搜索定位
clip_note: 上轮"AI 视频助手格局"研究（2026-09-05）的聚焦补充：扩展层的开源一侧。star 数为 2026-09-06 GitHub API 实测值，会漂移。
---

# 实测数据（GitHub API，2026-09-06）

| repo | ⭐ | license | 最后推送 | 形态 |
|---|---|---|---|---|
| the1812/Bilibili-Evolved | 30,393 | NOASSERTION（自定义） | 2026-09-05 | B站增强脚本（油猴），AI 总结非核心 |
| ChatGPTBox-dev/chatGPTBox | 10,757 | MIT | 2026-09-03 | 通用 ChatGPT 集成扩展（含视频总结） |
| JefferyHcool/BiliNote | 7,235 | MIT | 2026-09-01 | 本地后端 + 浏览器插件 + 桌面端（v2.4.0） |
| JimmyLv/BibiGPT-v1 | 6,204 | GPL-3.0 | 2026-05-04 | web app 开源版（BibiGPT 商业版前身） |
| wendy7756/AI-Video-Transcriber | 3,249 | Apache-2.0 | 2026-08-23 | web 工具（30+ 平台转录+总结） |
| kazuki-sf/YouTube_Summary_with_ChatGPT | 866 | MIT | 2023-01（停更） | 纯 Chrome 扩展（YouTube） |
| gaogaotiantian/biliscope | 827 | MIT | 2025-06-26 | B站专用扩展（悬停卡片） |
| Rimagination/bili-note | 281 | MIT | 2026-06-25 | 学习向 Markdown 笔记（含弹幕） |
| Yuiffy/BiliGPT | 149 | GPL-3.0 | 2023-08（停更） | B站视频总结 |
| 2951121599/Bili-Insight | 78 | 无 | 2023-07（停更） | Chrome 悬停洞察 B站 |

# BiliNote README 关键事实（全文剪藏节录）

- **形态演进**：v2.1.0 起提供浏览器插件（Chrome/Edge/Firefox **MV3**）——工具栏 popup、视频页悬浮按钮、右键菜单、侧边栏（Markdown / 思维导图 / AI 问答）四件套；另有 Tauri 桌面客户端（Windows/macOS）与 Docker 自托管。
- **B站字幕优先策略**（原文）："插件在用户浏览器里直接抓字幕（带本地登录态 cookie），跳过后端音频转写"；非插件场景走后端 `BilibiliSubtitleFetcher`（player API 拿字幕）作为 yt-dlp 兜底。
- **转写引擎可选**：Fast-Whisper / MLX-Whisper（本地）+ Groq / BCut（必剪）/ 快手（在线）；LLM 自配 OpenAI / DeepSeek / Qwen；另有"多模态视频理解"开关。
- **笔记能力**：结构化 Markdown、自动截图插入、原片跳转链接（时间戳关联原视频）、多版本记录；v2.0.0 起基于 RAG 的笔记 AI 问答（支持 Function Calling，模型可主动查询原文数据；RAG 索引含视频元信息）。
- **商业化**：BiliNote Pro 在线版（bilinote.app）+ 付费一对一搭建服务——开源核心 + 云端托管的双轨。
- 部署依赖：ffmpeg 必须；本地 whisper 默认 tiny（~75MB）可升档；GPU 加速走 faster-whisper + CUDA 12.4。

# 搜索过程发现（未深挖，登记为线索）

- B站视频总结插件的技术原理讨论（V2EX t/1021463）：ffmpeg 提取音频 + 开源 Whisper ASR 转写——无字幕视频的通用兜底路线。
- "Video to Text Assistant"（Chrome/Edge，MIT，商店在架）：字幕提取 + DeepSeek/OpenAI + **Obsidian 导出**——与本库 KM 场景直接相关，商店页未剪藏。
- VideoSeek AI（Firefox，MPL 2.0）：总结+思维导图+字幕，支持 YouTube/B站/TikTok。
- ElMo Chat：免费闭源扩展（B站/YouTube 分段总结+思维导图），LINUX DO 社区推荐——非开源，仅格局参照。
