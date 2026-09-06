---
type: source
created: 2026-09-06
updated: 2026-09-06
sources:
  - "raw/2026-09-06-oss-video-extension-landscape.md"
---

# 开源在线视频分析项目格局（扩展/工具态，star 实测 2026-09-06）

**一句话**：B站/YouTube 方向的开源高星项目集中在"链接→笔记"应用形态（BiliNote 7.2k、BibiGPT-v1 6.2k），**纯浏览器扩展形态的高星开源项目其实稀疏**（B站专用最高 biliscope 827⭐、通用最高 chatGPTBox 10.7k 但视频总结只是其功能之一）——因为扩展拿不到平台 ASR 数据护城河。

## 核心要点

- **BiliNote（7,235⭐，MIT，活跃）是当前开源标杆**（本地实读：[[wiki/concepts/bilinote-architecture|架构分析]]）：本地 FastAPI 后端 + MV3 浏览器插件（悬浮按钮 + 侧边栏：Markdown/思维导图/AI 问答）+ Tauri 桌面端 + Docker 自托管；已走向"开源核心 + BiliNote Pro 云端"双轨商业化。
- **BiliNote 的 B站字幕优先策略**是关键架构洞察：插件在用户浏览器里**带登录态 cookie 直接抓字幕**（绕过风控），跳过后端音频转写；无字幕才兜底（player API → [[wiki/concepts/yt-dlp|yt-dlp]] → 本地 Fast-Whisper/Groq/BCut 转写）。对任何 B站工具都是可复用的取数通道设计。
- **BibiGPT-v1（6,204⭐，GPL-3.0，2026-05 停更）**：BibiGPT 商业版的开源前身——"开源 v1 引流 + 闭源商业版"的典型路径，与 [[wiki/sources/video-understanding-api-products|BibiGPT 快照]]互为印证。
- **纯扩展侧**：biliscope（827⭐，B站悬停卡片：UP 主信息 + AI 总结）；chatGPTBox（10,757⭐，通用 ChatGPT 集成，视频总结非核心）；YouTube_Summary_with_ChatGPT（866⭐，2023 停更）。B站最大生态 Bilibili-Evolved（30.4k⭐）AI 总结非核心。
- **问答能力下探到开源笔记工具**：BiliNote v2.0.0 起带 RAG 问答 + Function Calling（模型可主动查询原文数据）——上轮格局里"chat with video"只在商业产品，现已进入开源个人工具。

## 值得注意的细节

- B站取数的两条开源通道：字幕抓取（cookie 登录态，廉价但依赖视频有字幕）vs 下载音频+本地 Whisper（通用但重，V2EX 讨论的 ffmpeg+Whisper 是社区标准兜底）。
- Rimagination/bili-note（281⭐）与 JefferyHcool/BiliNote 是**两个不同项目**，名字高度混淆，引用时注意。

## 后续线索

- "Video to Text Assistant"（MIT 扩展，字幕提取 + **Obsidian 导出**）商店页待剪藏——与本库 KM 场景最直接相关的扩展。
- Bilibili-Evolved 插件市场是否已有 AI 总结社区组件未核实。
