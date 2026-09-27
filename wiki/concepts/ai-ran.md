---
type: concept
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-architecture-overview.md"
  - "raw/2026-09-26-oran-sc-architecture.md"
  - "raw/2026-09-26-ai-ran-wg1-whitepaper.md"
  - "raw/2026-09-26-ai-ran-wg3-whitepaper.md"
  - "raw/2026-09-26-ai-ran-platform-infrastructure-orchestrator.md"
  - "raw/2026-09-26-nvidia-ai-ran-solutions.md"
  - "raw/2026-09-26-nvidia-ai-aerial-developer-portal.md"
  - "raw/2026-09-26-nvidia-telecom-6g-ai-native-platforms.md"
  - "raw/2026-09-26-nvidia-t-mobile-physical-ai-ai-ran.md"
  - "raw/2026-09-26-ocudu-realtime-ai-ran-platform.md"
---

# AI-RAN

**一句话定义**：AI-RAN 是把 **AI/ML、加速计算和云原生编排**直接融入 Radio Access Network（RAN）的网络架构，让 RAN 一边继续负责无线接入，一边用 AI 优化自己，并在靠近用户的基站/边缘设施上承载 AI 应用。

更口语地说：过去基站主要是一台“把手机连到运营商核心网的专用通信机器”；AI-RAN 想把它升级成一台**既是通信节点、又是 AI 优化系统、又是边缘 AI 算力节点**的多用途基础设施。

> 本页是 2026-09-26 的研究综合。官方术语和来源论断均链接到对应来源页；“AI 推断”会单独标注。AI-RAN 目前仍处在标准、试点和产品化并行推进阶段，不应把厂商宣传数字当作全网商用事实。

> 若要按“RAN 基础 → vRAN/O-RAN → 通信线 → 服务线 → 厂商方案”的顺序学习，先看 [[wiki/concepts/ai-ran-foundations-learning-path|AI-RAN 基础学习路径]]。

## 先补 5 分钟 RAN 基础

RAN（Radio Access Network，无线接入网）是移动网络里“负责无线那一跳”的部分。你手机发出的信号先到附近的基站设备，再由 RAN 接入核心网，最后进入互联网。

一个典型 5G gNB（base station 的 3GPP 术语）可以拆成：

| 缩写 | 全称 | 通俗职责 |
|---|---|---|
| RU | Radio Unit | 天线附近的射频单元，负责发/收无线电波 |
| DU | Distributed Unit | 负责很紧实时性的基带处理，如物理层/L1 和部分调度 |
| CU | Centralized Unit | 更集中一些的控制面/用户面处理，覆盖多个小区/站点 |

AI-RAN Alliance 的架构文档正是把 gNB 定义为 RU/DU/CU 组合，并让 AI-for-RAN Apps 靠近 gNB/CU/DU，AI-on-RAN Apps 与 RAN workload 并行运行。见 [[wiki/sources/ai-ran-architecture-overview|AI-RAN 架构总览]]。

可以把它类比成一条机场安检线：

- RU 像入口大门和扫描门。
- DU 像现场判图员和实时调度员，必须在很短时间内判断“谁先过、包怎么检查”。
- CU 像后方值机/安检调度中心。
- 过去这些角色大多是通信专用设备；AI-RAN 想让旁边或同一套算力里出现一个“AI 助手”，实时看队列、预测拥堵、调度资源，甚至直接给旅客提供 AI 服务。

## AI-RAN 解决什么问题

AI-RAN 不是单一产品名，而是针对三个相互纠缠的问题：

### 1. 无线资源越来越难优化

5G/6G 要同时服务手机、车、工厂机器人、AR/VR、摄像头和海量 IoT。频谱、功率、天线波束、上下行时隙、小区间干扰和能耗耦合在一起。传统算法通常用规则和保守参数保证稳定，但在复杂动态环境下容易浪费频谱或达不到最佳覆盖。

AI/ML 的机会是学习历史信道、干扰、流量和反馈模式，做更好的预测和调度。AI-RAN Alliance WG1 把这类工作称为 **AI-for-RAN**，涉及资源管理、信号质量、流量预测、能耗优化和预测性维护；其技术白皮书进一步讨论了链路自适应、信道估计、AI receiver、MIMO 接收机等具体模块。见 [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]]。

