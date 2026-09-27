---
type: concept
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-architecture-overview.md"
  - "raw/2026-09-26-ai-ran-wg1-whitepaper.md"
  - "raw/2026-09-26-ocudu-realtime-ai-ran-platform.md"
  - "raw/2026-09-26-ai-ran-alliance-mwc-2026-progress.md"
  - "raw/2026-09-26-nvidia-ai-native-ran-spectral-efficiency.md"
  - "raw/2026-09-26-softbank-ai-for-ran-verification.md"
  - "raw/2026-09-26-avidthink-ai-in-mobile-ran.md"
---

# AI-RAN 训练模型与传统 RAN 功能映射

**一句话**：AI-RAN 的训练和推理不是“用一个大模型代替整个基站”，而是把传统 RAN 的 **L1/L2/L3/运维**拆成很多小决策点，再用信道估计、链路自适应、波束赋形、neural receiver、MAC 调度、节能或运维模型去优化其中某一个点。

> 本页把 [[wiki/concepts/ai-ran-training-inference-infrastructure|AI-RAN 训练、模型与推理基建]]里的论文、白皮书和厂商实验，重新放回传统 RAN 协议栈。证据混合官方白皮书、arXiv 论文、厂商实验和联盟 demo；所有百分比和倍数都只能按对应实验口径理解。

## 先记住一个总原则

传统 RAN 是一条高度工程化的流水线。手机和基站之间不是只发生“发信号、收信号”两件事，而是每个毫秒都要完成：

```text
测量/参考信号
  → 信道估计
    → 质量反馈 CQI/RI/PMI
      → 调度/链路自适应
        → 编码/调制
          → MIMO 预编码/波束赋形
            → 射频发射
              → 接收端均衡/解调/译码
                → HARQ/重传
                  → QoS/承载/移动性管理
```

AI 模型通常只接管其中一个箭头，或者只给这个箭头提建议。

**快递中心类比**：

- L1 是“看包裹、扫描条码、分拣、装车”的物理执行层。
- L2 是“哪个包裹先进、走哪辆车、重发哪些丢失件”的调度层。
- L3/RRC 是“开账户、登记地址、决定包裹去哪个城市”的管理层。
- AI 模型很少取代整座快递中心；它可能只优化“条码模糊时怎么识别地址”或“晚高峰怎么安排班车”。

## 传统 RAN 分层到底装了什么

很多人把“L1/L2”当成一个整体，但它内部有很多模块。下表是新手版拆解：

| 层级/模块 | 传统 RAN 功能 | 传统方法 | AI 可能优化的子问题 |
|---|---|---|---|
| RF/Low-PHY | 收发电波、FFT/IFFT、CP 处理、PRACH 提取、数模/模数转换 | 固定信号处理 | 通常不是 AI 重点；更多是可编程加速和实时实现问题 |
| PHY 信道估计 | 用 SRS/DMRS/CSI-RS 估计无线信道 | LS/MMSE、插值、滤波 | 从稀疏、移动、低 SNR 或过期参考信号中预测信道 |
| PHY 测量反馈 | 测 SINR/RSRP/RSRQ，生成 CQI/RI/PMI | 门限、查表、滤波 | 预测短期信道变化，减少过期反馈 |
| MIMO/波束赋形 | 多天线形成空间流，服务一个或多个用户 | MMSE/ZF、码本、规则波束管理 | 预测波束、计算权重、MU-MIMO 用户配对 |
| PHY 调制/编码 | LDPC/Polar、QPSK/16QAM/256QAM、层映射、资源映射 | 3GPP 规定流程 | 一般不随意替换；AI 可辅助选 MCS 或接收端恢复 |
| PHY 接收机 | 信道估计 + 均衡 + 解调 + LLR + 译码 | MMSE、SIC、传统 Turbo/LDPC 译码 | neural receiver 从 IQ/频域信号预测 LLR，或补偿非线性 |
| MAC 调度 | 分配 PRB、MCS、波束、功率、优先级 | round robin、max C/I、PF、QoS-aware scheduler | 学习用户质量、队列、干扰和业务需求，做实时资源分配 |
| MAC HARQ | 管理重传、软合并、进程号 | 固定进程和协议状态机 | 可预测重传风险，但主状态机通常仍要遵守协议 |
| RLC | 分段、重组、ARQ 重传、排序 | 协议状态机 | 不是当前主要 AI 突破口 |
| PDCP | 加密/完整性、头压缩、重复、排序 | 协议流程 | 不是当前主要 AI 突破口 |
| RRC/移动性 | 连接建立、测量配置、切换、寻呼 | 规则和测量事件 | 预测轨迹、切换风险、连接稳定性 |
| 网管/SON | 参数配置、告警、根因分析、节能 | 规则、阈值、经验 | 预测流量、异常检测、自动参数调整、LLM 策略辅助 |

