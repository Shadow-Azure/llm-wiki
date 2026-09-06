---
type: source
created: 2026-09-05
updated: 2026-09-05
sources:
  - "raw/2026-09-05-hkuds-videorag-vimo.md"
---

# HKUDS VideoRAG / Vimo（2025-02 论文，KDD'2026）

**一句话**：开源"chat with your videos"双通道 RAG 框架（多模态知识图谱 + ImageBind 分层上下文编码），并已产品化为 Electron+Python 桌面应用 Vimo——开源世界最接近"AI 原生视频对话应用"的成品，但 Beta 安装包仍是 Coming Soon。

## 核心要点

- **形态**：拖拽导入 MP4/MKV/AVI → 自然语言提问；支持多视频同时分析、"Find specific moments and scenes"、导出洞察与引用——[[wiki/concepts/ai-video-assistant-landscape|AI 视频助手格局]]中开源桌面侧的代表。
- **成熟度**：发行版未发布（macOS Apple Silicon Beta 优先筹备，Win/Linux 随后），当前只能源码运行（conda + torch + ImageBind + Flask 后端，pnpm 前端，仅 macOS M1 验证过）。
- **架构是"图 RAG"而非 ASR-first**：nano-graphrag/LightRAG（跨视频知识图谱）+ ImageBind（多模态表征）+ neo4j/hnswlib；README 未提及任何 Whisper 类 ASR 组件——与 OmAgent 的 ASR-first 管线形成路线对照。
- **效果自报**：Video-MME long 轨 60.2%（对照 MiniCPM w/ subs 56.3%）；更长上下文定位（数百小时、单卡 RTX 3090）。
- 无播放器内嵌、无时间戳跳转 UI 声明——"上传-对话"形态，不是"播放器+AI"。

## 值得注意的细节

- 作者优先权声明"首个专为超长上下文视频设计的 RAG 框架"——2024 年已有同类工作（如 ECCV VideoAgent），"首个"仅限其特定定位。
- LongerVideos 基准（164 视频 / 134.6 小时 / 602 查询）可作为后续评测参考。

## 后续线索

- Vimo Beta 发布后实测其时间戳定位与多视频分析的真实体验。
- VideoRAG 论文的双通道实现细节（图谱如何对齐视觉特征）未深挖。