### 2. AI 应用需要“近、稳、可保证”的连接和算力

很多 AI 体验不是“能连上就行”：

- 工厂机器人需要低延迟闭环。
- 视频安防 agent 需要稳定处理摄像头流。
- AR/远程协助需要稳定的上链路，不能经常小中断。
- 车路协同和无人机需要可靠性，而不只是峰值速率。

如果所有数据都送到远端公有云，往返时延、抖动、带宽成本和隐私压力都会变大。AI-on-RAN 希望把推理放到 RAN/边缘附近，并通过网络能力提供 bounded latency、uplink throughput floor、reliability target 等可验证服务水平。WG3 白皮书强调，对 AI 场景而言，**可预测性往往比峰值速率更重要**。见 [[wiki/sources/ai-ran-wg3-whitepaper|WG3 AI-on-RAN 白皮书]]。

### 3. 运营商不想只卖管道

传统电信业务中，运营商投入大量站点、机房、频谱和电力，但收入增长常被压缩成“流量管道费”。AI-RAN 给运营商描绘的新模式是：同一套站点级加速基础设施，白天/高峰保障 RAN，其他时间或同站并行承载视觉推理、生成式 AI、企业 agent、数字孪生等 workload。

NVIDIA 的产品页把这类能力概括为 AI-for-RAN、AI-and-RAN、AI-on-RAN，并宣称同一 GPU 动态分配 5G 与 AI workload 可提升 2-3x capacity utilization。注意这是厂商宣称，需要独立部署数据验证。见 [[wiki/sources/nvidia-ai-ran-solutions|NVIDIA AI-RAN Solutions]]。

## 三个关键词：别把 AI-RAN 说成一件事

AI-RAN 语境里最容易混淆的是三个短语。它们不是三个标准协议，而是三类工作负载关系。

| 名称 | 中文直觉 | AI 做什么 | 举例 |
|---|---|---|---|
| **AI-for-RAN** | AI 优化 RAN | AI 服务对象是基站/空口 | 预测重传、优化 MCS、信道估计、AI receiver、节能调度 |
| **AI-and-RAN** | AI 与 RAN 共用基础设施 | AI 与 RAN 共享算力/平台 | 同一套 GPU/容器平台同时承载 CU/DU 和 AI 应用 |
| **AI-on-RAN** | 在 RAN 上跑 AI | AI 应用作为边缘服务 | 工厂视觉质检、城市视频搜索、AR 导航、机器人推理 |

AI-RAN Alliance 架构文档明确把 AI-for-RAN 和 AI-on-RAN 列为 AI-RAN 的双重功能，并把 gNB、AI-for-RAN Apps、AI-on-RAN Apps、dUPF 放在同一 workload 域里。NVIDIA 产品页也使用同样三分法。来源见 [[wiki/sources/ai-ran-architecture-overview|架构总览]] 与 [[wiki/sources/nvidia-ai-ran-solutions|NVIDIA AI-RAN Solutions]]。

## 业界通用方案：分层看

一个简化的 AI-RAN 参考架构可以这样理解：

```mermaid
flowchart TD
  U[手机 / 机器人 / 摄像头 / AR 眼镜] --> RU[RU 射频单元]
  RU --> DU[DU 分布式单元<br/>实时 RAN L1/L2]
  DU --> CU[CU 集中单元]
  CU --> Core[5G/6G 核心网]

  subgraph Shared Infrastructure[共享加速基础设施]
    HW[CPU + GPU/DPU/加速器]
    K8s[实时节点 + Kubernetes/CaaS/PaaS]
    AIRuntime[AI Runtime]
  end

  DU --> Shared Infrastructure
  SharedInf Apps[AI-for-RAN Apps<br/>AI-on-RAN Apps] --> Shared Infrastructure
  Shared Infrastructure --> UPF[dUPF / local breakout]
  UPF --> AIApps[视觉 agent / 生成式 AI / XR / 控制闭环]

  Orchestrator[AI-RAN Orchestrator<br/>RAN/AI/Platform Orchestration] --> Shared Infrastructure
  Telemetry[Telemetry + MLOps + Multi-Agent Manager] --> Orchestrator
```

