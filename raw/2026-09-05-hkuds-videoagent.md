---
type: clip
source: https://github.com/HKUDS/VideoAgent
title: "HKUDS/VideoAgent: Open Agentic Video Intelligence — Understanding, Editing, and Generation"
author: HKUDS
accessed: 2026-09-05
via: zread.ai 全文抓取（readme.md @ master）
related_paper: arXiv 2606.23327（README 标注，EMNLP2026）
clip_note: 本次研究核心来源之一："AI 代替用户看视频干活"（理解+剪辑+重制一体）的最完整开源 agent 框架；无任何播放器 UI。README 全文剪藏（省略 badge/图片/demo 链接，未删改任何事实性内容）。
---

<!-- 剪藏正文：readme.md（2026-09-05 快照） -->

**🌟 Comprehensive Video Intelligence: An All-in-One Framework for Understanding, Editing, and Generation**

In the demo video, VideoAgent is used to: articulate user requirements → intent analysis and autonomous tool use & planning → create multi-modal products including detailed workflows → fully automatic generation of video overview.

## 🚀 Key Features

- 🧠 **Understanding Video Content** — in-depth analysis, summarization, and insight extraction from video media.
- ✂️ **Editing Video Clips** — assembling, clipping, and reconfiguring content with seamless workflow integration.
- 🎨 **Remaking Creative Videos** — generative technologies to produce new video content.
- 🔧 **Multi-Modal Agentic Framework**
- 🚀 **Seamless Natural Language Experience** — "pure conversational AI - no complex interfaces or technical expertise required, just natural dialogue with VideoAgent."

功能分解：Video Understanding & Summarization（Video Q&A / Video Summarization）；Video Editing（Movie Edits / Commentary Video / Video Overview）；Video Remaking（Meme Videos / Music Videos / Cross-Cultural Comedy）。

### 竞品能力对照表（README 原表节录）

|  | VideoAgent | Director | Funclip | NarratoAI | NotebookLM |
|---|---|---|---|---|---|
| Beat-synced Edits | ✅ | ✅ | ✅ | — | — |
| Storytelling Video | ✅ | — | — | — | — |
| Video Overview | ✅ | ✅ | ✅ | ✅ | ✅ |
| Meme Video Remaking | ✅ | — | — | — | — |
| Song Remixes | ✅ | — | — | — | — |
| Cross-lingual Adaptations | ✅ | — | — | — | — |
| Video Q&A | ✅ | ✅ | — | — | ✅ |
| Sound Effects Tools | ✅ | — | — | — | — |

## 🌟 System Overview

Three key innovations: **Intent Analysis**（分解显式+隐式子意图，intent-to-agent mapping 精准激活组件）；**Autonomous Tool Use & Planning**（graph-powered workflow generation：节点=工具能力，边=工作流连接；two-step self-evaluation 反馈环自纠错）；**Multi-Modal Understanding**（Storyboard Agent 分析 pre-captioned video material banks，把用户输入分解为视觉+语义对齐的细粒子查询做检索）。

## 🔧 Evaluation（要点）

- Workflow 构建创造力对比 5 个 agent × 3 骨干模型："VideoAgent exhibits superior and more stable creative performance under the Claude 3.7 backbone compared to GPT-4o and Deepseek-v3"。
- 文本→视频检索实验（Recall / Embedding Matching / IoU 三指标）验证时序检索精度。
- 反思轮次越多性能越好："consistent workflow composition success rates of 0.95 across all tested configurations"。

## 🚀 Quick Start（要点）

- 环境：GPU 8GB；Linux, Windows。
- 依赖模型可按功能选装：CosyVoice / fish-speech / seed-vc / DiffSinger（语音生成）、**Whisper**（whisper-large-v3-turbo）、**ImageBind**（imagebind_huge.pth）。

### 按功能的必需模型矩阵（README 原表节录）

| Feature Type | Required Models |
|---|---|
| Cross Talk（英stand-up→中相声） | CosyVoice, Whisper, ImageBind |
| MAD TTS | fish-speech |
| MAD SVC | DiffSinger, seed-vc, Whisper, ImageBind |
| Rhythm | Whisper, ImageBind |
| Comm（解说视频） | CosyVoice, Whisper, ImageBind |
| News | CosyVoice, Whisper, ImageBind |
| **Video QA/Summarization** | **Whisper** |

### LLM Configuration（原文注释节录）

```yaml
# Applicable scenarios and LLM configuration
# Claude is required as it powers the Agentic Graph Router
llm:
  # Video Remixing/TTS/SVC/Stand-up/CrossTalk
  deepseek_api_key: ""
  # Agentic Graph Router/TTS/SVC/Stand-up/CrossTalk
  claude_api_key: ""
  # Video Editing/Overview/Summarization/QA/Commentary Video
  gpt_api_key: ""
  # MLLM for caption and fine-grained video understanding
  gemini_api_key: ""
```

### Usage

```bash
python main.py
# The console will output:
User Requirement: ...
```

---

<!-- 剪藏结束。注：当前 LLM selections are optimized for each function（README 原文）。 -->
