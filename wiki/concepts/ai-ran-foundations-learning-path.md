---
type: concept
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-architecture-overview.md"
  - "raw/2026-09-26-ai-ran-wg1-whitepaper.md"
  - "raw/2026-09-26-ai-ran-wg3-whitepaper.md"
  - "raw/2026-09-26-ai-ran-platform-infrastructure-orchestrator.md"
  - "raw/2026-09-26-oran-sc-architecture.md"
  - "raw/2026-09-26-nvidia-ai-aerial-developer-portal.md"
  - "raw/2026-09-26-nvidia-ai-ran-solutions.md"
  - "raw/2026-09-26-nvidia-ai-native-ran-spectral-efficiency.md"
  - "raw/2026-09-26-nvidia-t-mobile-physical-ai-ai-ran.md"
  - "raw/2026-09-26-softbank-ai-for-ran-verification.md"
  - "raw/2026-09-26-softbank-aitras-roi-and-architecture.md"
  - "raw/2026-09-26-avidthink-ai-in-mobile-ran.md"
  - "raw/2026-09-26-mwc-shanghai-china-ai-native-network-context.md"
  - "raw/2026-09-26-ocudu-realtime-ai-ran-platform.md"
  - "raw/2026-09-26-ai-ran-alliance-mwc-2026-progress.md"
  - "raw/2026-09-26-sharetechnote-nr-frame-structure.md"
---

# AI-RAN 基础学习路径

**一句话**：把 AI-RAN 拆成 **RAN 基础 → vRAN/O-RAN → 通信线 → 服务线 → 厂商方案** 五站；先弄懂基站怎么传数据，再理解它为什么软件化、AI 能优化什么、边缘 AI 怎么被服务化，最后看大厂怎么产品化。

> 本文优先建立直觉。厂商/试验数字都标注为“宣称、场景估算或 demo 数据”；“AI 推断”单独标出。

## 学习地图

| 站点 | 你要回答的问题 | 关键词 |
|---|---|---|
| 1. RAN 基础 | 手机到互联网之间发生了什么？ | UE、上下行、RU/DU/CU、调度、MCS、切片、QoS |
| 2. vRAN/O-RAN | 为什么基站要软件化和开放接口？ | 虚拟化、O-RU/O-DU/O-CU、SMO、RIC、O1/E2/A1 |
| 3. 通信线 | AI 怎么改进无线链路？ | 信道估计、链路自适应、AI receiver、MIMO、能耗 |
| 4. 服务线 | RAN 怎么承载 AI 服务？ | 边缘推理、dUPF、local breakout、SLA、telemetry、网络 API |
| 5. 厂商方案 | 业界怎么产品化？ | NVIDIA Aerial、设备商 vRAN/O-RAN、运营商试点 |

```text
手机/车/机器人
   ↓ 上行 / ↑ 下行
RU（收发电波）
   ↓
DU（毫秒级 MAC/PHY、调度）
   ↓
CU（控制面/用户面汇聚）
   ↓
核心网 / Internet
   ↘ AI-for-RAN：优化链路、调度、能耗
   ↘ AI-on-RAN：把推理服务放在 RAN/边缘
   ↘ SMO/RIC/OSS：管理、策略、模型生命周期
```

## 第一站：RAN 基础

### 1.1 RAN 是哪一段？

RAN（Radio Access Network，无线接入网）是移动网络里“无线那一跳”。你刷视频时，链路可简化为：

```text
视频服务器 → Internet → 5G Core → 基站 RAN → 手机
```

RAN 不负责视频推荐，只负责把数据可靠、高效、低时延地变成电波发出去，并把手机发来的电波解回数据。

**例子**：工厂 AGV 接收“左转”指令，指令从云/边缘经核心网和 RAN 到 RU 发射；AGV 回传摄像头画面走相反方向。实时性和稳定性由 RAN、核心网和应用共同决定。

### 1.2 上下行

- **DL / Downlink / 下行**：基站发给终端，例如视频帧、导航语音、推理结果。
- **UL / Uplink / 上行**：终端发给基站，例如语音、摄像头流、控制反馈和参考信号。

5G TDD 常用同一频段分时做上下行，所以“上下行配比”直接影响业务。短视频、直播、机器人视觉和生成式 AI 上传会显著增加上行压力。

**例子**：看 4K 视频主要吃下行；工厂摄像头持续把画面交给边缘 AI 主要吃上行。如果沿用“下行为主”的时隙配比，上行视频会排队、抖动甚至断流。

### 1.3 RU/DU/CU：三个接力岗位

可以把 gNB 理解成三个分工不同的岗位：CU 管“业务和连接规则”，DU 管“毫秒级排队和空口编解码”，RU 管“把比特真正变成电波”。O-RAN SC 把 O-RU 定义为 Low-PHY + RF，把 O-DU 定义为 RLC/MAC/High-PHY，把 O-CU 分成控制面 CP 与用户面 UP；管理侧有 SMO 和 Non-RT RIC。见 [[wiki/sources/oran-sc-architecture|O-RAN SC 架构]]。

| 组件 | 全称 | 主要职责 | 内部典型模块 | 时延脾气 | 常见部署位置 |
|---|---|---|---|---|---|
| RU | Radio Unit | 天线口收发电波、滤波、放大、AD/DA，O-RAN 中还含 Low-PHY | RF 前端、PA/LNA、滤波器、FFT/IFFT、CP 处理、PRACH 提取 | 微秒级信号处理 | 天线杆/楼顶/机房附近 |
| DU | Distributed Unit | 空口实时处理和调度 | High-PHY 编码/译码、调制/解调、MIMO/波束处理、MAC scheduler、HARQ、RLC | 亚毫秒到毫秒级 | 站点机房、集中机房、GPU/实时服务器 |
| CU | Centralized Unit | 上层用户面/控制面汇聚 | CU-UP：PDCP、SDAP；CU-CP：RRC、控制面 PDCP | 十毫秒以上更常见 | 边缘数据中心、区域机房 |

#### 1.3.1 用短视频下行逐环节拆解

