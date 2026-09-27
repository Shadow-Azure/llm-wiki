---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-oran-federated-learning-papers.md"
---

# O-RAN 联邦学习论文样本

**身份**：arXiv 上三篇 O-RAN/联邦学习论文摘要样本。

**一句话**：O-RAN 的 Non-RT/Near-RT RIC 架构让跨站点模型可以用联邦/分裂学习训练，但必须同时解决通信开销、模型大小、实时截止期、xApp 协同和非 IID 数据。

## 核心要点

- Split Federated Learning 可把部分模型层从 Near-RT RIC 卸载到 Non-RT RIC，但频繁数据/梯度传输会带来通信成本。
- Federated DRL 可协调多个独立 xApp 做功率控制和切片资源分配。
- CSI prediction 论文中，gNB 本地收集 CSI feedback 和 C-V2X CAM，独立训练或联邦训练，面向 mmWave 车联网和 rApps/xApps。

## 值得注意

联邦学习不移动原始数据，但不是“绝对隐私”：模型更新/梯度也可能泄露信息，且非 IID、掉线、恶意更新、通信瓶颈都会影响收敛。

## 后续线索

- 对比 FedAvg、FedProx、split learning、secure aggregation 和 differential privacy 的 RAN 实验。
- 梳理 O-RAN R1/E2/A1 上模型更新的频率、签名和回滚机制。

依据来源：[[raw/2026-09-26-oran-federated-learning-papers]]。
