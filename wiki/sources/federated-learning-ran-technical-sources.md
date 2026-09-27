---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-federated-learning-ran-technical-sources.md"
---

# 联邦学习与 RAN 技术来源

**身份**：FedAvg/FedProx/Secure Aggregation/DP-SGD 论文、O-RAN FL 论文和开源框架 README 摘录。

**一句话**：联邦学习通过本地训练、上传更新、加权聚合得到共享模型；FedAvg/FedProx 解决通信和非 IID/异构，安全聚合与 DP 降低泄露风险，O-RAN 再叠加 RIC 时间尺度和 xApp 治理。

## 核心要点

- FedAvg 让数据留在本地，通过聚合本地计算更新训练共享模型；原始论文强调非 IID/不平衡数据和通信成本。
- FedProx 在 FedAvg 上加入 proximal term，并允许不同客户端完成不同工作量，处理系统和统计异构。
- Secure Aggregation 能只让服务器看到和值，不看到单方值；DP-SGD 则对梯度做隐私预算下的保护。
- O-RAN 分裂学习会把部分层从 Near-RT RIC 卸载到 Non-RT RIC，但面临数据/梯度传输和资源分配成本。
- 联邦 DRL 可协调功率控制和切片资源分配 xApp；CSI 预测可在 gNB 本地用 CSI/C-V2X 数据联邦训练。
- NVIDIA FLARE、Flower、OpenFL、TensorFlow Federated 提供从模拟到部署的工程框架。

## 值得注意

“本地训练出来的模型”是真实的，但往往只见过本地分布。联邦聚合的目标不是替代所有本地模型，而是在隐私约束下学出可迁移的基础模型；更新仍可能泄露信息，需要聚合、裁剪、噪声、签名、审计和回滚。

## 后续线索

- 对比 FedAvg/FedProx/SCAFFOLD/Ditto 在 RAN 非 IID 站点上的效果。
- 研究 O-RAN R1/E2/A1/O1 的模型签名、权限、灰度和回滚机制。

依据来源：[[raw/2026-09-26-federated-learning-ran-technical-sources]]。
