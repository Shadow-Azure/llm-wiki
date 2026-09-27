---
type: concept
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-digital-twin-ran-playback-sources.md"
  - "raw/2026-09-26-nvidia-sionna-aodt-simulation-data.md"
  - "raw/2026-09-26-ai-ran-alliance-mwc-2026-progress.md"
---

# AI-RAN 数字孪生回放数据

**一句话**：数字孪生回放数据不是一堆孤立射频样本，而是“虚拟小区/虚拟网络在时间轴上运行一遍”所产生的状态、事件、KPI 和反事实记录。它回答的问题不是“这个信道长什么样”，而是“在这个网络里这样做，后面会发生什么”。

## 先回答最容易混淆的三个问题

### 1. 它是不是“给一组配置，然后生成仿生数据”？

接近，但不完整。配置只是触发器。完整数字孪生至少包括：

1. **场景定义**：3D 环境、站址、天线、建筑材质、UE/AGV/车辆轨迹；
2. **网络配置**：频段、带宽、PRB、TDD pattern、功率、邻区、调度器、HARQ、波束策略；
3. **仿真执行**：EM/ray tracing + RAN L1/L2/流量/移动模型按时间步推进；
4. **标定**：用现场测量、路测、telemetry 调整材质、方向图、传播、负载或业务模型；
5. **回放/反事实**：先跑 baseline，再改一个策略或配置跑分支；
6. **数据契约**：记录版本、随机种子、参数、时间戳、schema 和 KPI，否则结果不可复现。

所以更准确的说法是：**一组配置会生成一次仿真数据；标定后的时间轴仿真加上 baseline/分支记录，才构成数字孪生回放数据。**

### 2. 它和 2.1 合成信道数据有什么区别？

| 维度 | 2.1 合成信道数据 | 2.2 数字孪生回放数据 |
|---|---|---|
| 核心粒度 | 一条 CIR/H 矩阵/IQ/DMRS/SRS 样本 | 一段时间的小区/UE/调度/KPI/事件轨迹 |
| 回答的问题 | 如果信道是 H，接收符号和标签是什么 | 如果这样调度/节能/切换，网络 KPI 怎样变化 |
| 是否有时间闭环 | 通常是样本级前向仿真 | 有状态机：当前调度影响队列、干扰、重传和后续 KPI |
| 常见输入 | 几何、材质、波形、噪声、UE 速度 | 拓扑、邻区、RAN 栈、流量、telemetry、校准数据 |
| 常见输出 | IQ、LLR、BLER、最优 MCS 标签 | SINR/PRB/MCS/HARQ/时延/能耗/掉话/切换轨迹 |
| 主要用途 | 模型预训练、罕见场景增强 | 安全测试、策略评估、反事实实验、模型验收 |
| 类比 | 风洞里的一张照片/一段气流记录 | 飞行模拟器里的一整段航班 |

两者有交集。例如 AODT 的 **EM mode** 更接近合成信道生成；**RAN mode** 打开 5G RAN L1/L2 栈并输出 deployment telemetry，就更接近数字孪生。见 [[wiki/sources/digital-twin-ran-playback-sources|RAN 数字孪生回放技术来源]]。

### 3. 同一份数据能不能既训练又验证？

可以生成、不能混用。同一套孪生系统可以同时服务训练和验证，但必须拆分数据：

- **训练集**：城市 A/工厂 A/第 1-7 天/随机种子 1-100；
- **验证集**：城市 B/不同负载/不同 TDD/随机种子 101-120；
- **压力集**：故障、异常流量、边界切换、设备老化；
- **最终验收**：HIL/OTA/受限现场，而不是继续只看孪生。

如果用训练场景评估模型，相当于让学生背过的原题再考一遍。即使指标很好，也不能说明模型泛化。

## 它是怎么生成的？

### 第 0 步：先定义决策问题

不要从“建孪生”开始，而要问：

- 你要测试什么？节能、调度、切换、波束、容量还是故障恢复？
- 决策粒度是什么？slot、分钟、小时还是天？
- 成功指标是什么？吞吐、p99 时延、QoS 满足率、能耗、掉话率？
- 不能恶化的指标是什么？

**例子**：目标是“夜间小区节能后，视频用户 p95 卡顿率不上升”。这决定了孪生必须包含小时级流量、视频 QoS、载波关断/唤醒延迟和用户感知指标。

### 第 1 步：构建物理与网络场景

