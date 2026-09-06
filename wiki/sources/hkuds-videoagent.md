---
type: source
created: 2026-09-05
updated: 2026-09-05
sources:
  - "raw/2026-09-05-hkuds-videoagent.md"
---

# HKUDS VideoAgent（2025-07 repo，~1.8k stars，EMNLP2026）

**一句话**：把视频理解（Q&A/摘要）、剪辑、重制（remix）统一在一个自然语言驱动的多智能体框架里的最完整开源"AI 代替用户看视频干活"实现——但纯 CLI 批处理，无任何播放器 UI。

## 核心要点

- **编排创新**：不是单循环 tool calling，而是"意图分析（显式+隐式子意图）→ 图驱动工作流生成（节点=工具能力，边=连接）→ 两步自评估反馈环"；README 原文硬性要求 **Claude 作为 Agentic Graph Router 骨干**，DeepSeek/GPT/Gemini 分别承担 remix/剪辑/caption。
- **"对视频提问"实质是 transcript QA**：按功能的必需模型矩阵显示 Video Q&A/Summarization **仅需 Whisper**；时序检索/节拍对齐/解说才追加 ImageBind——评估各家"chat with video"深度时的可核查标尺。
- **能力对照表**（README 原表）把 Director（VideoDB）、Funclip、NarratoAI、NotebookLM 拉通对比——NotebookLM 也被归为 Video Q&A 竞品，是商业侧的重要参照。
- 多模态理解走 Storyboard Agent 预 caption 路线（MLLM 对素材库预生成字幕，再分解细粒度子查询检索），ASR=Whisper、embedding=ImageBind。

## 值得注意的细节

- 自评：workflow 组装成功率 0.95（跨数据集/骨干）；Claude 3.7 骨干下创造力表现"superior and more stable"（厂商自评，注意立场）。
- 环境门槛低（8GB GPU，模型按功能选装），但四个 LLM API key 是硬要求。

## 后续线索

- 其 graph workflow 编排与 [[wiki/concepts/dsh-agent-loop|dsh agent loop]] / subagent 变体的对照。
- 与 Director/Funclip/NarratoAI 的逐项对比表可扩展为独立概念页素材。