假设你点开一条 4K 短视频。数据包要从互联网进入核心网，再穿过 CU、DU、RU，最后变成手机屏幕上的画面。下面按“输入 → 解决什么问题 → 输出”拆开。

```text
Internet → 5G Core UPF → CU-UP → DU → RU → 手机
                                  ↑
5G Core AMF/SMF → CU-CP：只下发控制规则，不搬运每个视频包
```

##### 环节 0：核心网把视频包交给 RAN

| 项目 | 内容 |
|---|---|
| 输入 | 视频服务器发来的 IP 包，经 UPF 封装成 GTP-U/NG-U 隧道包；包里带有 QFI/QoS flow 信息 |
| 这一步解决什么 | 把“互联网上的数据包”和“某个手机、某条 PDU session、某种 QoS”关联起来 |
| 输出 | 交给 CU-UP 的用户面 PDU，例如“UE-A 的 5QI=8 视频流数据包” |
| 通俗类比 | 快递先贴好收件人和服务等级标签，再交给无线接入网 |

##### 环节 1：CU-UP 处理用户面数据包

| 项目 | 内容 |
|---|---|
| 输入 | 来自核心网 UPF 的 GTP-U/NG-U 包，包含 UE 身份、PDU session、QFI、IP payload |
| 这一步解决什么 | 把核心网 QoS flow 映射成 DRB；完成 PDCP 加密/完整性保护、头处理、编号、排序和重复去除，让数据包可以通过无线承载安全有序地下发 |
| 输出 | PDCP PDU，通过 F1-U 发给对应 O-DU/gNB-DU |
| 通俗类比 | 后方分拣中心给包裹贴无线承载号、封安全袋、编流水号，但还不决定哪辆车几点发车 |

##### 环节 2：CU-CP 建立控制规则

这一步和视频包不是同一批数据。CU-CP 走控制面，负责告诉 UE 和 DU“按什么规则通信”。

| 项目 | 内容 |
|---|---|
| 输入 | 来自核心网 AMF 的 NAS 消息、UE 的 RRC 请求、无线测量报告、SMF/UPF 会话信息 |
| 这一步解决什么 | 建立/修改 RRC 连接、安全参数、测量配置、承载/QoS 配置和切换参数；让用户面知道“这个业务走哪条 DRB，用什么配置” |
| 输出 | RRC 消息，例如 RRC Setup/RRC Reconfiguration；同时把承载配置同步给 DU |
| 通俗类比 | 运行控制中心决定这批货使用哪条登机口、什么安全流程、是否改签，但它不亲自搬每个包裹 |

##### 环节 3：DU 做 RLC/MAC/PHY 处理

| 项目 | 输入 | 解决什么问题 | 输出 |
|---|---|---|---|
| RLC | CU-UP 发来的 PDCP PDU | 分段/重组、重传、排序 | RLC PDU |
| MAC | RLC PDU、缓存状态、QoS 优先级、调度请求、HARQ 状态、相邻用户/小区干扰 | 决定这个 slot 调度谁、分配哪些 PRB、用什么 MCS、是否重传 | MAC PDU/transport block |
| High-PHY | transport block、CQI/RI/PMI、波束信息、TDD pattern | 编码、速率匹配、调制、层映射、预编码、资源映射 | 数字基带/频域资源上的符号，可交给 Low-PHY 做 IFFT/CP 等 |

**短视频例子**：DU 看到 CU-UP 缓存里有一批视频数据，手机 CQI 较好，当前 DL slot 可用 100 PRB。MAC scheduler 可能优先给这台手机较多 PRB、256QAM 和 2 layer，把剩余资源给旁边聊天的人。它解决的是“这 0.5 ms 里，谁用哪块时间-频率-波束资源，用什么可靠度发送”。

##### 环节 4：O-RU/Low-PHY 和射频前端把符号变成电波

| 项目 | 内容 |
|---|---|
| 输入 | DU/O-RU 接口上的数字基带数据，通常是频域符号/IQ 样本，已带时间同步信息 |
| 这一步解决什么 | 完成 Low-PHY/射频处理：IFFT、加 CP、数字/模拟波束相关处理、DA 转换、上变频、滤波、放大；确保在正确的时间用正确的射频频率和功率发出去 |
| 输出 | 天线口射频信号，也就是空间中的电磁波 |
| 通俗类比 | 现场传送带和广播喇叭：不讨论快递为什么这么排，只负责在准确时刻把包裹/声音送到物理世界 |

##### 环节 5：UE 接收并恢复数据

| 项目 | 内容 |
|---|---|
| 输入 | 天线收到的射频信号，包含目标信号、噪声、多径和邻区干扰 |
| 这一步解决什么 | 同步、信道估计、均衡、解调、译码、PDCP 解密/排序，把无线信号恢复成 IP 包；同时生成 HARQ ACK/NACK 和 CSI |
| 输出 | 视频播放器可用的 IP/TCP/QUIC/RTP 数据；反馈给基站的 ACK/NACK、CQI/RI/PMI |
| 通俗类比 | 收件人拆袋、核对流水号、把碎片视频帧拼回完整画面 |

##### 环节 6：反馈回到 DU/CU，形成下一轮调度

| 项目 | 内容 |
|---|---|
| 输入 | UE 上报的 CSI/HARQ feedback；DU 测得的 SINR、BLER、重传、缓存、TDD 方向 |
| 这一步解决什么 | 判断上一个传输是否成功；更新信道质量；下一 slot 决定提高/降低 MCS、换波束、重传或调整资源 |
| 输出 | 新一轮调度决策：UE-A 用 80 PRB/256QAM，UE-B 用低时延小包资源；必要时 CU-CP 调整 RRC 配置 |
| 通俗类比 | 快递系统看“上一批有没有破损、客户是否催单、路况如何”，决定下一班车的车型、路线和优先级 |

#### 1.3.2 用工厂摄像头上行逐环节拆解

现在反向看一个 AI-RAN 常见场景：工厂摄像头持续上传视频，边缘服务识别缺陷。

