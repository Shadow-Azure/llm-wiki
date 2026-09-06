---
type: source
created: 2026-09-05
updated: 2026-09-05
sources:
  - "raw/2026-09-05-omagent-video-understanding.md"
---

# OmAgent / video_understanding 示例（EMNLP 2024）

**一句话**：开源多模态 agent 框架（Apache-2.0，~2.7k stars），其视频理解示例是"ASR + 场景切分 + 抽帧 + 向量索引 + agent 任务树"教科书管线的最完整可运行开源实现。

## 核心要点

- **预处理**：OpenAI ASR 转写 → scenedetect 场景边界检测切块 → 按间隔（默认 5s）抽帧 → MLLM 给每个场景块写详细摘要 → 写入向量库（Milvus，text-embedding-3-large 3072 维）。
- **问答**：向量检索 → 抽取相关片段近似 start/end 时间戳 → 构建 Divide-and-Conquer 任务树执行。
- **Rewinder 工具**是"AI 代替用户看视频"的关键细节：agent 按秒级起止时间回滚视频重新抽帧（至多 10 帧）交给 VLM 细粒度分析——时间戳定位在 agent 内部完成，用户不用拖进度条。
- **交互形态是 Gradio 网页**（左上传/右提问）与 CLI：无播放器内嵌 AI、无点击跳转 UI——开源侧最强 video agent 也不做嵌入式播放器形态（对空位假设的支持证据）。

## 值得注意的细节

- 工程栈重：Redis（状态）+ Milvus（长期记忆）+ Conductor（工作流编排）——个人工具级复用需裁剪。
- 自认局限："agent may give the wrong or vague answer, especially some questions are related the name of the characters in the video"（人物身份类问题仍是弱项）。

## 后续线索

- DnC 任务树的实现质量（对比单循环 ReAct 的实际增益）值得读 DnC.md 与论文实验节。
- 该框架与 [[wiki/concepts/dsh-agent-loop|dsh agent loop]] 的任务树/工具调用对照是潜在概念页。