图中的分层来自 AI-RAN Alliance 架构文档与平台编排白皮书：基础设施层、workloads 域、管理/控制/编排域。见 [[wiki/sources/ai-ran-architecture-overview|架构总览]] 与 [[wiki/sources/ai-ran-platform-infrastructure-orchestrator|Platform Infrastructure Orchestrator]]。

### 1. 共享加速基础设施

传统基站常用通信专用硬件：DSP、FPGA、ASIC 或专用加速卡，追求确定性时延和长期稳定。AI-RAN 路线则把更多 L1/L2、网络功能和 AI 推理搬到 CPU + GPU/DPU/加速器 + 云原生平台。

但这不是“把基站程序塞进普通 K8s”。RAN workload 要求确定性低延迟，AI workload 要求高吞吐和弹性。因此平台要解决：

- 实时内核、CPU pinning、NUMA、低抖动网络。
- GPU partitioning、调度隔离、时间片/独占策略。
- 多站点/多集群统一部署与升级。
- 多租户隔离、配额、认证、审计。
- telemetry、告警、自愈、滚动升级和容灾。

这些是 AI-RAN 与普通边缘 AI 最大的差别。来源见 [[wiki/sources/ai-ran-platform-infrastructure-orchestrator|Platform Infrastructure Orchestrator]]。

### 2. RAN workload 与 AI workload 共存

AI-RAN 不是“AI 取代 RAN”，而是让两类 workload 在同一基础设施上协作：

- RAN workload：RU/DU/CU、调度、资源管理。
- AI-for-RAN Apps：预测信道、辅助调度、优化链路、节能。
- AI-on-RAN Apps：视频理解、生成式 AI、XR、工业控制。
- dUPF：做 local breakout，把用户流量直接路由到同址 AI workload。

dUPF 很关键。假设工厂摄像头每秒产生大量视频帧，如果每帧都绕到几十公里外的集中云，时延和带宽都不划算；dUPF 可以让数据在本地或附近站点直接进入视觉推理服务。定义见 [[wiki/sources/ai-ran-architecture-overview|架构总览]]。

### 3. 统一编排、观测和 MLOps

AI-RAN 需要一个“交通指挥系统”：

- RAN Workload Orchestrator：管 CU/DU 等网络功能。
- AI Workload Orchestrator：管模型、推理服务和训练/部署流水线。
- Platform & Infra Orchestrator：管多站点硬件、集群和加速器。
- Multi-Agent Manager：管网络运维中的自主 agents。
- Telemetry/MLOps：持续收集数据、评估模型、监控漂移、触发闭环优化。

这些组件在官方架构文档中被归入 Management, Control and Orchestration 域。见 [[wiki/sources/ai-ran-architecture-overview|架构总览]]。

### 4. 从“外挂 AI”到“进入实时循环”

普通边缘 AI 可以把模型放在基站旁的服务器上；真正困难的是把 AI 放进 RAN 实时控制循环。OCUDU dApp 论文把有价值区间描述为 DU 内低于 10 ms，包括 link adaptation、per-slot scheduling、channel estimation 和 receiver，并提出 Class A/B/C 三类实时契约：GPU receive chain、scheduler 100 μs admitted deadline、非阻塞 observer。见 [[wiki/sources/ocudu-realtime-ai-ran-platform|OCUDU dApp Platform]]。

**AI 推断，待来源验证**：可以把 AI-for-RAN 分成两个风险等级——“旁路建议型 AI”先给出参数建议，人不接管调度主路径；成熟后再做“内嵌控制型 AI”，由平台严格限制权限、截止时间和回退路径。OCUDU 的 Class C observer 更接近前者，Class A/B 更接近后者。

## 演进路线：传统 RAN → vRAN/O-RAN → AI-RAN

AI-RAN 不是凭空出现，它接在 RAN 软件化之后。