**结论**：说“AI 解决 L1/L2 问题”太粗。更准确的说法是——AI 解决 L1/L2 中的**某个测量、决策或恢复模块**。

## 一个完整例子：工厂摄像头上行

想象工厂里一台摄像头要把画面发给基站旁边的视觉 AI 服务。

### 第 0 步：业务目标

业务关心的不是“峰值速率多高”，而是：

1. 上行速率不要忽高忽低；
2. P99 时延要稳；
3. 切换或干扰时不掉帧；
4. 不能抢走旁边 PLC 或语音的 QoS。

这决定了 AI 模型要优化的 RAN KPI：**上行用户吞吐、BLER、重传、时延抖动、中断次数**。

### 第 1 步：摄像头先发参考信号

摄像头发送 **DMRS** 跟随数据，或周期性发送 **SRS** 帮基站探测上行信道。

**类比**：快递员每隔几秒发一张定位和路况照片，调度中心才知道现在路面是不是拥堵。

### 第 2 步：信道估计

基站收到参考信号后估计：

- 信号从哪些路径来；
- 每条路径衰减多少；
- 频率选择性衰落如何；
- 高速移动带来什么相位变化。

传统方法常用 LS/MMSE 和插值。如果参考信号稀疏、移动快、低 SNR 或有功放非线性，传统估计可能不够准。

AI 对应方案：

| 模型 | 学什么 | 对应传统功能 |
|---|---|---|
| SoftBank UL channel interpolation | 在 poor network-quality 场景补齐/修正上行信道 | PHY 信道估计 |
| Samsung/Keysight/NVIDIA/HPE PUSCH workflow | 从低 SNR PUSCH 数据形成更好的信道估计工作流 | 上行接收链路中的信道估计 |
| SKT/Samsung SRS channel estimation | 优化 SRS 信道估计 | 上行探测/波束管理输入 |

SoftBank 宣称 UL channel interpolation 在实验室 poor network-quality 场景带来约 +20% 上行用户吞吐。注意：这是实验室实测口径，不是全网商用基准。见 [[wiki/sources/softbank-ai-for-ran-verification|SoftBank AI-for-RAN 实测]]。

### 第 3 步：反馈和链路自适应

下行方向，终端会反馈 CQI/RI/PMI；上行方向，基站可以直接测量。系统根据信道质量选择：

- 用 QPSK 还是 256QAM？
- 码率高一点还是低一点？
- 用几个 MIMO layer？
- 是否先保守传输、依赖 HARQ？

这就是 **link adaptation**。

**开车类比**：路况好比开快车；雾大就降速、加大保护距离。MCS 选择也是同一件事。

AI 对应方案：

| 模型 | 输入 | 输出 | 解决哪块 |
|---|---|---|---|
| NVIDIA DRL link adaptation | 信道状态、历史 ACK/NACK、环境状态 | MCS/传输策略 | 替代或辅助 OLLA 等外环链路自适应 |
| AsiaInfo real-time link adaptation agent | 实时无线链路状态 | 链路自适应建议/决策 | MWC 2026 的实时链路自适应 demo |

NVIDIA 宣称 DRL link adaptation 在 cell edge 对比 OLLA 有 1.3x，且推理保持低于 30 μs。这个数字要看基线、负载和实验环境。见 [[wiki/sources/nvidia-ai-native-ran-spectral-efficiency|NVIDIA AI-native RAN 频谱效率]]。

### 第 4 步：MAC 调度

同一个小区里可能同时有：

1. 摄像头上行；
2. 工人语音；
3. PLC 控制；
4. 手机视频；
5. 后台 IoT。

MAC scheduler 要决定谁用哪些 PRB、什么时刻发、用什么优先级。传统算法可能是 round robin、PF、QoS-aware scheduler。

**餐厅类比**：只有 10 张桌子和 3 个服务员。调度器要决定谁先坐、谁拼桌、VIP 要不要插队、外卖单什么时候出餐。

AI 对应方案：

