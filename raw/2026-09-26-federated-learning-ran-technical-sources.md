---
title: Federated Learning and RAN Technical Sources
source_url: https://arxiv.org/abs/1602.05629
secondary_sources:
  - https://arxiv.org/abs/1812.06127
  - https://arxiv.org/abs/1611.04482
  - https://arxiv.org/abs/1607.00133
  - https://arxiv.org/abs/2508.02534
  - https://arxiv.org/abs/2208.01736
  - https://arxiv.org/abs/2607.21963
  - https://github.com/NVIDIA/NVFlare
  - https://github.com/flwrlabs/flower
  - https://openfl.readthedocs.io/en/latest/
  - https://github.com/tensorflow/federated
accessed: 2026-09-26
publisher: arXiv authors / open-source projects
language: en
---

# Federated Learning and RAN Technical Sources

## Foundational algorithms

### FedAvg: Communication-Efficient Learning of Deep Networks from Decentralized Data

- arXiv: <https://arxiv.org/abs/1602.05629v4>
- Training data are privacy-sensitive, large, or both, and may preclude logging to a data center.
- Federated Learning leaves data distributed on devices and learns a shared model by aggregating locally computed updates.
- The paper introduces iterative model averaging and evaluates unbalanced/non-IID data.
- Communication cost is a principal constraint.

### FedProx: Federated Optimization in Heterogeneous Networks

- arXiv: <https://arxiv.org/abs/1812.06127v5>
- FL has systems heterogeneity and statistical heterogeneity/non-IID data.
- FedProx generalizes FedAvg, allows variable local work, and adds a proximal term.
- It targets more robust convergence under heterogeneous devices and data.

### Secure Aggregation

- arXiv: <https://arxiv.org/abs/1611.04482v1>
- Secure aggregation lets mutually distrusting parties compute the sum of private values without revealing each value.
- Applied to FL, it protects each user's model gradient.
- The protocol tolerates up to one third of users failing and has bounded communication expansion.

### Differential Privacy for Deep Learning

- arXiv: <https://arxiv.org/abs/1607.00133v2>
- Training models may expose sensitive information in datasets.
- DP-SGD provides algorithmic techniques and refined privacy-cost analysis under a privacy budget.

## O-RAN applications

### Split Federated Learning in O-RAN

- arXiv: <https://arxiv.org/abs/2508.02534v1>
- O-RAN hierarchy enables FL using data from Non-RT and Near-RT RICs.
- Larger models can jeopardize Non-RT/Near-RT deadlines.
- Split FL offloads partial layers from Near-RT RIC to high-performance Non-RT RIC.
- Frequent data/gradient transfer and compute/communication allocation are key challenges.

### Federated DRL for Resource Allocation in O-RAN Slicing

- arXiv: <https://arxiv.org/abs/2208.01736v1>
- Coordinates independent xApps with federated deep RL.
- Includes power-control and slice-based resource-allocation xApps.

### CSI Prediction for O-RAN mmWave Vehicular Networks

- arXiv: <https://arxiv.org/abs/2607.21963v1>
- gNBs collect and label local datasets for CSI prediction.
- Datasets combine CSI feedback and C-V2X cooperative awareness messages.
- Models are trained independently and with FL for O-RAN rApps/xApps.

## Open-source landing frameworks

### NVIDIA FLARE

- Repository: <https://github.com/NVIDIA/NVFlare>
- Domain-agnostic, extensible Python SDK for adapting ML/DL workflows to federated paradigms.
- Supports PyTorch, TensorFlow, scikit-learn, XGBoost; horizontal/vertical FL; FedAvg, FedProx, FedOpt, Scaffold, Ditto; global model evaluation and cross-site validation.
- Includes differential privacy, homomorphic encryption, private set intersection, simulator/POC mode, cloud/on-premise deployment, authorization/privacy policy, resiliency and fault tolerance.

### Flower

- Repository: <https://github.com/flwrlabs/flower>
- Friendly federated learning research framework.
- Documentation includes ClientApp/ServerApp, federated strategies, federated evaluation, secure aggregation protocols, SuperLink/SuperNode deployment, Docker, Helm, OpenShift, multiple clusters, and baselines such as FedAvg/FedOpt/FedProx-related methods.

### OpenFL

- Documentation: <https://openfl.readthedocs.io/en/latest/>
- Provides workflow API, federated evaluation, privacy meter, secure aggregation, federated analytics, PKI utilities, dataset splitters, compression, aggregation override, and straggler handling interfaces.

### TensorFlow Federated

- Repository: <https://github.com/tensorflow/federated>
- Open-source framework for decentralized-data computation and federated learning research.
- Has high-level `tff.learning` APIs for federated training/evaluation and lower-level Federated Core APIs for custom algorithms.

## 阅读定位

联邦学习不是“每个节点随便训练一个模型再拷来拷去”。FedAvg 的关键是从同一全局模型出发聚合本地更新；FedProx/安全聚合/DP 解决非 IID、系统异构、隐私和投毒；O-RAN 论文再叠加 RIC 时间尺度、回传带宽、xApp 协同和实时约束。