| 输入 | 例子 |
|---|---|
| 3D 环境 | 建筑物、墙体材质、金属货架、树木、地形、室内/室外 |
| 站点拓扑 | RU 位置、高度、方向、倾角、邻区关系、站间距 |
| 天线配置 | 4T4R/64T64R、极化、方向图、波束 codebook |
| 频谱配置 | 3.5 GHz、100 MHz、30 kHz SCS、TDD pattern |
| 网络配置 | BWP、PRB、MCS 表、HARQ、功率、切换参数 |
| 业务负载 | 视频、语音、摄像头、PLC、背景 IoT |
| 移动轨迹 | 行人、车、AGV、高铁、工厂叉车 |

NVIDIA AODT 官方文档把工作流分成 EM mode、RAN mode、GIS pipeline 和 calibration；RAN mode 会运行 5G RAN L1/L2 并产生 deployment telemetry。见 [[wiki/sources/digital-twin-ran-playback-sources|RAN 数字孪生回放技术来源]]。

### 第 2 步：做 baseline replay

baseline replay 的目标是“尽量重演真实网络的一天/一小时/一次事件”。

```text
输入：
  真实小区配置
  + 真实/回放流量
  + UE/AGV 轨迹
  + 现场路测或 telemetry

过程：
  t0 → t1 → t2 → ... → tN
  每一步更新位置、信道、缓存、调度、干扰和 KPI

输出：
  baseline trace
```

注意：baseline 不是为了“和真实完全一样”，而是为了让误差可量化。例如真实小区吞吐是 100 Mbps，孪生是 108 Mbps，误差 8%。后续如果策略 B 在孪生里提升 15%，就要判断这个提升是否显著大于 baseline 误差。

### 第 3 步：标定

标定解决“虚拟世界和物理世界不像”的问题。

| 标定对象 | 例子 |
|---|---|
| 传播路径 | 路损、多径、遮挡、穿墙、绕射 |
| 材质 | 混凝土、玻璃、金属货架、植被 |
| RU/UE 方向 | 下倾角、方位角、面板 roll |
| 波束 | 方向图、beam index、beamwidth |
| 业务模型 | 包大小、到达率、并发用户、视频码率 |
| KPI | RSRP/SINR/吞吐/时延/掉话/能耗 |

AODT 文档将 calibration 单列为工作流，并提到 UE orientation、RU orientation、RU beams、building/surface materials、vegetation materials 等可校准对象。见 [[wiki/sources/digital-twin-ran-playback-sources|RAN 数字孪生回放技术来源]]。

### 第 4 步：跑反事实实验

反事实实验是数字孪生最有价值的部分。保持同一个场景、流量和随机种子，只改一个变量：

```text
分支 A：baseline scheduler
分支 B：AI scheduler，摄像头优先
分支 C：夜间 23:00-05:00 关闭部分载波
分支 D：切换滞后参数 +2 dB
```

输出每个分支的：

- 吞吐、BLER、HARQ、MCS；
- 时延分布和 QoS 满足率；
- 切换次数和中断时长；
- 能耗；
- 边缘用户速率和公平性。

### 第 5 步：形成可复现数据包

一份成熟的数字孪生回放数据包不只是 CSV/KPI，还应包括：

1. **scenario manifest**：城市/工厂/小区 ID、版本、坐标、地图源；
2. **network config snapshot**：RAN 参数、频段、PRB、TDD、调度器版本；
3. **traffic/mobility manifest**：业务模型、UE 数、轨迹源、时间窗；
4. **physics/RF config**：ray 数、材质、天线、功率、噪声、随机种子；
5. **calibration report**：实测来源、误差、修正参数；
6. **run outputs**：时间序列、事件、KPI、反事实分支；
7. **validation report**：baseline 与真实差异、敏感性和 acceptance criteria。

NVIDIA AODT 的结果层把 batched simulation outputs 存成 S3-compatible Parquet 表并注册到 Iceberg catalog；RAN waveform dump 是可选 H5 IQ 数据。这正好对应“结构化、可查询、可复现”的数据包。见 [[wiki/sources/digital-twin-ran-playback-sources|RAN 数字孪生回放技术来源]]。

## 怎么使用？

### 用法 1：安全测试 xApp/rApp

**例子：节能 xApp**

```text
问题：
  夜间是否可以关闭 64T64R 小区的部分通道/载波？

孪生实验：
  replay 20:00-06:00 流量
  A：不节能
  B：0:00-4:00 关闭 32 通道
  C：0:00-4:00 关闭载波，视频用户到达时提前唤醒

判断：
  B 能耗最低，但边缘视频 p95 卡顿率 +12%，不可接受；
  C 能耗降低 14%，QoS 变化 <1%，可进入 HIL/OTA。
```

这里的价值不是“训练一个模型”，而是**在不碰生产网的情况下证明策略 B 不可上、策略 C 可以试**。

### 用法 2：训练和验收 AI 调度/链路自适应

AI 调度器可以在孪生环境中与环境交互：