| 模型 | 学什么 | 对应传统功能 |
|---|---|---|
| SoftBank AI MAC scheduling / MU-MIMO pairing | 用户信道相关性、队列、业务质量 | MAC 调度和用户配对 |
| SKT traffic prediction + bin-packing | 预测流量，预留 RAN 资源，再打包 AI/LLM 工作负载 | 资源编排，不是空口调度本身 |

SoftBank 宣称 AI MAC scheduling 平均用户吞吐约 +8%，这是系统级仿真结果。见 [[wiki/sources/softbank-ai-for-ran-verification|SoftBank 实测]]。

### 第 5 步：MIMO 和波束赋形

基站有很多天线。系统要决定：

- 能不能同时服务多个用户？
- 每个用户用几个空间层？
- 波束能量指向哪里？
- 多用户之间干扰怎么压？

**教室类比**：一个喇叭对全班喊话很吵；beamforming 像给每组学生戴定向耳麦；MU-MIMO 像同时给多个小组开不同声道。

AI 对应方案：

| 模型 | 解决哪块 | 典型收益 |
|---|---|---|
| NVIDIA ML beamforming | 从信道矩阵学习更优预编码/波束权重 | 64T64R/16 用户/2 layer 场景宣称 1.62x |
| SoftBank/NVIDIA massive MU-MIMO | 室外 16-layer MU-MIMO 场景提升容量 | 试验宣称约 3x |
| Hanyang University + Keysight/VIAVI beam tracking | 移动中预测和跟踪波束 | 数字孪生/demo |

这些数字依赖天线数、层数、用户分布和对比基线，不能直接外推到全网。见 [[wiki/sources/nvidia-ai-native-ran-spectral-efficiency|NVIDIA 频谱效率方法]]与 [[wiki/sources/ai-ran-alliance-mwc-2026-progress|MWC 2026 进展]]。

### 第 6 步：接收机恢复

即使发送端选好了 MCS 和波束，真实信道仍会带来：

- 噪声；
- 干扰；
- 多径；
- 相位误差；
- 功放非线性。

传统接收机做均衡、解调和译码。AI receiver/DeepRx 的目标是直接从接收信号中学出更稳的表示，比如预测 bit LLR，再交给译码器。

WG1 白皮书把演进分成四段：Baseline → 参数/控制优化 → 替换部分模块 → 高度 AI/ML 化。DeepRx 属于更靠后的 neural receiver 方向。见 [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]]。

**降噪耳机类比**：不是简单把音量调大，而是识别噪声路径后反向抵消。AI receiver 也是从混叠信号中恢复最有用的比特信息。

MWC 2026 中，Nokia/Rohde & Schwarz 的 HybridDeepRx 用 hardware-in-the-loop 验证真实 UE 功放非线性下的 AI receiver。这说明它优化的是 **PHY 接收链路中的失真补偿/信号恢复**，不是整个 gNB。见 [[wiki/sources/ai-ran-alliance-mwc-2026-progress|MWC 2026 进展]]。

## 把模型放回时间尺度

不同 RAN 功能有不同的决策时间，模型不能混着放。

| 时间尺度 | RAN 位置 | 典型模型 | 通俗类比 |
|---|---|---|---|
| 天/小时 | SMO、Non-RT RIC、网管 | LLM 策略、AIOps、节能策略、参数建议 | 公司运营部看周报和月报 |
| 1 s–10 ms | Near-RT RIC | 流量预测、移动性辅助、RIC xApp | 调度中心看实时路况 |
| <10 ms | DU/L2 | MAC 调度辅助、MU-MIMO 配对、链路自适应 | 值班领班现场排桌 |
| 100 μs 级 | DU/L1 | beamforming、channel estimation、neural receiver | 高速分拣机毫秒级识别包裹 |

OCUDU 论文特别强调：让第三方 AI 进入 production DU，不能只做普通容器化。它定义了三类实时契约：

1. **Class A**：进入 GPU receive chain；
2. **Class B**：进入 scheduler 的约 100 μs admitted deadline；
3. **Class C**：非阻塞 observer，只观察，结果可被 scheduler 消费。

并且 dApp 要签名、权限类型化、不替代常规路径。见 [[wiki/sources/ocudu-realtime-ai-ran-platform|OCUDU dApp Platform]]。

**AI 推断**：可以把生产模型分成三种治理角色：

1. **observer**：只看不做，验证分布和收益；
2. **advisor**：给传统算法建议，传统算法可拒绝；
3. **actor**：直接控制 L1/L2，必须有 deadline、watchdog、回退和认证。

## 每种训练数据对应哪个 RAN 功能

训练 AI-RAN 模型时，数据不是泛泛的“网络日志”，而是与协议栈模块强绑定。

