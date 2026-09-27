---
type: concept
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-federated-learning-ran-technical-sources.md"
  - "raw/2026-09-26-oran-federated-learning-papers.md"
  - "raw/2026-09-26-avidthink-ai-in-mobile-ran.md"
---

# AI-RAN 联邦/梯度级数据

**一句话**：联邦/梯度级数据不是第四种“网络测量数据”，而是本地模型训练产生的可交换资产：模型参数、模型增量、梯度、局部统计和评估指标。它让多个站点/RIC 在不集中原始数据的情况下，共同得到一个更泛化的基础模型。

## 先回答核心疑问

### 1. 本地数据训练出来的东西是模型吗？

是。每个 gNB/RIC/边缘节点都可以用本地数据训练一个真实模型，例如：

- CSI 预测模型；
- 流量预测模型；
- 切换风险模型；
- 节能策略模型；
- 切片资源分配策略。

这个本地模型可能已经能解决本站点问题。

### 2. 既然本地能训练模型，为什么要聚合模型更新？

因为本地模型通常只见过本地分布。聚合的目的不是替代本地模型，而是解决三个问题：

1. **单站点数据不够**：某些异常、切换边界、高速场景，一个站点几天都采不到几条；
2. **单站点模型难泛化**：隧道站学到的 CSI 模式放到高架站可能完全错误；
3. **原始数据不能集中**：运营商受隐私、合规、多厂商信任和数据主权限制。

**医生类比**：每家医院可以基于本地病例训练诊断模型。但罕见病例分散在不同医院。如果只交换脱敏统计和模型学习结果，而不搬运病例，所有医院可以共同提高对罕见病例的识别能力。

### 3. 为什么不直接把本地训练好的模型拷到一起平均？

这样容易失败。联邦学习的标准做法通常是从**同一个全局模型 θ₀** 出发：

1. 服务器把 θₜ 发给站点 A/B/C；
2. 各站点在本地数据上继续训练几步；
3. 各站点上传自己的更新，而不是从零训练的完全不同模型；
4. 服务器聚合更新得到 θₜ₊₁；
5. 下一轮再从 θₜ₊₁ 开始。

如果各站点从不同初始化和不同数据分布独立训练到收敛，再直接平均，模型参数可能处在损失面的不同区域，平均后性能会崩。这种现象在深度神经网络中尤其明显。

### 4. “梯度级数据”到底是什么？

工程上常发送的不一定是单条样本梯度，而是：

| 资产 | 含义 | 例子 |
|---|---|---|
| raw data | 原始测量/业务数据 | 用户轨迹、CDR、业务 URL、完整 IQ |
| local statistic | 本地脱敏统计 | 每小时 PRB 利用率分布 |
| mini-batch gradient | 一批样本上的梯度 | ∇L(θ; batch) |
| model delta / update | 一轮本地训练后的参数差 | Δθ = θ_local - θ_global |
| model parameter | 模型权重 | MLP/DRL actor 的权重 |
| evaluation metric | 本地评估结果 | MAE、AUC、QoS 满足率、样本数 |
| training metadata | 可审计信息 | 站点、频段、数据范围、模型版本 |

日常说“联邦/梯度级数据”时，通常指 **model delta/parameter + metric + provenance**，而不是把原始网络数据搬到中心。

## 数学直觉：聚合到底在算什么

### FedAvg 的简化版

假设 3 个站点分别有 n₁、n₂、n₃ 条样本，当前全局模型是 θₜ。

```text
1. 服务器广播 θₜ
2. 站点 i 在本地数据训练：
   θᵢ_local = θₜ - η ∇Lᵢ(θₜ)
3. 计算更新：
   Δθᵢ = θᵢ_local - θₜ
4. 服务器加权聚合：
   θₜ₊₁ = θₜ + Σᵢ (nᵢ / N) Δθᵢ
```

FedAvg 原始论文把这种方式描述为：数据留在本地，通过聚合本地计算更新学习共享模型，并特别面对 unbalanced/non-IID 数据和通信成本。见 [[wiki/sources/federated-learning-ran-technical-sources|联邦学习与 RAN 技术来源]]。

### FedProx 的改进

当站点数据差异大、算力不同、本地训练步数不同，客户端会偏离全局目标。FedProx 在本地目标里加一个“别跑太远”的约束：

```text
minθ  Lᵢ(θ) + (μ / 2) ||θ - θₜ||²
```

它允许不同设备完成不同工作量，同时提高异构环境下的收敛稳健性。

### Secure Aggregation

服务器希望得到：

```text
Δθ₁ + Δθ₂ + ... + Δθₙ
```

但不希望看到单个站点的：