```text
摄像头 UE → RU → DU → CU-UP → 5G Core UPF → dUPF/local breakout/边缘推理服务
```

##### 环节 1：UE 发起上行

| 项目 | 内容 |
|---|---|
| 输入 | 摄像头产生的视频帧/码流；UL grant、SRS 配置、缓冲区状态 |
| 这一步解决什么 | 把业务数据封装成 PDCP/RLC/MAC，并申请或使用上行空口资源 |
| 输出 | 编码调制后的 UL 波形，以及 SRS/DMRS 等参考信号 |
| 通俗类比 | 摄像头把视频装袋贴号，并在获得通行证后开进检查通道 |

##### 环节 2：RU 接收射频信号

| 项目 | 内容 |
|---|---|
| 输入 | 天线收到的模拟射频信号，含噪声、衰落、多径和邻区干扰 |
| 这一步解决什么 | 滤波、放大、下变频、AD 转换，以及 Low-PHY 中的 FFT/CP 处理；把物理电磁波变成可计算的数字信号 |
| 输出 | 数字 IQ/频域资源数据，送回 O-DU |
| 通俗类比 | 门口扫描设备把真实物品变成可输入系统的数字图像/条码 |

##### 环节 3：DU 解码并调度

| 项目 | 输入 | 解决什么问题 | 输出 |
|---|---|---|---|
| 信道估计/均衡 | DMRS/SRS/IQ 样本 | 估计信道失真，恢复各 RE 上的符号 | 信道估计值和软比特 |
| 解调/译码 | 均衡后的符号 | 判断 QPSK/16QAM/256QAM 符号承载的比特，LDPC/Polar 译码 | transport block 和 CRC 结果 |
| MAC/HARQ | 解码结果、ACK/NACK、队列、QoS | 决定是否重传、是否给摄像头更多 PRB、是否保住上行速率下限 | 成功的 MAC PDU，交给 RLC/PDCP |

**工厂例子**：如果摄像头在某几个 PRB 上长期 SINR 差，DU 可以给它更多 PRB或降低 MCS；如果语音用户进入小区，调度器又要给语音保留短时低时延资源。这里 AI 的机会不是“重写基站”，而是辅助信道估计、SRS 预测、链路自适应或 MAC 调度。

##### 环节 4：CU-UP 还原业务包并交给核心网

| 项目 | 内容 |
|---|---|
| 输入 | DU 上来的 PDCP/RLC 数据流，UE/PDU session/QFI 信息 |
| 这一步解决什么 | PDCP 解密、去重、重排序，恢复 GTP-U/NG-U 包；保持业务流和 QoS 映射 |
| 输出 | 发给 UPF/dUPF 的用户面 IP/GTP-U 包 |
| 通俗类比 | 后方分拣中心拆安全袋、核对序号，再把包裹交回物流网络 |

##### 环节 5：dUPF/local breakout 送边缘推理

| 项目 | 内容 |
|---|---|
| 输入 | 摄像头上行 IP 包、网络策略、边缘服务地址 |
| 这一步解决什么 | 如果策略允许，就在站点附近分流到边缘 AI 服务，避免先绕远端核心/云再回来 |
| 输出 | 交给视觉模型的视频流；必要的话再把推理结果发回工厂系统 |
| 通俗类比 | 工厂旁边的质检室直接拆包检查，而不是每个包裹都送回总部 |

#### 1.3.3 三种业务对比：为什么分工很重要

| 业务例子 | CU 的关键动作 | DU 的关键动作 | RU 的关键动作 | 输入/输出差异 |
|---|---|---|---|---|
| 短视频下行 | CU-UP 建立视频 DRB，CU-CP 可能调整 QoS/测量配置 | 每 slot 分配 DL PRB/MCS/beam，处理 HARQ | 把基带符号变成天线电波 | 输入是核心网 IP 流；输出是手机恢复的视频包 |
| 语音通话 | 建立高优先级 QoS flow，切换时保持承载 | 给小包短时资源，优先降低时延/抖动 | 精确收发上下行语音信号 | 包小、频率高，怕时延和抖动，不怕单包大吞吐 |
| 工厂摄像头上行 | 允许 QoS flow 映射到合适 DRB；支持 dUPF 分流策略 | 用 SRS/DMRS 估计上行信道，调度足够 UL PRB，处理重传 | 接收弱信号/多径/干扰，输出数字 IQ | 上行持续、怕 micro-outage，需要速率下限 |
| AGV 控制 | CU-CP 保持连接和测量配置 | 极低时延调度/可靠性保障，必要时上行小包优先 | 稳定发射接收控制信号 | 输入是控制报文/传感器数据；失败影响是停车或误动作 |

#### 1.3.4 一句话区分

- **CU-CP 的输入输出是控制消息**：输入 NAS/RRC/测量/会话信息，输出 RRC/承载/移动性规则；解决“连接和策略该怎么配”。
- **CU-UP 的输入输出是业务数据包**：输入核心网/无线承载包，输出安全、有序的 PDCP/DRB 数据；解决“业务包怎么映射、保护和排序”。
- **DU 的输入输出是实时调度和编码结果**：输入 PDCP/RLC 包、CSI、队列、HARQ、TDD/PRB/beam 状态，输出 transport block、调度决策和无线帧符号；解决“这个 slot 谁用哪块空口资源、怎么编解码”。
- **RU 的输入输出是物理射频信号和数字 IQ**：输入数字基带/频域符号，输出射频电磁波；反向输入电磁波，输出数字 IQ；解决“电波和数字信号之间的精确转换”。

#### 1.3.5 机场类比升级版

- **RU** 是安检门和传送带：真正接触旅客/包裹，必须准点运转。
- **DU** 是安检区现场调度：几十毫秒内决定谁先检、谁重检、哪条通道开多久。
- **CU-UP** 是后方行李分拣中心：处理成箱数据、加密标签和目的地排序。
- **CU-CP** 是值机/运行控制中心：决定旅客能不能登机、是否改签、走哪条登机口。
- **SMO/OSS** 是机场运营总部：看全天 KPI、升级系统、下发规则。