| 训练数据 | 代表字段 | 能训练什么模型 | 对应 RAN 功能 |
|---|---|---|---|
| 参考信号/IQ | SRS、DMRS、CSI-RS、频域 IQ | 信道估计、SRS 预测、neural receiver | PHY 测量/接收链 |
| 反馈与误块 | CQI、RI、PMI、ACK/NACK、BLER | link adaptation、重传预测 | 外环链路自适应 |
| 信道矩阵 | 信道响应、相关矩阵、干扰矩阵 | beamforming、MU-MIMO pairing | MIMO/预编码 |
| 调度状态 | PRB、MCS、队列、QoS class、缓存、负载 | MAC 调度模型 | MAC 资源分配 |
| 轨迹/测量 | RSRP/RSRQ、邻区、速度、切换事件 | handover/connection stability | RRC/移动性 |
| 告警/配置/KPI | 告警、日志、能耗、参数、工单 | AIOps、SON、节能 | 网管/运维 |

这就是训练基建的第一个设计问题：**你要优化 RAN 的哪一块，就先定义那块模块的输入、输出、时间和 KPI**。

## 模型方法谱系：解决传统 RAN 的哪一块

| AI 模型 | 它不做什么 | 它解决的具体块 | 成功标准 |
|---|---|---|---|
| Channel estimation model | 不决定完整调度表 | 从 SRS/DMRS/IQ 恢复更准信道 | 估计误差下降、BLER/吞吐改善 |
| SRS prediction model | 不改变 SRS 协议本身 | 预测两次 SRS 之间的信道变化 | 高速/稀疏探测下吞吐提升 |
| Link adaptation model | 不负责射频和波束硬件 | 选更合适 MCS/传输参数 | cell-edge 吞吐、BLER、p99 时延改善 |
| Beamforming model | 不取代 3GPP 波束流程 | 计算波束权重、用户配对、干扰抑制 | 频谱效率、容量、边缘 SINR |
| Neural receiver | 不重写 gNB 协议状态机 | 均衡/解调/失真补偿/LLR 预测 | 低 SNR、非线性、强干扰下 BLER |
| MAC scheduler model | 不直接做秒级运维 | 分配 PRB、选择用户、MU-MIMO 配对 | 平均/边缘吞吐、QoS 满足率 |
| RIC xApp | 不进入 sub-ms L1 主路径 | 近实时负载均衡、干扰协调、节能 | 小区级 KPI 与稳定性 |
| LLM/AIOps | 不做 sub-ms 波束或调制 | 意图解析、配置生成、根因分析 | 故障时间、配置错误、人工工作量 |

WG1 白皮书的阶段划分可以理解为 AI 渗透深度：

1. **Stage 0/Baseline**：传统算法；
2. **Stage 1**：AI 做参数/控制优化；
3. **Stage 2**：AI 替换部分模块；
4. **Stage 3**：接近 AI-native air interface。

见 [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]]。

## 为什么 PDCP/RLC 反而不是重点

RLC/PDCP 也有复杂功能，例如分段重组、加密、完整性保护、头压缩和重复传输。但当前 AI-RAN 讨论更少聚焦这里，原因有三个：

1. 这些流程被 3GPP 状态机和安全要求强约束；
2. 它们的实时压力通常不如 L1 和 MAC scheduler；
3. 商业瓶颈更多在频谱效率、上行稳定性、能耗和运维效率。

所以不要把“L2 AI”泛化成“AI 重写整个协议栈”。更现实的是：**L2 的 MAC 调度、HARQ 风险预测、QoS 准入可能被 AI 辅助；RLC/PDCP 仍主要按协议实现**。

## 训练如何落到某个 RAN 决策点

一个可信的项目应该从功能映射开始，而不是先买 GPU。

```text
1. 选 RAN 决策点
   例如：上行信道插值、MCS 选择、MU-MIMO 配对、小区节能。

2. 定义传统基线
   例如：MMSE/插值、OLLA、PF scheduler、规则节能。

3. 定义模块边界
   输入：SRS/DMRS/CQI/ACK/队列/信道矩阵；
   输出：信道估计/MCS/配对/开关建议。

4. 定义 KPI 和失败条件
   吞吐、BLER、p99 时延、能耗、切换中断、QoS 满足率、GPU 功耗。

5. 构建数据闭环
   现场脱敏数据 + 合成信道 + 数字孪生 + HIL/OTA。

6. 分级上线
   offline sim → HIL → OTA → shadow → advisor → limited actor → rollback。
```

