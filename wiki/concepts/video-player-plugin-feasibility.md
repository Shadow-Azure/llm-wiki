---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: []
---

# 本地视频播放器插件（Obsidian）可行性

**定义**：在 Obsidian 内自研本地视频播放器插件的可行性结论——**技术上完全可行（本库 demo 已验证最小形态），合规干净（纯本地无披露义务），差异化空间在 AI 原生闭环**。

## 技术通道

- 渲染：自定义 `ItemView` 放 `<video>`，或 markdown post-processor 接管 `![[xxx.mp4]]` 渲染；插件在 Electron renderer 有完整 DOM（见 [[wiki/concepts/obsidian-plugin-development]]）。
- 本地文件：`Vault.getResourcePath(TFile)` 返回 `app://` URL 直接作 `src`；vault 外路径桌面端用 Node fs（须 `isDesktopOnly: true` + README 披露 vault 外访问）。
- 原生基线：Obsidian 原生支持嵌入 `.mp4 / .webm / .mov / .mkv / .ogv` 内联播放；**实际解码取决于 Chromium**——H.264 / VP8 / VP9 / AV1 可靠，HEVC 常见"只声无画"（随系统/硬件波动），H.264 的 MP4 最稳。〔官方 help 与论坛帖，待剪藏〕
- 库内证据：demo 分支 `q5-video-learning.md` 已跑通"视频内嵌播放 + AI 内容地图 + 批注讨论"（素材 `raw/assets/Q5_大作文插板工程_难度5.mp4`；.gitignore 约定视频仅本地保留，见 [[wiki/log]] 2026-08-23 schema 条目）。

## 功能阶梯

| 层级 | 内容 | 工作量 |
|---|---|---|
| L0 | 原生 `![[video.mp4]]` 嵌入 | 零 |
| L1 MVP | 自定义视图/控制条、倍速、A-B 循环、记住播放位置（`data.json` 或 frontmatter）、时间戳链接（`t=hh:mm:ss` 点击即 seek） | 几天 |
| L2 | 字幕（.srt→.vtt 挂 track）、章节书签、播放列表/媒体库视图（可接 Bases）、缩略图（canvas 抓帧） | 1–2 周 |
| L3 AI | 本地转写（spawn whisper 子进程，[[wiki/entities/claudian|Claudian]] 的 CLI spawn 先例）、转写→内容地图→批注联动（demo 方向） | 数周 |
| L4 硬骨头 | HEVC/任意 MKV 流：WebCodecs + JS demuxer 自渲染，或运行时下载 ffmpeg | 不建议首发 |

## 政策合规检查（对照 [[wiki/concepts/obsidian-plugin-distribution|分发政策]]）

- 纯本地播放 = 无网络使用 → **无披露义务**，最干净的插件类别 ✓
- Node API（fs / child_process / whisper）→ `isDesktopOnly: true`；纯 Web API 可兼顾移动端（移动端资源路径行为有差异〔AI 推断，待验证〕）
- **二进制分发限制**：官方渠道 release 仅允许 `main.js / manifest.json / styles.css` 三资产——原生二进制不能随插件分发。出路：wasm bundle 进 main.js（Smart Connections 先例）；或引导用户自备/运行时下载（[[wiki/concepts/obsidian-plugin-networking|灰色地带]]，须披露）〔AI 推断，待验证〕

## 竞品与差异化

- 头部：**Media Extended**（时间戳笔记、YouTube、截图、transcript；[官网](https://mx.pkmer.net/) / [社区页](https://community.obsidian.md/plugins/media-extended)），字幕排版由 Subtitles MD 补位——均**待剪藏**。
- 差异化空档：现有竞品是"播放器 + 笔记"，不是 AI 工作流——"转写→内容地图→批注"闭环（demo 已验证形态）是明确空位。

## 建议路径

先装 Media Extended 试用一周 → 确认差距是否在 AI 集成 → 若是，自研 MVP（L1 + L3 的转写），全部用文档化 API，桌面优先。

## 依据来源与后续线索

- 库内：[[wiki/concepts/obsidian-plugin-development]] / [[wiki/concepts/obsidian-plugin-distribution]] / [[wiki/concepts/obsidian-plugin-networking]]（raw 支撑）+ demo 分支实践
- 外部待剪藏：Obsidian help [file formats](https://obsidian.md/help/file-formats)、[HEVC 播放问题帖](https://forum.obsidian.md/t/embedded-videos-encoded-with-h265-hevc-dont-play-video-only-audio/60718)、Media Extended 文档
- L4 的编解码与二进制分发细节为 AI 推断，动手前需实验验证
