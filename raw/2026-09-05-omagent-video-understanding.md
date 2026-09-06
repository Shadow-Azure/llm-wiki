---
type: clip
source: https://github.com/om-ai-lab/OmAgent
title: "OmAgent: A Multi-modal Agent Framework for Complex Video Understanding with Task Divide-and-Conquer"
author: om-ai-lab（Zhang Lu, Zhao Tiancheng, Ying Heting, Ma Yibo, Lee Kyusong）
accessed: 2026-09-05
via: zread.ai 全文抓取（README.md + examples/video_understanding/README.md @ master）
related_paper: arXiv:2406.16620（EMNLP 2024）
clip_note: 本次研究核心来源之一：最贴近"ASR + 场景切分 + 抽帧 + 向量索引 + agent 任务树"教科书管线的开源框架，附可直接运行的 video QA 应用。双 README 合并剪藏，省略 badge/图片链接。
---

<!-- 剪藏正文一：主 README（节选，2026-09-05 快照） -->

# OmAgent — Build Multimodal Agents with Ease

OmAgent is a Python library for building multimodal language agents with ease.
- Wraps complex engineering (worker orchestration, task queue, node optimization) behind a simple interface.
- Reusable agent components; graph-based workflow orchestration engine + various memory types.
- Native multimodal support: VLM models, real-time API, computer vision models, mobile connection.
- Supports local model deployment via Ollama / LocalAI; fully distributed architecture, Lite mode available.

Install: `pip install omagent-core`（Python >= 3.10）

Example projects:
1. **Video QA Agents** — "Build a system that can answer any questions about uploaded videos with video understanding agents. We provide a gradio based application."（论文 arXiv:2406.16620）
2. Mobile Personal Assistant
3. Agentic Operators（ReAct / CoT / SC-Cot 等算子对比）

<!-- 剪藏正文二：examples/video_understanding/README.md（关键全文） -->

# Video Understanding Example

This example implements a video understanding task workflow based on the DnC workflow, which consists of following components:

1. **Video Preprocess Task**
   - Preprocess the video with audio information via speech-to-text capability
   - It detects the scene boundaries, splits the video into several chunks and extract frames at specified intervals
   - Each scene chunk is summarized by MLLM with detailed information, cached and updated into vector database for Q&A retrieval
   - Video metadata and video file md5 are transferred for filtering

2. **Video QA Task**
   - Take the user input question about the video
   - Retrieve related information from the vector database with the question
   - Extract the approximate start and end time of the video segment related to the question
   - Generate video object from serialized data in short-term memory (stm)
   - Build init task tree with the question to DnC task

3. **Divide and Conquer Task**
   - Execute the task tree with the question

The system uses Redis for state management, Milvus for long-term memory storage and Conductor for workflow orchestration.

## Configuration（预处理超参，原文要点）
- `scene_detect_threshold`: 场景检测阈值，min value means more scenes（默认 **27**）
- `frame_extraction_interval`: 抽帧间隔（默认 **5** 秒）
- `stt.endpoint` / `stt.api_key`: 语音转文字服务，默认使用 **OpenAI ASR service**
- 文本编码器默认 OpenAI `text-embedding-3-large`，**3072** 维（MilvusLTM 需对应设置 dim）

## Running
- `python run_webpage.py`：网页用法——"First, select a video or upload a video file on the left; after the video preprocessing is completed, ask questions about the video content on the right."
- `python run_cli.py`：CLI 用法——先输入视频路径预处理入库，然后输入问题作答。

<!-- 剪藏结束。省略部分：Troubleshooting（Redis/Milvus 维度检查等）、容器配置细节。 -->
