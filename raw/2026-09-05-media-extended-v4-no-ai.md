---
type: clip
source:
  - "Media Extended v4.0 官方发布说明 / 开发者公告（2025-09-17，primary）"
  - "Obsidian 官方论坛相关讨论（2025-02-17，forum，见 [[raw/2026-09-05-player-embedded-ai-snapshot]] 快照 6）"
title: "Media Extended v4.0 发布说明快照：确认无任何 AI 能力"
accessed: 2026-09-05
via: deep-research workflow extractor 论断提取（官方 release notes，primary）
clip_note: 库内竞品关键证据：直接确认 [[wiki/concepts/video-player-plugin-feasibility]] 中 Media Extended 的"播放器+笔记、无 AI"定性在 2025-09 仍然成立，且 AI 是其转闭源的动因而非已有能力。
---

# Media Extended v4.0（2025-09-17 发布）关键事实

- v4.0 **不含任何 AI 功能**；AI 摘要与章节检测仅处于 "exploring" 阶段的 roadmap 条目；开发者明确把"交付 AI 摘要"列为未来转闭源（closed-source transition）的动机之一。
- v4.0 **没有 ASR/转写能力**：自动字幕生成仅在"调查中"（under investigation）；视频索引依赖用户自备字幕文件。
- v4.0 已发布的 transcript 功能是**人工驱动**的时间戳导航：要求预存 .srt/.vtt/.ass 字幕，用户点击行跳转或复制带时间戳引用——无 ASR、无 embedding 索引、无问答。

# 对本库的含义

- [[wiki/concepts/video-player-plugin-feasibility]] 的竞品定性（"播放器 + 笔记，不是 AI 工作流"）截至 2025-09-17 官方一手证据成立。
- 差异化窗口仍在，但边界要改：平台侧（YouTube/B站原生）与浏览器扩展侧（LunaNotes 等）的播放器 AI 已拥挤，空位收窄为"**本地视频库 + 个人知识库 + 播放器内嵌 AI**"（详见 [[wiki/concepts/ai-video-assistant-landscape]]）。
