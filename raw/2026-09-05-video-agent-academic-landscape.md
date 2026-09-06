---
type: clip
source:
  - "VideoAgent (BIGAI+北大, ECCV 2024): arXiv 2403.11481"
  - "Video-RAG (arXiv 2411.13093, 2024-11, NeurIPS 2025)"
  - "VideoMultiAgents (Panasonic Connect, arXiv 2504.20091, 2025-04; repo 2024-07 创建, MIT)"
  - "NVIDIA 官方博客: An Easy Introduction to Multimodal RAG for Video and Audio (2024-12-16; 注意原任务给的 URL 有拼写错误 404，已用正确 URL developer.nvidia.com/blog/an-easy-introduction-to-multimodal-retrieval-augmented-generation-for-video-and-audio/)"
  - "Video-MME benchmark (arXiv 2405.21075, CVPR 2025; leaderboard 更新至 2025-09-28)"
  - "awesome-video-agents 清单 (2026-05-27 快照, secondary)"
title: "视频理解 agent 学术与参考架构多源快照合集（VideoAgent ECCV / Video-RAG NeurIPS / VideoMultiAgents / NVIDIA 参考架构 / Video-MME / awesome-video-agents）"
accessed: 2026-09-05
via: deep-research workflow extractor 论断提取（论文与官方博客，primary 为主）
clip_note: 本次研究关键证据合集：技术架构模式的学术源头与工程参考架构；含长视频瓶颈的基准证据。
---

# 快照 1：VideoAgent（BIGAI + 北京大学，ECCV 2024，arXiv 2403.11481）

- 两阶段架构：离线"记忆构建"——视频切 2 秒片段、视频 captioning 模型生成事件描述、存文本+视觉特征（SQL 库存对象：类别、CLIP 特征、出现片段）。索引靠视觉 captioning 而非 ASR。
- 推理：LLM 零样本工具调用，仅 4 个工具——Caption Retrieval（按起止片段取最多 15 条描述）、Segment Localization（文本查询→特征相似度定位片段）、Visual Question Answering（video LLM 看短片段）、Object Memory Querying。
- 效果：长视频 QA 上平均 +6.6%（NExT-QA）、+26.0%（EgoSchema），作者称缩小了开源栈与 Gemini 1.5 Pro 的差距。

# 快照 2：Video-RAG（arXiv 2411.13093，NeurIPS 2025）

- Training-free、即插即用：开源外部工具从原始视频抽三类视觉对齐辅助文本（**ASR 转录、OCR、物体检测**），与帧和 query 一起注入现成 LVLM；无微调。
- 前提论断：长视频受上下文窗口限制是 video LLM 的未解难题——"chat with video" 产品必须预索引（转写/切分/检索）而非整段直塞的技术根据。
- 明确是**单轮检索**非迭代 agent 循环：LVLM 被提示一次发出检索请求、三路文本并行抽取、一次性检索；作者以成本为由反对 GPT-based video agent（2024 年的框架，先于开源权重 agent 生态）。

# 快照 3：VideoMultiAgents（Panasonic Connect，arXiv 2504.20091）

- LLM 协调的多智能体：Organizer Agent 协调视觉、文本（caption）、图结构（scene graph）三类专用 agent + 问题导向 caption 生成。
- 纯视觉中心，**无 ASR/转录、无 embedding 索引层**；依赖 GPT-4o 与 Gemini 付费 API。
- 定位是学术基准代码（Intent-QA 79.0% / EgoSchema subset 75.4% / NExT-QA 79.6% zero-shot），无终端用户产品形态。

# 快照 4：NVIDIA "talk to your videos" 参考架构（官方博客，2024-12-16）

- 五段文本化管线：音频摄取（ASR）→ 视频摄取（抽帧 + VLM captioning）→ 音视频信息混合 → 检索器构建 → 答案生成；**刻意把所有模态转成文本**，不用共享多模态 embedding 空间。
- 时间戳即检索元数据：词级 utterance 时间戳与帧时间戳作为 chunk 级元数据存向量库、随答案返回；用户面能力止于视频库问答——**无播放器 UI、无剪辑/笔记/其他 agent 动作**（索引/时间戳侧已商品化的直接证据）。
- 帧预算工程：30/60 FPS（至多 3600 帧/分钟）降采样到 4 FPS（240 帧/分钟），再经 shot-boundary 章节切分、SSIM 关键片段检测、模糊/重复帧剔除、高熵帧选择压到约 **40 帧/分钟**（~90 倍缩减；VLM 只看 <2% 帧）。

# 快照 5：Video-MME 基准（CVPR 2025；本条数字已经库内另一次独立全文抓取核对）

- 900 视频 / 254 小时 / 2700 人工 QA 对；6 域 30 子类；短(<2min)/中(4-15min)/长(30-60min)三档。
- 关键发现一：**加字幕/ASR 文本显著提升**——Gemini 1.5 Pro 长视频 67.4%（无字幕）→ 77.4%（有字幕）；ASR+VLM 融合优于纯帧摄取的直接基准证据。
- 关键发现二：**所有模型随视频变长性能下降**（50+ 模型一致）——"AI 一口气看完整小时视频"靠暴力抽帧未解决，是预索引/RAG 架构的动机。

# 快照 6：awesome-video-agents 清单（2026-05-27 快照）

- 整个分类法（7 个内容区）面向视频**制作/剪辑/生成**及"为制作服务的理解"；**无**消费级播放器内 AI 助手分区或条目；全列表唯一的终端用户 "chat with video" 条目是桌面应用（Vimo）。
- 收录 video-db/Director 为"video agent 开源框架"（search/edit/compile/generate 动作空间 + VideoDB 后端）。