```text
Δθ₁、Δθ₂...
```

Secure Aggregation 让参与方协作计算和值，而不暴露每方明文更新。原始论文面向 FL 中保护用户模型梯度，并能容忍部分用户失败。

### 差分隐私

常见做法：

1. 对每个客户端 update 做范数裁剪；
2. 加入噪声；
3. 控制隐私预算 ε/δ。

DP 的代价通常是准确率下降、需要更多数据或更多轮数。它不是装饰品，而是隐私预算和模型质量之间的交易。

## 端到端流程

```text
1. 定义任务
   例如：未来 100 ms CSI 预测 / 小区流量预测 / 切换风险 / 切片准入。

2. 选择联邦范围
   同一城市、同频段、同厂商、同类站点，或跨站点但按场景聚类。

3. 初始化全局模型
   可用合成数据、数字孪生或公开仿真数据预训练。

4. 下发全局模型
   服务器把 θₜ 和训练配置发给各 Non-RT/Near-RT RIC。

5. 本地训练
   站点用本地 CSI、SRS、CQI、流量、KPI 训练。
   不上传原始数据。

6. 本地验证
   先看本地 held-out 指标；异常站点不参与聚合。

7. 上传更新
   发送 Δθ、样本量、KPI、数据范围、模型版本。
   可加稀疏化、量化、裁剪、噪声、secure aggregation。

8. 聚合
   服务器按样本数/质量/可靠性加权平均，或使用更稳健聚合。

9. 全局验证
   用未参与训练的站点/时间段评估：
   平均指标、尾部指标、每站点公平性。

10. 分发上线
   新模型先 shadow，再灰度；保留传统算法回退。
```

## 三个具体例子

### 例 1：跨城市 CSI 预测

**问题**：高铁/车载用户在两个测量周期之间信道变化快。每个城市的数据分布不同。

```text
城市 A：隧道多，深衰落样本多
城市 B：高架多，多普勒和切换边界多
城市 C：城市峡谷，反射和邻区干扰多
```

联邦做法：

1. 各市 gNB 用本地 CSI feedback/C-V2X 数据训练同一 MLP；
2. 上传 Δθ 和本地验证 MAE；
3. 聚合成全局模型；
4. 在第四个城市 held-out 数据上测试。

为什么不用本地模型？

- 城市 A 的模型没见过高架场景；
- 城市单独样本不足；
- 原始用户轨迹不能集中。

O-RAN CSI prediction 论文就是这个方向：gNB 本地收集/标注数据，结合 CSI feedback 与 C-V2X cooperative awareness messages，独立训练或联邦训练，面向 rApps/xApps。见 [[wiki/sources/federated-learning-ran-technical-sources|联邦学习与 RAN 技术来源]]。

### 例 2：O-RAN 切片资源分配

**问题**：不同站点的 xApp 都在控制功率和切片资源，但各自经验有限。

联邦做法：

1. 每个站点训练本地 DRL agent；
2. 不上传用户会话数据，只上传策略网络/Q 网络 update；
3. 服务器聚合出更通用的策略；
4. 各站点再根据本地负载微调或使用不同策略头。

一篇 O-RAN 论文专门用 federated deep RL 协调独立 xApps，包括 power-control xApp 和 slice-based resource-allocation xApp。见 [[wiki/sources/federated-learning-ran-technical-sources|联邦学习与 RAN 技术来源]]。

### 例 3：节能策略

城市中心、体育场、工业区和郊区的小区流量潮汐不同。

- 单站点模型：可能学会本站规律；
- 联邦模型：能学到“体育场散场后 20 分钟流量上升”“工业区周末回落”等跨站模式；
- 个性化层：每个站点保留本地 fine-tune 头或策略参数。

目标不是让所有小区用完全一样的节能开关时间，而是用共享模型作为先验，再适配本地。

## 联邦学习和普通分布式训练的区别

| 维度 | 普通分布式训练 | 联邦学习 |
|---|---|---|
| 数据位置 | 数据可以先集中到训练集群 | 数据必须留在本地 |
| 信任边界 | 通常同一组织/集群 | 多站点、多厂商、多租户 |
| 通信内容 | 数据、激活、梯度都较自由 | 只应交换受限的 update/statistics |
| 数据分布 | 可以 shuffle 成 IID | 天然非 IID |
| 可用性 | 集群资源稳定 | 站点掉线、算力不均、回传不稳定 |
| 隐私 | 依赖访问控制 | 需要 secure aggregation、DP、审计 |
| 交付物 | 训练模型 | 模型 + 治理链路 + 灰度/回滚 |

## 为什么必须治理？

联邦学习不是“不传原始数据 = 绝对安全”。