#### 1.3.6 它们不做什么

- RU 不理解 IP 地址、视频 URL 或 QoS 规则；它主要忠实完成射频/低层信号处理。
- DU 不负责核心网注册、计费策略和跨基站的移动性管理；它的重点是空口实时调度和物理层处理。
- CU 不通常直接生成每个 OFDM symbol 的波形；它的控制更慢，但视野更宽。

### 1.4 空口资源与调度

空口资源可以粗略理解成**时间 × 频率 × 空间 × 功率**的组合。调度器每个周期都要回答一组问题：

1. 这个 slot/mini-slot 允许哪些用户发送/接收？
2. 给每个用户多少 PRB/RE？
3. 用什么 MCS？
4. 用哪个波束、空间层或预编码？
5. 给多少功率？
6. 新传和 HARQ 重传谁优先？
7. 怎么同时满足吞吐、公平性、QoS、时延和能耗？

#### 1.4.1 资源栅格：时间和频率怎么切

NR 把无线资源切成一个二维网格。ShareTechnote 引用 3GPP 38.211 的组织方式：1 radio frame 是 10 ms，1 subframe 是 1 ms，slot 数随 numerology 变化；normal CP 下 1 slot 有 14 个 OFDM symbols，1 RB 有 12 个 subcarriers。subcarrier spacing 越大，slot 越短。见 [[wiki/sources/sharetechnote-nr-frame-structure|ShareTechnote NR Frame Structure]]。

| 资源维度 | 单位/概念 | 通俗理解 | 例子 |
|---|---|---|---|
| 时间 | radio frame / subframe / slot / OFDM symbol | 哪一毫秒、哪个时间片可用 | 30 kHz SCS 时，1 slot 约 0.5 ms，含 14 symbols |
| 频率 | carrier / BWP / PRB / subcarrier / RE | 哪一段频谱可用 | 1 PRB = 12 subcarriers；30 kHz SCS 下约 360 kHz 宽 |
| 方向 | DL / UL / flexible | 这段时间发还是收 | TDD pattern 可以是 DDDSU：3 个下行 slot、1 个上行 slot、1 个特殊 slot |
| 空间 | antenna port / layer / beam / MIMO | 用哪条空间车道 | 4 layer 给一个用户，或两个用户做 MU-MIMO |
| 功率 | transmit power / PSD | 用多大声音说话 | 边缘用户加大功率，近点用户降低功率省电降干扰 |

一个最小资源单位可以记为：

```text
RE = 1 个 OFDM symbol × 1 个 subcarrier
PRB-slot ≈ 12 个 subcarrier × 14 个 OFDM symbols
```

粗略感受容量：100 MHz 载波、30 kHz SCS 时约有 270+ 个 PRB。0.5 ms 一个 slot 意味着每秒约 2000 个 slot。仅时间×频率就有几十万个“PRB-slot 组合/秒/方向”；再加上波束和 MIMO 层，组合数量更大。但这些资源还要扣除控制信道、参考信号、同步信号、保护带和 TDD 方向限制，不能全部给业务数据。

#### 1.4.2 为什么资源有限

1. **频谱有限**：运营商只能用 licensed 频段；100 MHz 已经很贵，不能无限加宽。
2. **时间不能同时反向**：TDD 中同一频段同一时刻通常只能主要做 DL 或 UL；如果上行配比少，摄像头/直播上传会排队。
3. **同块资源会互相干扰**：给 A 用户的 PRB，如果同时给信道相关性差或波束隔离差的 B 用户，可能互相干扰、BLER 上升。
4. **空间层有限**：MIMO 层数受天线数、终端能力、信道秩和干扰限制，不是想叠几层就叠几层。
5. **功率和散热有限**：基站总功率、PA 效率、能耗和小区间干扰都有限。
6. **反馈过期**：CQI/PMI/RI 有测量和上报周期；高速移动时，拿到反馈时信道可能已经变了。
7. **协议和硬件实时约束**：PDCCH、DMRS、HARQ、guardband、RU/DU 处理时延都会消耗一部分资源。

#### 1.4.3 一个具体调度例子

假设一个 TDD 小区在某上行 slot 有 80 个可用 PRB，同时有 3 个用户：

| 用户 | 业务 | 信道情况 | 需求 |
|---|---|---|---|
| A | 工厂摄像头 | 中等 | 上行持续吞吐，怕抖动 |
| B | 工人语音 | 好 | 低时延、小包、高可靠 |
| C | 手机上传大文件 | 很好 | 尽量快，但对 10 ms 抖动不敏感 |

调度器不是只问“谁信道好”，而是为每个用户选一个资源组合：

| 用户 | 一种可能组合 | 组合含义 |
|---|---|---|
| A | 40 PRB × 14 symbols × 16QAM × beam 1 | 给摄像头较大上行管道，保证画面稳定 |
| B | 10 PRB × 2 symbols × QPSK/低码率 × beam 2 | 只给它小管道，但更早发、更抗错，保时延和可靠性 |
| C | 30 PRB × 14 symbols × 256QAM × beam 3 | 信道好，用高阶调制快速清空上传队列 |

同样 80 个 PRB，不同组合会得到不同结果：

- 如果把 80 PRB 全给 C，本 slot 总吞吐可能最高，但 A 画面卡顿、B 语音延迟。
- 如果三人均分约 27 PRB，看似公平，但 B 可能拿太多、A 可能仍不够，C 的高信道能力也被浪费。
- 更好的做法是按 **业务效用 + 队列状态 + 信道状态 + 历史吞吐**加权：A 保速率下限，B 优先小包低时延，C 用剩余资源提吞吐。

#### 1.4.4 调度组合如何影响结果

