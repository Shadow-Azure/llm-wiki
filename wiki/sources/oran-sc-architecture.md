---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-oran-sc-architecture.md"
---

# O-RAN SC Architecture Overview

**身份**：O-RAN Software Community 的架构概览页，用于解释 O-RAN Alliance 架构的软件社区术语。

**一句话**：O-RAN 把 RAN 拆成 O-RU/O-DU/O-CU，并把 Non-RT RIC 放入 SMO、Near-RT RIC 放到靠近 RAN 的控制位置，通过 O1/E2 等接口让管理、策略和应用可扩展。

## 核心要点

- 无线侧包括 Near-RT RIC、O-CU-CP、O-CU-UP、O-DU、O-RU。
- 管理侧是 Service Management and Orchestration Framework，内含 Non-RT RIC。
- Near-RT RIC 通过 E2 做细粒度数据采集和近实时优化；xApp 是其第三方插件。
- Non-RT RIC 负责 AI/ML workflow、模型训练/更新，并通过策略指导 Near-RT RIC。
- O-DU 承载 RLC/MAC/High-PHY；O-RU 承载 Low-PHY 和射频处理。
- O1 面向管理面，支持 FCAPS、软件管理和文件管理。

## 值得注意

它明确了 AI-RAN 经常借用的 O-RAN 词汇；但 O-RAN 本身是开放智能 RAN 架构，不等同于 AI-RAN。AI-RAN 更强调把 AI workload、加速计算、MLOps 和 RAN workload 统一编排。

## 后续线索

- O-RAN E2/A1/O1 接口的权限模型、时延边界与 AI-RAN dApp 接口值得对照。
- O-RAN SC 的 RIC 实现能否承载 AI-for-RAN 模型生命周期需要单独验证。

依据来源：[[raw/2026-09-26-oran-sc-architecture]]。