1. 状态：队列、CQI、PRB、HARQ、TDD、QoS class；
2. 动作：给哪些用户分配哪些 PRB/MCS/beam；
3. 奖励：QoS 满足率 + 吞吐 + 公平性 - 能耗/重传惩罚；
4. 训练：在场景集 A/B/C 中试错；
5. 验证：在未训练过的场景 D/E/F 上评估。

ARIADNE 论文就是把 RL link adaptation 接入 Sionna/数字孪生环境，宣称最高 11%/20% 频谱效率提升。这个数字仍属论文/实验口径。见 [[wiki/sources/digital-twin-ran-playback-sources|RAN 数字孪生回放技术来源]]。

### 用法 3：故障复现和根因分析

某工厂 AGV 在 14:23-14:27 间出现卡顿。

1. 回放当时的 UE 轨迹、流量、邻区干扰、切换事件；
2. 检查是否有金属叉车遮挡、邻区突然加压、切换参数过晚；
3. 注入候选策略：切换提前、摄像头 QoS 提高、邻区功率下降；
4. 看哪种方案能减少 micro-outage。

这比直接在现场反复试参数安全得多。

### 用法 4：模型验收门禁

一个模型上生产前可以设置四道门：

```text
Gate 1：held-out 孪生场景指标达标
Gate 2：故障/边界场景无灾难性回退
Gate 3：HIL/OTA 小规模指标达标
Gate 4：现场 shadow → 少量小区灰度 → 紧急回滚
```

## 测试、仿真和数字孪生的差别

| 概念 | 含义 | 类比 |
|---|---|---|
| 仿真 | 用模型执行一个虚拟过程 | 在风洞里吹一段气流 |
| 测试 | 有目标、输入、期望、指标和判定的实验 | 按考试大纲组织一场考试 |
| 数字孪生 | 与物理网络对齐、可同步/回放/反事实的虚拟系统 | 可重复起飞的飞行模拟器 |
| 合成数据 | 为训练/评估生成的样本 | 考试模拟题 |

所以，数字孪生不只是“跑仿真”，而是**把仿真组织成可复现、可标定、可反事实、可验收的实验系统**。

## 为什么单独拎出来？

因为它的主要价值不是“多一些训练样本”，而是提供**闭环后果**。

在 2.1 合成信道数据里，你可以生成：

```text
信道 H → 接收 IQ → 是否译码成功
```

但在数字孪生里，你能看到链式后果：

```text
调度决策 → 某用户速率上升
→ 占用更多 PRB
→ 邻区用户延迟
→ 触发更多 HARQ/切换
→ 下一个 slot 干扰和队列变化
→ 小区级 p99/能耗/QoS 变化
```

这就是为什么调度、节能、切换和编排策略必须用数字孪生测试，而不应只看单点信道样本。

## 落地方案

| 方案 | 落地重点 | 适合用途 |
|---|---|---|
| NVIDIA AODT | EM mode/RAN mode、batched/interactive、Parquet/Iceberg、H5 IQ、calibration | 单站到城市级 5G/6G RF/RAN 仿真、策略评估 |
| Colosseum Open RAN digital twin | 数据集、不干扰生产网测试、自动化验证 | O-RAN xApp/rApp、AI/ML 竞赛与集成测试 |
| O-RAN Network Digital Twin | 覆盖物理/逻辑组件、协议和算法的风险-free 虚拟表示 | 配置变更、优化、监控和 What-if |
| Sionna/ARIADNE 类研究栈 | 可微分链路/系统仿真 + RL link adaptation | 学术原型和算法筛选 |
| 能效 xApp + DT 闭环 | 能耗/QoS 联合评估 | 载波关断、通道关断、资源分配 |

## 落地检查清单

1. 是否定义了决策问题和 acceptance criteria？
2. 是否记录了仿真版本、随机种子、场景和 RAN 配置？
3. 是否用真实 telemetry/路测做过标定？
4. 是否区分 baseline replay 和 counterfactual branch？
5. 训练/验证/压力测试是否用不同场景、日期和种子？
6. 最终是否经过 HIL/OTA/现场 shadow？
7. KPI 是否同时看均值和尾部：p95/p99、边缘用户、公平性？
8. 策略失败时是否有传统算法回退？

## 推荐阅读

- [[wiki/concepts/ai-ran-training-inference-infrastructure|AI-RAN 训练、模型与推理基建]]
- [[wiki/sources/digital-twin-ran-playback-sources|RAN 数字孪生回放技术来源]]
- [[wiki/sources/nvidia-sionna-aodt-simulation-data|NVIDIA Sionna 与 AODT 仿真数据]]
- [[wiki/sources/ai-ran-alliance-mwc-2026-progress|AI-RAN Alliance MWC 2026 进展]]