| 改变哪个资源 | 可能带来的影响 |
|---|---|
| 增加 PRB/时间片 | 原始吞吐更容易提高，但会挤压其他用户 |
| 提高 MCS | 单位资源比特更多，但对 SINR/BLER 更敏感；误码会带来重传 |
| 增加时间提前量/优先级 | 小包更快发出，适合语音/PLC，但可能牺牲大流量用户 |
| 换更窄波束 | 能量更集中、干扰更小，但对移动/阻塞更敏感 |
| 增加 MIMO layer | 吞吐潜力更高，但要求信道秩和干扰条件支持 |
| 增加 UL 时隙比例 | 上传业务更稳，但下行容量变少 |
| 加大功率 | 覆盖/SINR 可能改善，但能耗和邻区干扰也可能上升 |

#### 1.4.5 调度想让什么最大？

不要把目标简化成“让总吞吐最大”。生产网络通常是：

```text
maximize Σ weight(user) × utility(user)
subject to
  PRB 不冲突
  功率/干扰受限
  BLER/重传受限
  QoS 速率/时延/可靠性满足
  公平性和切换稳定性受限
  能耗可控
```

不同调度哲学对应不同“最大化”：

| 策略 | 最大化什么 | 风险 |
|---|---|---|
| Max C/I | 本 slot 总吞吐或频谱效率 | 信号差的边缘用户可能长期饿死 |
| Round robin | 短期机会公平 | 频谱效率可能低，QoS 优先级不敏感 |
| Proportional fair | 当前信道机会 × 长期公平 | 对硬时延/QoS 不一定足够 |
| QoS-aware scheduling | SLA 满足率/效用 | 平均吞吐可能低于 max C/I |
| Energy-aware scheduling | 每比特能耗/站点能耗 | 可能牺牲峰值吞吐或增加排队 |

**餐厅类比**：调度器像领班。它不是只让最快吃完的客人先吃，也不是机械轮询；它要同时看菜快好了没有、客人是否等太久、有没有 VIP、桌子能否拼座、服务员会不会互相撞车。好的调度目标是“整体营业额 + 顾客满意度 + 翻台率”都好，而不是单一指标最高。

AI 的机会是学习流量、信道、干扰、移动性和业务优先级，辅助或替代部分调度策略。但它的评价不能只看平均吞吐，还要看 BLER、p99 时延、QoS 满足率、边缘用户速率、公平性和回退成功率。

### 1.5 MCS

MCS（Modulation and Coding Scheme）决定：

- **调制阶数**：一个符号装多少比特。QPSK 更稳但慢，256QAM 更快但更怕噪声。
- **码率**：加多少冗余保护。码率低更抗错，但有效吞吐低。

链路自适应根据 SNR/SINR、CQI、RI、PMI、历史 BLER 等选择 MCS。选保守了浪费容量；选激进了高误块率、重传和时延。

**开车类比**：路况好比开快车、少保护；雾大就降速、开雾灯、加大车间距；隧道丢信号就降档、重传或换路线。

### 1.6 切片和 QoS

**QoS** 是一条业务流的优先级、时延、抖动、丢包、速率等处理规则。**网络切片**是把同一张物理网络虚拟成多个逻辑网络，每个切片面向一类服务组合接入、传输、核心网和运维策略。

| 场景 | 关心什么 | 网络目标 |
|---|---|---|
| 手机浏览 | 平均速率 | 吞吐优先 |
| AR 协作 | 稳定低时延 | bounded latency、低抖动 |
| 工厂 PLC | 可靠和确定 | 高可靠性、极低抖动 |
| 摄像头上行 | 持续上传 | 上行速率下限 |
| IoT 水表 | 大连接低成本 | 覆盖和能效 |

**医院类比**：同一家医院有普通门诊、急诊和 ICU。急诊不是重新盖医院，而是有独立通道、优先分诊、专门设备和医护。切片/QoS 就是在共享物理设施上建立服务边界。

WG3 白皮书强调 AI 应用需要可验证的 bounded latency、uplink throughput floor 和 reliability，而不只是峰值速率。见 [[wiki/sources/ai-ran-wg3-whitepaper|AI-RAN WG3 白皮书]]。

## 第二站：vRAN 与 O-RAN

### 2.1 vRAN：为什么基站要软件化

传统 RAN 常用专用 ASIC/DSP/FPGA 和私有硬件，性能强、能耗效率高，但升级慢、生态封闭。vRAN 把部分基带功能搬到通用服务器、虚拟机或容器中，好处是硬件池化、升级更快、RAN 和边缘 AI 可共享基础设施，也更容易做云原生部署和自动化。

代价同样清楚：RAN L1/L2 有硬实时要求；通用 CPU 能耗和确定性可能不划算；容器调度、NUMA、CPU pinning、中断和内存带宽都会影响性能；多租户共享时必须隔离故障和噪声。

**厨房类比**：专用 RAN 像只做一种菜的高速自动炸锅，效率高但改菜单难；vRAN 像可编程商用厨房，能做更多菜，但需要严格控温、排班和隔离油烟。

### 2.2 O-RAN：开放接口

O-RAN 不只是“虚拟化”，而是把 RAN 拆开并定义开放接口：

| 接口/组件 | 通俗职责 |
|---|---|
| Open Fronthaul / O-RU | 让 RU 更容易与不同 O-DU 对接 |
| O-DU / O-CU | 基带逻辑功能软件化、可拆分部署 |
| SMO | 服务管理与编排，负责配置、FCAPS、生命周期 |
| Non-RT RIC | 秒级以上策略、分析、AI/ML 训练和模型管理 |
| Near-RT RIC | 约 10 ms 到秒级近实时控制，运行 xApps |
| E2 | Near-RT RIC 与 O-CU/O-DU 之间的控制/数据接口 |
| A1 | Non-RT RIC 向 Near-RT RIC 下发策略/意图 |
| O1 | SMO 到网元的配置、性能、告警、软件管理 |

AI-RAN Alliance 则把加速基础设施、RAN/AI workload、管理控制编排、MLOps、Telemetry 和 Multi-Agent Manager 放到同一框架。见 [[wiki/sources/ai-ran-architecture-overview|AI-RAN 架构总览]]。