1. **梯度也可能泄露信息**：攻击者可能从 update 反推训练样本特征；
2. **非 IID 会伤公平性**：平均模型可能对隧道站、体育场、工厂都不准；
3. **掉线和慢节点影响收敛**：无线回传和 RIC 算力不均；
4. **恶意更新会投毒**：异常站点可以让全局模型变差；
5. **版本和审计很关键**：要能回答“这个模型用了哪些站点、哪些时间范围、哪个模型版本、哪些隐私机制”。

常用缓解：

| 风险 | 缓解 |
|---|---|
| 单方 update 泄露 | secure aggregation、TEE、最小化元数据 |
| update 反推样本 | clipping + differential privacy |
| 非 IID | FedProx、clustering FL、personalization、meta-learning |
| 系统异构 | 可变本地步数、异步/半异步聚合、分层聚合 |
| 通信太大 | quantization、sparsification、low-rank update、梯度压缩 |
| 投毒 | 签名、准入、异常检测、稳健聚合、回滚 |
| 效果未验证 | shadow、canary、A/B、全局/本地 held-out 验收 |

## 在 AI-RAN/O-RAN 中的落地位置

| 位置 | 时间尺度 | 适合联邦任务 | 注意 |
|---|---|---|---|
| Non-RT RIC / SMO | 分钟到天 | 流量预测、节能策略、切片策略、模型仓库 | 可承担聚合和较重训练 |
| Near-RT RIC | 10 ms-1 s | 干扰协调、移动性辅助、CSI 预测 | 聚合不宜阻塞控制环 |
| DU/L1 附近 | <10 ms/100 μs | channel estimation、link adaptation、receiver | 通常本地适配，联邦只提供预训练/先验 |
| 边缘 AI 服务 | 10 ms-秒 | 视觉、VLM、机器人 | 更偏 AI-on-RAN，不一定优化 RAN |

O-RAN 分裂学习论文指出，Non-RT/Near-RT RIC 数据可以用于联邦训练，但模型变大会威胁截止期；split FL 会把部分层从 Near-RT RIC 卸载到 Non-RT RIC，同时带来数据/梯度传输成本。见 [[wiki/sources/federated-learning-ran-technical-sources|联邦学习与 RAN 技术来源]]。

## 落地方案

| 方案 | 落地能力 | AI-RAN 用法 |
|---|---|---|
| NVIDIA FLARE | FedAvg/FedProx/FedOpt/Scaffold、跨站点验证、DP、同态加密、模拟到生产 | CSI/流量预测、切片策略、节能模型的联邦训练 |
| Flower | ClientApp/ServerApp、strategy、federated evaluation、secure aggregation protocol、多集群部署 | 快速原型和研究到多站点试点 |
| OpenFL | workflow API、federated evaluation、secure aggregation、PKI、dataset splitter、straggler handling | 受控多站点训练与合规协作 |
| TensorFlow Federated | `tff.learning` 和 Federated Core API | 算法研究、联邦评估和自定义聚合 |
| O-RAN RIC xApp/rApp | Non-RT/Near-RT 控制环、E2/A1/R1 接口 | 将全局模型变成可下发、可回滚的 RAN 应用 |

## 实施检查清单

1. 任务是否真的需要跨站点泛化？如果本地数据足够且只服务本站，本地模型即可。
2. 原始数据集中是否被合规禁止？如果允许集中，联邦不是必须。
3. 本地样本是否按时间切分 train/valid/test，避免泄漏？
4. 上传的是 raw data、gradient、model delta 还是 local statistic？
5. 全局模型和本地模型的责任边界是什么？
6. 非 IID 站点是否需要聚类、个性化层或本地微调？
7. update 是否签名、加密、裁剪、加噪、审计？
8. 是否有恶意 update 检测和自动回滚？
9. 模型上线前是否经过 shadow/canary？
10. 是否记录模型版本、训练范围、参与站点、隐私预算和验收指标？

## 一句话收束

本地训练能产生模型；联邦聚合不是为了否认这件事，而是为了在“原始数据不能集中”的前提下，把分散在多站点的经验合成一个更泛化的基础模型，再安全地适配回每个站点。

## 推荐阅读

- [[wiki/concepts/ai-ran-training-inference-infrastructure|AI-RAN 训练、模型与推理基建]]
- [[wiki/sources/federated-learning-ran-technical-sources|联邦学习与 RAN 技术来源]]
- [[wiki/sources/oran-federated-learning-papers|O-RAN 联邦学习论文样本]]
- [[wiki/sources/avidthink-ai-in-mobile-ran|AvidThink AI in the Mobile RAN]]