| 阶段 | 关键形态 | 主要解决的问题 | AI-RAN 新增的东西 |
|---|---|---|---|
| 传统 RAN | 专用设备、专用软件、紧耦合硬件 | 保证通信可靠与时延 | AI 能力少，站点算力通常专机专用 |
| vRAN / Cloud RAN | CU/DU 软件化，跑在通用/加速云平台 | 部署弹性、集中化、降低专用硬件绑定 | 开始具备共享算力和云原生基础 |
| O-RAN | 开放接口、RIC、多厂商互操作 | 打破锁定，引入可编程 RAN 自动化 | RIC/接口可作为 AI 控制入口 |
| AI-RAN | RAN 与 AI workload 共享加速基础设施 | 频谱效率、边缘 AI、运维自动化、新收入 | AI-for-RAN / AI-and-RAN / AI-on-RAN |

**AI 推断，待来源验证**：可以把 O-RAN 理解为“开放化 + 可编程化”，AI-RAN 理解为“在开放云原生底座上引入 AI-native 计算范式”。两者互补，不是竞争标准；AI-RAN Alliance 文档也提到可观测性可由 RIC、SMO、OSS、EMS 或 SON 等现有组件实现。

## 用三个例子循序渐进理解

### 例 1：基站小区像一条拥堵马路

小区里有人看视频、有人开会、有工厂机器人上传传感器流。传统系统按规则测量信道质量和缓存状态，再选择调制编码方案和调度优先级。

AI-for-RAN 相当于给这条马路加一个 AI 交警：

- 观察过去几百毫秒/几秒的信道和重传模式。
- 预测某用户可能马上重传。
- 提前调整 MCS、调度顺序、功率或波束。
- 目标是让整条马路吞吐更高、堵车更少、能耗更低。

WG1 白皮书中的链路自适应、信道估计、AI receiver 都属于这类。见 [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]]。

### 例 2：工厂视频质检不只上传，还本地判断

一条产线上有 20 个摄像头。传统做法：

1. 摄像头把视频全部上传远端云。
2. 云端模型判断缺陷。
3. 结果返回产线控制系统。

问题：带宽贵、时延高、断网不可控、数据出厂有合规风险。

AI-on-RAN 的做法：

1. 摄像头通过 5G RAN 上传到附近 AI-RAN 节点。
2. dUPF/local breakout 让视频直接进入同站或就近的视觉模型。
3. 模型输出缺陷标签，控制在毫秒到几十毫秒级返回产线系统。
4. 网络给这个应用分配一个保障等级，例如 bounded latency、最低上行吞吐、丢包/可靠性目标。

这就是 AI-on-RAN：网络路径、网络质量和边缘算力一起编排。WG3 白皮书强调 uplink micro-outage、切换中断和上行波动对 AI 应用的影响。见 [[wiki/sources/ai-ran-wg3-whitepaper|WG3 AI-on-RAN 白皮书]]。

### 例 3：城市里的机器人与视频 agent

园区里有巡检机器人、摄像头和调度系统。机器人需要本地上传激光雷达/视频，附近 AI 节点完成感知与路径建议；城市摄像头需要实时搜索“谁拿了红色工具箱”。

AI-RAN 的价值是把三件事合在一起：

- 5G RAN 提供移动、广覆盖连接。
- 站点级 GPU 提供视觉/多模态推理。
- 网络质量等级保证控制闭环可预测。

NVIDIA/T-Mobile/Nokia 的 physical AI 试点描述了类似方向：把 5G 网络当作 distributed AI computer，在边缘运行视觉搜索、摘要和 reasoning agents。见 [[wiki/sources/nvidia-t-mobile-physical-ai-ai-ran|NVIDIA/T-Mobile Physical AI 案例]]。该案例是试点/生态发布，不能直接推出全网商用规模。

## 主要玩家和生态

### AI-RAN Alliance

AI-RAN Alliance 是术语和架构讨论的重要组织。其 v1.2 架构文档给出了 AI-RAN 的 dual functions、基础设施、workloads 和编排层定义。官方页面称其工作群覆盖 AI-for-RAN、AI-and-RAN、AI-on-RAN。来源见 [[wiki/sources/ai-ran-architecture-overview|架构总览]]。

### NVIDIA

