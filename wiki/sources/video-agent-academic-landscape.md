---
type: source
created: 2026-09-05
updated: 2026-09-05
sources:
  - "raw/2026-09-05-video-agent-academic-landscape.md"
---

# 视频 agent 学术与参考架构快照（VideoAgent ECCV / Video-RAG NeurIPS / VideoMultiAgents / NVIDIA / Video-MME / awesome-video-agents）

**一句话**：学术侧确立了"预索引 + agent 工具调用"范式的多种实现（caption 记忆型、图 RAG 型、辅助文本注入型），NVIDIA 参考架构把文本化管线定为工程默认，Video-MME 基准证明 ASR 融合与长视频瓶颈是架构选择的硬依据。

## 核心要点

- **VideoAgent（ECCV 2024，BIGAI+北大）**：2 秒片段 + captioning 特征记忆 + LLM 零样本调用 4 工具（Caption 检索/片段定位/VQA/对象记忆）；长视频 QA +6.6%（NExT-QA）/+26.0%（EgoSchema）——索引+工具调用优于单遍推理的最早系统证据之一。
- **Video-RAG（NeurIPS 2025）**：training-free，ASR+OCR+物体检测三路辅助文本注入现成 LVLM；刻意**单轮检索**非 agent 循环（2024 年的成本立场）；其前提论断（长视频上下文墙→必须预索引）是整个产品品类的技术根据。
- **VideoMultiAgents（Panasonic）**：Organizer 协调视觉/caption/scene-graph 三类 agent；纯视觉中心、无 ASR 无索引——学术路线与工程产品路线的分化样本。
- **NVIDIA 参考架构**：五段全文本化管线（ASR→抽帧+caption→混合→检索→生成）；时间戳=向量库 chunk 元数据；帧预算工程（3600→240→~40 帧/分钟，~90 倍缩减）；**无播放器 UI、无 agent 动作**——索引/时间戳侧已商品化的证据。
- **Video-MME**（数字经库内独立全文抓取核对）：Gemini 1.5 Pro 长视频 67.4%→77.4%（加字幕）；50+ 模型全部随时长降性能——ASR+VLM 融合有效、暴力长上下文不可行。
- **awesome-video-agents**（2026-05）：整个开源清单面向制作/剪辑/生成；无播放器内 AI 助手条目。

## 值得注意的细节

- 学术路线的组合分歧很说明问题：VideoAgent(ECCV) 纯视觉 captioning、Video-RAG(NeurIPS) ASR+OCR+检测、VideoMultiAgents 纯视觉多 agent、OmAgent ASR+场景+索引、"首个"声明（HKUDS VideoRAG）被更早工作限定——"transcript 优先还是视觉优先"尚无定论，但商业产品几乎都选 transcript 优先。

## 后续线索

- Video-MME leaderboard 前排的 video-SALMONN 2+（2025-09，81.6% w/ subs）值得跟踪新一代视频 LM 对"预索引必要性"的冲击。
- NVIDIA 帧预算工程（40 帧/分钟）可作为本地视频索引的成本基准。
