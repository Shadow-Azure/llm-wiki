---
type: clip
source: https://github.com/HKUDS/VideoRAG
title: "VideoRAG: Chat with Your Videos • Vimo Desktop (HKUDS)"
author: HKUDS（港大数据智能实验室）+ 百度（论文作者 Ren Xubin, Xu Lingrui, Xia Long, Wang Shuaiqiang, Yin Dawei, Huang Chao）
accessed: 2026-09-05
via: zread.ai 全文抓取（README.md @ master）
related_paper: arXiv:2502.01549（2025-02 提交，README 标注 KDD'2026 录用）
clip_note: 本次研究（"AI agent + 视频"格局调研）核心来源之一：开源 "chat with your videos" 桌面应用的直接实例。README 全文剪藏如下。
---

<!-- 剪藏正文：README 全文（2026-09-05 快照），除本行外一字未动 -->

<div align="center">
  <picture>
      <img src="cover.png" width="80%" style="border: none; box-shadow: none;" alt="Vimo: Chat with Your Videos">
  </picture>

  <h1>
    <strong>VideoRAG: Chat with Your Videos</strong> • <strong>Vimo Desktop</strong>
  </h1>

  <a href='https://arxiv.org/abs/2502.01549'><img src='https://img.shields.io/badge/arXiv-2502.01549-b31b1b'></a>
  [![Platform](https://img.shields.io/badge/platform-macOS%20|%20Windows%20|%20Linux-lightgrey.svg)]()

  **🎬 Intelligent Video Conversations | Powered by Advanced AI | Extreme Long-Context Processing**

</div>

Vimo is a revolutionary desktop application that lets you **chat with your videos** using cutting-edge AI technology. Built on the powerful VideoRAG framework, Vimo can understand and analyze videos of any length - from short clips to hundreds of hours of content - and answer your questions with remarkable accuracy.

## ✨ Key Features

### For Everyone
- **Drag & Drop Upload**: Simply drag video files into Vimo
- **Smart Conversations**: Ask questions in natural language
- **Multi-Format Support**: Works with MP4, MKV, AVI, and more
- **Cross-Platform**: Available on macOS, Windows, and Linux

### For Power Users
- **Extreme Long Videos**: Process videos up to hundreds of hours
- **Multi-Video Analysis**: Compare and analyze multiple videos simultaneously
- **Advanced Retrieval**: Find specific moments and scenes with precision
- **Export Capabilities**: Save insights and references for later use

### For Researchers
- **VideoRAG Framework**: Access to cutting-edge retrieval-augmented generation
- **Benchmark Dataset**: LongerVideos benchmark with 134+ hours of content
- **Performance Metrics**: Detailed evaluation against existing methods
- **Extensible Architecture**: Build upon our open-source foundation

## 🌟 Why Vimo?

**For Video Enthusiasts & Professionals:**
- **Effortless Video Analysis**: Upload any video and start asking questions immediately
- **Natural Conversations**: Chat with your videos as if talking to a human expert
- **No Length Limits**: Process everything from 30-second clips to 100+ hour documentaries
- **Deep Understanding**: Combines visual content, audio, and context for comprehensive answers

**For Researchers & Developers:**
- **State-of-the-Art Algorithm**: Built on VideoRAG, featuring graph-driven knowledge indexing
- **Benchmark Performance**: Evaluated on 134+ hours across lectures, documentaries, and entertainment
- **Open Source**: Full access to VideoRAG implementation and research findings
- **Scalable Architecture**: Efficient processing with single GPU (RTX 3090) capability

## 🚀 Quick Start of Vimo

### Option 1: Download Vimo App (Coming Soon)

> [!NOTE]
> We are preparing the **Beta release** for macOS Apple Silicon first, with Windows and Linux versions coming soon!

### Option 2: Run from Source Code

**Quick Overview:**
1. Set up the Python backend environment and start the VideoRAG server
2. Launch the Electron frontend application
3. Start chatting with your videos!

## 🔬 VideoRAG Algorithm

VideoRAG introduces a novel dual-channel architecture that combines:

- **Graph-Driven Knowledge Indexing**: Multi-modal knowledge graphs for structured video understanding
- **Hierarchical Context Encoding**: Preserves spatiotemporal visual patterns across long sequences
- **Adaptive Retrieval**: Dynamic retrieval mechanisms optimized for video content
- **Cross-Video Understanding**: Semantic relationship modeling across multiple videos

### Technical Highlights

- **Efficient Processing**: Handle hundreds of hours on a single RTX 3090 (24GB)
- **Structured Indexing**: Distill long videos into concise knowledge representations
- **Multi-Modal Retrieval**: Align textual queries with visual and audio content
- **LongerVideos Benchmark**: 160+ videos, 134+ hours across diverse domains

### Performance Comparison

We also evaluate VideoRAG's QA performance on the Video-MME long video track:

| Video-MME Long Video | MiniCPM-o w/o subs | MiniCPM-o w/ subs | MiniCPM-V w/o subs | MiniCPM-V w/ subs | VideoRAG |
| --- | ---: | ---: | ---: | ---: | ---: |
| Accuracy | 52.2% | 56.3% | 51.8% | 56.3% | **60.2%** |

> Note: The score may show slight fluctuations across runs due to the instability of LLM generation.

## 🧪 LongerVideos Benchmark

| Video Type       | #Collections | #Videos | #Queries | Avg. Duration |
|------------------|-------------|---------|----------|---------------|
| **Lectures**     | 12          | 135     | 376      | ~64.3 hours   |
| **Documentaries**| 5           | 12      | 114      | ~28.5 hours   |
| **Entertainment**| 5           | 17      | 112      | ~41.9 hours   |
| **Total**        | 22          | 164     | 602      | ~134.6 hours  |

## 🙏 Acknowledgement

Vimo builds upon the incredible work of the open-source community:

- **[VideoRAG](https://arxiv.org/abs/2502.01549)**: The core algorithm powering Vimo's intelligence
- **[nano-graphrag](https://github.com/gusye1234/nano-graphrag)** & **[LightRAG](https://github.com/HKUDS/LightRAG)**: Graph-based retrieval foundations
- **[ImageBind](https://github.com/facebookresearch/ImageBind)**: Multi-modal representation learning
- **[uitars-desktop](https://github.com/bytedance/UI-TARS-desktop)**: Desktop application architecture inspiration

---

<!-- 剪藏结束。省略部分：badge 图片、demo 视频嵌入、目录、贡献与引用 BibTeX（原文可见 GitHub）。关键未删改事实：Beta 安装包标注 Coming Soon（macOS Apple Silicon 优先）；Video-MME long 轨数字为 README 原表。 -->