**智能家居类比**：封闭 RAN 像整套只能用同一品牌的智能家居；O-RAN 像定义插座、传感器、网关和管理 App 的通用协议，让照明、安防、空调和语音助手能一起工作。

### 2.3 RIC/SMO 的三个控制环

1. **Non-RT RIC / SMO**：分钟到天级，看全网 KPI，训练模型，下发策略。
2. **Near-RT RIC**：约 10 ms 到 1 s，运行 xApp，做小区级干扰协调、切换优化、负载均衡。
3. **DU/L1 内实时 AI**：低于 10 ms，甚至 100 μs 级。这里不能简单起一个普通 Kubernetes Pod 就跑模型。

OCUDU 论文强调，把第三方 AI 安全放进 DU 实时控制面需要签名、类型化权限、实时契约和非阻塞观察者。见 [[wiki/sources/ocudu-realtime-ai-ran-platform|OCUDU dApp Platform]]。

**AI 推断**：离“空口决策”越近，安全隔离、截止时间、失败回退越关键。不要把 Non-RT RIC、Near-RT RIC 和 L1 内推理混成一团。

## 第三站：通信线

### 3.1 信道估计

无线信号经过反射、绕射、多普勒和干扰后失真。基站和终端用已知参考信号估计信道响应，再用于均衡、解调、波束和链路决策。

常见信号：

- **DMRS**：解调参考信号，跟着数据走。
- **SRS**：探测参考信号，常用于上行信道探测、波束管理和调度。
- **CSI-RS**：下行信道状态参考信号，终端据此反馈 CQI/RI/PMI。

**例子**：你在高铁上用手机，基站每隔一段时间才能收到 SRS。如果车在 80 km/h，两次测量之间信道已经变化。SoftBank 用 MLP 预测非接收间隔的 SRS，宣称 80 km/h 终端下行吞吐约 +13%；这是系统级仿真结果。见 [[wiki/sources/softbank-ai-for-ran-verification|SoftBank AI-for-RAN 实测]]。

### 3.2 链路自适应

传统算法通常基于阈值和保守规则。AI/ML 可以学习干扰、历史 BLER、移动性和流量模式，预测更合适的 MCS、功率、波束或调度权重。

厂商示例：

- NVIDIA 宣称 DRL link adaptation 带来 1.3x 和低于 30 μs 推理时延；这是厂商/实验数据。见 [[wiki/sources/nvidia-ai-native-ran-spectral-efficiency|NVIDIA AI-native RAN 频谱效率]]。
- AsiaInfo 在 AI-RAN Alliance 2026 demo 中展示实时无线链路自适应 agent。见 [[wiki/sources/ai-ran-alliance-mwc-2026-progress|MWC 2026 进展]]。

模型误判的后果不是页面显示错，而是吞吐下降、BLER 上升、重传变多，甚至影响多个用户。因此必须有性能保护、回退传统算法和灰度发布。

### 3.3 AI receiver

传统接收链大致是：

```text
RF → FFT/资源解映射 → 信道估计 → 均衡 → 解调/LLR → 译码
```

AI receiver 有三种形态：

1. **辅助模块**：AI 只做信道插值、噪声估计或相位校正。
2. **神经网络均衡/解调**：AI 从接收符号预测比特 LLR，再交给译码器。
3. **端到端学习空口**：把调制、信道和接收建模成可学习系统。

WG1 白皮书把演进分为 Baseline、Stage 1 参数优化、Stage 2 替换部分模块、Stage 3 高度 AI/ML 化；其中 DeepRx 从频域信号预测 bit LLR。见 [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]]。

**降噪耳机类比**：不是让音乐本身变大，而是识别噪声路径并反向抵消。AI receiver 试图从混叠信号中恢复最有用的比特信息。

### 3.4 MIMO

MIMO 用多天线形成空间自由度：

- **SU-MIMO**：一个用户同时吃多个空间流。
- **MU-MIMO**：多个用户在空间上被同时服务。
- **Massive MIMO**：大量天线做波束赋形，把能量更准确指向用户并抑制干扰。

AI 的机会包括用户配对、波束选择/赋形和干扰抑制。

**教室收音类比**：单天线像一个大喇叭喊话；beamforming 像给每组学生戴定向耳麦；MU-MIMO 像同一段时间给不同小组开不同定向声道。NVIDIA 宣称 ML beamforming 在其场景有 1.62x 增益，但必须看实验口径。

### 3.5 能耗

RAN 能耗很大。AI 可以预测潮汐流量，决定符号关断、通道关断、载波关断、小区休眠与唤醒、MU-MIMO 和调度上的能效权衡，以及站点散热策略。

**商场灯光类比**：没人时不关整栋楼，只关某些通道、扶梯和展柜灯；预测下一小时客流，提前而不是延迟唤醒设备。Qualcomm 在 MWC 2026 demo 中展示经 SMO 的 agentic RAN 管理和能效用例；仍属 demo 场景。

## 第四站：服务线

### 4.1 边缘推理

边缘推理不是“所有 AI 都必须进基站”，而是按业务选择位置：

| 位置 | 典型用途 | 优点 | 难点 |
|---|---|---|---|
| 终端 | 拍照增强、语音唤醒 | 最低时延、隐私好 | 算力和电池受限 |
| RAN/边缘站点 | 工业视觉、机器人、AR | 靠近数据源，可结合网络 SLA | 功耗、散热、运维 |
| 城市聚合/C-RAN | GPUaaS、多站点推理 | 资源池化更好 | 回传和租户隔离 |
| 中心云 | 大模型训练、非实时分析 | 算力强 | 时延、带宽、隐私 |

AI-RAN 的特殊点是把 RAN workload 和 AI workload 放到同一加速基础设施上，并通过编排器按时间、SLA 和商业优先级分配。SoftBank AITRAS 使用 GH200 + OpenShift 的方案属于这类思路；其 219% 五年 ROI 是场景估算。见 [[wiki/sources/softbank-aitras-roi-and-architecture|SoftBank AITRAS ROI 与架构]]。

### 4.2 dUPF / local breakout