NVIDIA 是目前最显眼的 AI-RAN 平台推动者之一，路线是 GPU 加速 + CUDA-Accelerated RAN + AI Aerial + Sionna/数字孪生 + Cloud Functions/AI Grid。其 AI-RAN 产品页把目标概括为 5G/6G RAN 与 AI workload 的公共加速基础设施。见 [[wiki/sources/nvidia-ai-ran-solutions|NVIDIA AI-RAN Solutions]]。

NVIDIA 新闻稿还列出 Deutsche Telekom、Ericsson、Nokia、SK Telecom、SoftBank、T-Mobile 等生态方，并把 6G 定义为 open、secure、AI-native、software-defined 平台。见 [[wiki/sources/nvidia-telecom-6g-ai-native-platforms|6G AI-Native 平台承诺]]。

**阅读提醒**：NVIDIA 资料能帮助理解产品化路线，但来源是厂商官网/新闻稿，不能视为中性基准测试。

### 运营商、设备商和开源组织

AI-RAN 的落地需要多方配合：

- 运营商拥有频谱、站点、核心网和 SLA。
- 设备商拥有 RAN 软件栈、射频/基带经验和电信级工程能力。
- 云/芯片厂商提供 GPU、AI runtime、编排和开发者生态。
- O-RAN、3GPP、Linux Foundation、GSMA/CAMARA 等组织影响接口与互操作。

**AI 推断，待来源验证**：未来更可能出现的是“电信设备商 RAN 栈 + 加速器平台 + 运营商编排 + 开发者 AI 服务”的组合，而不是单一厂商替代整条链路。

## 技术难点

AI-RAN 的核心挑战不是“AI 模型不够多”，而是把 AI 安全可靠地放进电信系统。

| 难点 | 为什么难 | 通用解法 |
|---|---|---|
| 确定性时延 | RAN L1/L2 有硬实时约束，普通 GPU 推理抖动不可接受 | 实时内核、隔离队列、加速器 partitioning、deadline scheduling |
| 多负载隔离 | RAN 故障影响通话/上网，AI 应用不能拖垮基站 | 多租户、资源配额、签名/权限、准入控制、故障回退 |
| 模型泛化 | 实验室仿真信道与真实街道/工厂差异大 | OTA 测试、多场景训练、持续监控、灰度发布 |
| 上链路波动 | 视频/机器人/AR 常以上行流量为主，上行更易受限 | 上行调度增强、切片、干扰管理、覆盖优化、admission control |
| 标准碎片化 | 3GPP、O-RAN、CAMARA、厂商接口众多 | 映射既有 API，避免平行接口，推动可验证 KPI |
| 商业闭环 | “更好的 AI 连接”必须可计量、可审计、可计费 | 服务等级、SLA、telemetry、事件报告、网络 API |

WG3 把上链路和移动性韧性列为基础能力，并强调 bounded latency、uplink throughput floor、reliability target 需要配套测量方法。见 [[wiki/sources/ai-ran-wg3-whitepaper|WG3 AI-on-RAN 白皮书]]。

## 新手常见误解

1. **AI-RAN = 用 ChatGPT 管理基站。**
   生成式 AI/agent 可能参与运维，但 AI-for-RAN 还包括信道估计、调度、链路自适应、接收机等强实时 ML；AI-on-RAN 还包括边缘视觉、XR 和机器人服务。

2. **AI-RAN = 边缘 AI。**
   边缘 AI 只说明算力靠近用户；AI-RAN 还要求与 RAN、频谱、QoS、dUPF、站点基础设施、SLA 和网络 API 协同。

3. **AI-RAN = 6G。**
   AI-RAN 与 6G 研究高度相关，但 WG3 明确说不需要等 6G；5G/5G-Advanced 可以先做上链路可观测性、服务等级和差异化 AI 连接。

4. **GPU 一上就省钱。**
   AI-RAN 可能提升利用率、频谱效率和收入，但也带来实时隔离、软件升级、电信级可靠性、散热、电力、安全和多厂商集成成本。是否划算取决于业务、站点和负载。