**车间类比**：不要一上来让 AI 当厂长。先让它当质检员（observer），再当班组长助手（advisor），最后在受控工位上做自动调节（actor），并保留急停按钮。

## 常见疑问

### L1/L2 里的很多模块都被 AI 替代了吗？

没有。2026 年讨论最多的是：

- PHY：信道估计、SRS/DMRS 处理、neural receiver、beamforming；
- MAC：调度、MU-MIMO 配对、链路自适应辅助；
- RIC/SMO：负载、干扰、能耗、移动性和配置策略；
- 运维：异常检测、根因分析、LLM 策略。

RF、编码器、译码器、HARQ 状态机、RLC/PDCP 等大部分仍要按协议和确定性工程实现。

### 一个模型能同时解决信道估计和调度吗？

可以做成端到端系统，但治理上最好拆开。原因：

1. 输入输出不同：IQ/参考信号 vs 队列/QoS/信道状态；
2. 时间尺度不同：百微秒 vs 毫秒/十毫秒；
3. 失败影响不同：一次译码错误 vs 多用户资源饿死；
4. 训练数据不同：物理信号数据 vs 网络状态数据。

**AI 推断**：生产系统更容易接受“一个模型优化一个模块，多个模型通过严格接口协作”，而不是一个黑盒模型直接输出全基站行为。

### LLM 是不是能管 RAN？

LLM 适合理解意图、写配置、生成策略、解释告警、辅助运维。但 AI-RAN Alliance MWC 2026 材料明确指出：LLM 是秒级 policy/configuration 层，RAN L1 是 sub-millisecond 层。见 [[wiki/sources/ai-ran-alliance-mwc-2026-progress|MWC 2026 进展]]。

所以 LLM 可以说“把这批小区夜间进入节能模式”，但不应该在 100 μs 内临时生成波束权重。

## 快速判断表

如果有人说某个 AI-RAN 模型很厉害，先问六个问题：

1. **它在传统 RAN 哪一层？** PHY/MAC/RRC/SON/SMO？
2. **它优化哪个模块？** 信道估计、MCS、beamforming、scheduler 还是节能？
3. **输入输出契约是什么？** IQ、CQI、ACK/NACK、队列、波束权重还是文本策略？
4. **时间预算是多少？** 100 μs、10 ms、1 s 还是分钟级？
5. **传统基线是什么？** MMSE、OLLA、PF、规则 SON，还是只是“无 AI”？
6. **失败怎么回退？** shadow、watchdog、规则回退、模型签名和回滚是否具备？

## 推荐阅读顺序

1. [[wiki/concepts/ai-ran-foundations-learning-path|AI-RAN 基础学习路径]] — 先建立 RAN、O-RAN、通信线、服务线和厂商方案的整体地图。
2. [[wiki/concepts/ai-ran-training-inference-infrastructure|AI-RAN 训练、模型与推理基建]] — 看数据、训练、推理和编排的产业视角。
3. 本页 — 把模型放回传统 RAN 的具体功能块。
4. [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]] — 看 PHY/链路模块的官方演进阶段。
5. [[wiki/sources/ocudu-realtime-ai-ran-platform|OCUDU dApp Platform]] — 看 DU 内 100 μs 级 AI 的运行时约束。
6. [[wiki/sources/softbank-ai-for-ran-verification|SoftBank 实测]] — 看信道插值、SRS 预测和 MAC 调度的具体实验。
7. [[wiki/sources/nvidia-ai-native-ran-spectral-efficiency|NVIDIA 频谱效率方法]] — 看 beamforming 和 DRL link adaptation。
8. [[wiki/sources/ai-ran-alliance-mwc-2026-progress|MWC 2026 进展]] — 看哪些模块已经进入多厂商 demo。

## 依据来源

- 传统演进与 AI 模块：[[wiki/sources/ai-ran-wg1-whitepaper]]
- DU/L1/L2 实时契约：[[wiki/sources/ocudu-realtime-ai-ran-platform]]
- 信道估计/SRS/MAC 调度实验：[[wiki/sources/softbank-ai-for-ran-verification]]
- beamforming 与 link adaptation：[[wiki/sources/nvidia-ai-native-ran-spectral-efficiency]]
- 多厂商 demo 与 LLM 边界：[[wiki/sources/ai-ran-alliance-mwc-2026-progress]]
- 产业架构和硬件权衡：[[wiki/sources/avidthink-ai-in-mobile-ran]]