传统用户面常把数据送到集中 UPF，再进互联网或数据中心。**dUPF** 把用户面功能下移，允许 **local breakout**：符合策略的数据在本地分流。

**例子**：工厂摄像头只需把画面送到同站点的缺陷检测服务。如果先绕回区域 UPF，再回边缘，会浪费回传带宽并增加时延。dUPF 可以让流量在附近直连推理服务，同时仍受计费、安全、合规和租户策略控制。

AI-RAN Alliance 架构把 dUPF 与 AI-on-RAN Apps 一起放在 workload 层。见 [[wiki/sources/ai-ran-architecture-overview|AI-RAN 架构总览]]。

### 4.3 SLA

网络服务不能只说“很快、很稳”。AI 场景常见 SLA 指标：

- **P95/P99 时延**：多数请求和最差尾延迟。
- **抖动**：时延波动幅度。
- **上行速率下限**：保证摄像头/机器人持续上传。
- **可靠性/可用性**：单位时间成功率或中断次数。
- **切换中断**：移动时服务不断。
- **隔离性**：一个租户的突发不拖垮另一个租户。

WG3 白皮书把 premium AI connectivity 的重点放在可测量、可审计、可执行的 SLA 上。见 [[wiki/sources/ai-ran-wg3-whitepaper|WG3 AI-on-RAN 白皮书]]。

**物流合同类比**：“尽快到”不是 SLA；“工作日 10:00 前送达、破损率低于 0.1%、超时赔付”才是。

### 4.4 Telemetry

RAN AI 系统需要四类数据：

1. **空口/链路数据**：CQI、SINR、RSRP/RSRQ、MCS、BLER、重传、调度历史。
2. **业务数据**：QoS flow、上下行速率、时延、丢包、用户类别。
3. **基础设施数据**：CPU/GPU 利用率、温度、功耗、队列、容器状态。
4. **模型数据**：特征分布、预测误差、回退次数、线上收益。

这些数据形成闭环：

```text
采集 → 清洗/脱敏 → 特征/标注 → 训练/评估 → 灰度发布 → 在线监控 → 回滚/再训练
```

AI-RAN Alliance 的 D4AI 和 Data-for-AI 工作强调数据质量、清洗、验证和访问。见 [[wiki/sources/ai-ran-alliance-mwc-2026-progress|AI-RAN Alliance MWC 2026 进展]]。

### 4.5 网络 API

传统 App 只知道“有网”。AI-on-RAN 希望应用按需请求能力：保障时延的 QoD API、上行速率下限、位置/区域、网络质量事件、边缘算力/推理服务发现，以及切片或专用连接生命周期管理。

WG3 提到应复用 3GPP NEF/NWDAF、O-RAN RAIE/R1、GSMA/CAMARA 等接口，而不是另造碎片化 API。见 [[wiki/sources/ai-ran-wg3-whitepaper|WG3 白皮书]]。

**打车 API 类比**：应用不必控制司机和红绿灯，只要请求“高峰期加价派车、预计 5 分钟到达、超时退款”。网络 API 也应把复杂内部机制封装成权限受控、可计费、可审计的能力。

## 第五站：厂商方案

### 5.1 NVIDIA：以 GPU 平台为中心

NVIDIA AI Aerial 的主线是把 RAN、AI 训练/推理、数字孪生和边缘应用放进加速计算生态。

| 层 | 代表组件 | 作用 |
|---|---|---|
| 研究/仿真 | Sionna、Aerial Framework | PHY、ray tracing、系统级仿真和 AI-native 算法开发 |
| RAN 软件栈 | Aerial CUDA-Accelerated RAN | 构建 GPU 加速、3GPP/O-RAN 兼容 gNB；官方说明支持 O-RAN 7.2x/O-DU |
| 数据/模型 | pyAerial、Aerial Data Lake、AODT | 数据采集、仿真、数字孪生和模型开发 |
| 测试 | ARC-OTA | Aerial RAN + OAI L2+/5G Core，在 GH200/DGX Spark 上 OTA 测试 |
| 部署 | RAN Computer-1、ARC-Compact、ARC-Pro | 中心化、站点级和 AI-native 5G/6G 平台 |

门户来源见 [[wiki/sources/nvidia-ai-aerial-developer-portal|NVIDIA AI Aerial Developer Portal]]，产品叙事见 [[wiki/sources/nvidia-ai-ran-solutions|NVIDIA AI-RAN Solutions]]。

**读法提醒**：NVIDIA 描述的是平台路线和工具链。2-3x capacity utilization、ML beamforming 1.62x、DRL link adaptation 1.3x 都是厂商/实验宣称，要看硬件、负载、频段、对比基线和功耗。

### 5.2 设备商

设备商不是只有一条路径：

- **Ericsson**：强调可运营 RAN automation。AvidThink 报告 Intelligent RAN Automation 约 15% 平均频谱效率增益，需注意口径。
- **Nokia**：有 SON/自动化，也参与 AI-RAN 生态；MantaRay SON 被描述为 Vodafone Idea 场景每天最多 70 万次自主变更；Nokia/R&S 还有 HybridDeepRx HIL demo。
- **Samsung、HPE/Juniper、Airspan 等开放 RAN 阵营**：更强调 O-RAN 拆分、多厂商集成和云端/gNB 软件栈。
- **国内 China Mobile/ZTE**：AvidThink 摘录宣称 RAN-native AI 在直播、高清视频和 VIP 保障场景提升 15% 以上用户体验。国内还有 5G-A、U6GHz、ISAC 和算力网络背景，但术语未必完全对齐 AI-RAN Alliance。

案例汇总见 [[wiki/sources/avidthink-ai-in-mobile-ran|AvidThink：AI in the Mobile RAN]] 和 [[wiki/sources/mwc-shanghai-china-ai-native-network-context|MWC Shanghai 中国背景]]。

**AI 推断**：未来不太可能只剩 GPU 或只剩 ASIC。更现实的是混合架构：确定性高频 L1 用高效硬件，AI/编排/边缘推理用 GPU/通用加速器，训练在云/区域池，推理按 SLA 分布在边缘。

