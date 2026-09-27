---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-avidthink-ai-in-mobile-ran.md"
---

# AvidThink：AI in the Mobile RAN

**身份**：AvidThink 2025 research brief，SoftBank 赞助，系统梳理 RAN AI 架构、用例、硬件和商业挑战。

**一句话**：业界路线是从 SON/封闭 RAN 走向开放、云原生、AI-native RAN；关键约束从模型精度转向数据治理、实时编排、站点功耗、ROI 和安全信任。

## 核心要点

- 架构：混合迁移优于 rip-and-replace；O-RAN Non-RT/Near-RT RIC 提供策略和控制应用位；MLOps 覆盖数据、训练和推理。
- China Mobile + ZTE：RAN-native AI 在直播、高清视频和 VIP 保障场景宣称提升 15% 以上用户体验。
- Ericsson Intelligent RAN Automation：约 15% 平均频谱效率增益。
- Nokia MantaRay SON：Vodafone Idea 场景每天最多 70 万次自主变更。
- SoftBank：UL 信道插值 +20%、80 km/h SRS 预测 +13%、MAC scheduling +8%。
- AI-on-RAN：NTT DOCOMO/NVIDIA/Fujitsu/Wind River 已验证 GPU-accelerated 5G Open RAN；SoftBank/NVIDIA 场景估计 219% 五年 ROI。
- 硬件：GPU 灵活适合 AI/vRAN，ASIC 高效但僵化，FPGA 可重配置；未来更可能是混合架构。
- 训练方法：云端/边缘分布式可选，federated learning 可在不集中原始数据的情况下训练 RAN 模型。

## 值得注意

报告由 SoftBank 赞助，厂商案例多为公开宣称；但它清楚指出最大缺口是训练数据共享、实时隔离、功耗/冷却、组织和 KPI，而不是“有没有模型”。

## 后续线索

- 核对 GSMA Foundry 的 China Mobile/ZTE 原文和实验条件。
- 研究 RAN AI 训练数据脱敏、合成数据、federated learning 和 test methodology 的标准进展。

依据来源：[[raw/2026-09-26-avidthink-ai-in-mobile-ran]]。