5. **AI-RAN 已全面商用。**
   术语、参考架构、试点和产品平台已较清晰，但全网规模、独立 TCO、能耗和真实 SLA 证据仍需按地区/运营商分别验证。

## 与相邻概念的关系

| 概念 | 与 AI-RAN 的关系 |
|---|---|
| RAN | AI-RAN 的对象。RAN 负责无线接入，AI-RAN 把 AI 和加速计算融入它 |
| vRAN / Cloud RAN | AI-RAN 的前置条件之一：RAN 软件化、云原生化和集中化 |
| O-RAN | 提供开放接口、RIC、SMO 等可编程点；AI-RAN 可复用这些能力，但不止于此 |
| MEC | 多租户边缘计算关注位置和算力下沉；AI-RAN 更强调与 RAN workload、无线质量和电信服务等级融合 |
| AI-native air interface | AI-for-RAN 的长期方向之一：用 AI/ML 参与或替代更多空口处理模块 |
| AI factory / AI grid | 运营商把分布式站点算力组成 AI 服务网的商业概念；AI-RAN 是其网络侧接口 |

## 一个最小学习路径

1. 先掌握 RAN 基础：RU/DU/CU、上下行、调度、MCS、切片、QoS。
2. 理解 vRAN/O-RAN：为什么要软件化、开放接口、RIC/SMO。
3. 读 AI-RAN Alliance 架构总览，记住 AI-for-RAN、AI-on-RAN、AI-and-RAN 三分法。
4. 分两条线深入：
   - 通信线：信道估计、链路自适应、AI receiver、MIMO、能耗。
   - 服务线：边缘推理、dUPF/local breakout、SLA、telemetry、网络 API。
5. 再看厂商方案：NVIDIA Aerial、设备商 vRAN/O-RAN 栈、运营商试点。
6. 最后评估工程现实：实时隔离、能耗、TCO、安全、升级、多厂商互操作。

## 开放问题

- AI-for-RAN 模型在多厂商、多频段、多站点环境下如何保证泛化和回退安全？
- GPU/通用加速器在硬实时 RAN 场景中的长期成本、功耗和故障模型是什么？
- AI-on-RAN 的 SLA 如何跨 3GPP NEF/NWDAF、O-RAN RAIE/R1、GSMA/CAMARA 落地成统一 API？
- 运营商是否会把 RAN 站点算力抽象成可计费的 AI Grid/AI factory 服务？
- OCUDU、AI-RAN Alliance、O-RAN Alliance、3GPP 和 Linux Foundation 的接口边界如何收敛？

## 进阶阅读

- [[wiki/concepts/ai-ran-training-inference-infrastructure|AI-RAN 训练、模型与推理基建]] — 2026 产业进展、数据/训练/模型/推理基建、大厂方案和国内外对照。

## 依据来源

- [[wiki/sources/ai-ran-architecture-overview|AI-RAN Alliance 架构总览 v1.2]] — AI-RAN 官方定义、双重功能和分层架构。
- [[wiki/sources/ai-ran-wg1-whitepaper|AI-RAN WG1 AI-for-RAN 白皮书]] — AI 优化空口/RAN 的技术演进。
- [[wiki/sources/ai-ran-wg3-whitepaper|AI-RAN WG3 AI-on-RAN 白皮书]] — AI 应用连接、SLA、上行韧性和部署路径。
- [[wiki/sources/ai-ran-platform-infrastructure-orchestrator|AI-RAN Platform Infrastructure Orchestrator]] — 共享加速底座与多站点编排。
- [[wiki/sources/nvidia-ai-ran-solutions|NVIDIA AI-RAN Solutions]] — GPU/软件定义平台的产品化路线。
- [[wiki/sources/nvidia-telecom-6g-ai-native-platforms|NVIDIA 与全球电信厂商 6G AI-Native 平台承诺]] — 生态方向。
- [[wiki/sources/nvidia-t-mobile-physical-ai-ai-ran|NVIDIA/T-Mobile Physical AI 案例]] — AI-on-RAN 边缘试点。
- [[wiki/sources/ocudu-realtime-ai-ran-platform|OCUDU dApp Platform]] — DU 内实时 AI 应用的运行时与实时契约。