### 5.3 运营商试点

| 运营商 | 代表方向 | 证据级别 |
|---|---|---|
| SoftBank | AITRAS、AI-for-RAN 验证、GPUaaS、Dynamic Scoring | 实验室/仿真/场景模型；ROI 非审计结果 |
| SK Telecom | 流量预测、RAN QoS、LLM/SLM bin-packing、AI infra orchestration | MWC demo/联盟材料 |
| T-Mobile + NVIDIA/Nokia | Physical AI、边缘视觉、AI-RAN 试点 | 厂商/试点新闻稿 |
| NTT DOCOMO 等日本生态 | GPU-accelerated Open RAN、AI-RAN 平台验证 | 试点/联合研发 |
| China Mobile/ZTE | RAN-native AI、5G-A、算力网络、ISAC | 厂商/媒体摘要；需一手白皮书复核 |

SoftBank 的 UL channel interpolation +20%、SRS prediction +13%、AI MAC scheduling +8%，分别对应实验室实测和系统级仿真。见 [[wiki/sources/softbank-ai-for-ran-verification|SoftBank 实测]]。T-Mobile 案例见 [[wiki/sources/nvidia-t-mobile-physical-ai-ai-ran|NVIDIA/T-Mobile Physical AI]]。

### 5.4 业界通用解决模式

AI-RAN 的通用方案不是“一个大模型管基站”，而是五层组合：

1. **统一底座**：加速计算、实时节点、云原生 CaaS、GPU 分区、CPU/NUMA 隔离、多集群管理。
2. **RAN workload**：RU/DU/CU 继续满足 3GPP 实时协议，软件化后保持可升级。
3. **AI-for-RAN**：信道估计、链路自适应、调度、MIMO、能耗、移动性优化。
4. **AI-on-RAN**：dUPF/local breakout + 边缘推理 + SLA API + 计费/安全治理。
5. **编排与治理**：SMO/RIC/OSS、Telemetry、MLOps、模型仓库、灰度、回滚、多 Agent 权限控制。

AI-RAN Platform & Infrastructure Orchestrator 白皮书强调，Kubernetes 只是基础，还必须补确定性低延迟、加速器分区、站点模板、多租户隔离和电信级高可用。见 [[wiki/sources/ai-ran-platform-infrastructure-orchestrator|AI-RAN 平台编排]]。

## 新手常见的 8 个误解

1. **“AI-RAN 就是用 LLM 管基站。”** LLM 适合策略、配置、诊断和 Agent 编排；sub-ms L1 不能靠生成式模型临时推理。
2. **“O-RAN 等于 AI-RAN。”** O-RAN 提供开放架构和 RIC/SMO；AI-RAN 还要求 AI workload、加速计算、MLOps 和 AI 服务原生集成。
3. **“模型精度高就能上线。”** 还要看跨站点泛化、数据漂移、失败回退、实时约束、安全和运维责任。
4. **“边缘推理放进基站就是 AI-RAN。”** 必须解决网络 SLA、dUPF、隔离、计费、观测和安全，否则只是旁边放了台服务器。
5. **“切片越多越好。”** 每个切片都有配置、运维、资源保障和协调成本，应面向真实商业场景。
6. **“MCS 只影响速度。”** 它同时影响 BLER、重传、时延、能耗和容量。
7. **“上行不如下行重要。”** 工厂视觉、直播、机器人反馈和多模态上传都会把上行变成核心瓶颈。
8. **“厂商增益可以全网外推。”** +8%、+13%、+20%、1.3x、1.62x 必须回到负载、频段、基线、硬件和实验环境。

## 自检清单

1. 上下行分别经过哪些 RU/DU/CU、核心网和数据网络环节？
2. 为什么 DU 内 100 μs 决策不能直接用普通 Kubernetes Pod 承载？
3. Non-RT RIC、Near-RT RIC、DU 内实时 AI 的控制时延有什么差别？
4. MCS 从保守切到激进，会同时改变哪些 KPI？
5. 摄像头上行为什么需要上行速率下限和低抖动，而不是只看平均速率？
6. dUPF/local breakout 如何减少绕路？它带来哪些安全和计费问题？
7. Sionna、CUDA-Accelerated RAN、ARC-OTA、ARC-Compact/Pro 分别处在哪一层？
8. SoftBank、SKT、T-Mobile、Ericsson、Nokia、China Mobile/ZTE 的证据分别属于实验、仿真、demo、试点还是商业模型？

## 推荐阅读顺序

1. 本页：建立五站地图。
2. [[wiki/concepts/ai-ran|AI-RAN]]：AI-for-RAN / AI-on-RAN / AI-and-RAN 三分法、参考架构和难点。
3. [[wiki/concepts/ai-ran-training-inference-infrastructure|AI-RAN 训练、模型与推理基建]]：数据、训练、模型、推理、编排五层细节。
4. [[wiki/sources/ai-ran-architecture-overview|AI-RAN Alliance 架构总览]]：核对官方架构词汇。
5. [[wiki/sources/oran-sc-architecture|O-RAN SC 架构]]：理解 O-RU/O-DU/O-CU、SMO、RIC、O1/E2。
6. [[wiki/sources/ai-ran-wg1-whitepaper|WG1 AI-for-RAN 白皮书]]：通信线模块和演进阶段。
7. [[wiki/sources/ai-ran-wg3-whitepaper|WG3 AI-on-RAN 白皮书]]：SLA、边缘 AI 连接和网络 API。
8. [[wiki/sources/nvidia-ai-aerial-developer-portal|NVIDIA AI Aerial Developer Portal]]：厂商工具链和部署形态。
9. [[wiki/sources/softbank-ai-for-ran-verification|SoftBank AI-for-RAN 实测]] 与 [[wiki/sources/softbank-aitras-roi-and-architecture|AITRAS ROI 架构]]：运营商验证与商业假设。
10. [[wiki/sources/avidthink-ai-in-mobile-ran|AvidThink 汇总]]：设备商、运营商、硬件路线和产业约束。
