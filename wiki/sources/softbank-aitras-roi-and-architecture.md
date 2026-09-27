---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-softbank-aitras-roi-and-architecture.md"
---

# SoftBank AITRAS ROI 与架构

**身份**：SoftBank AI-RAN 商业机制文章 + AI-RAN Alliance member spotlight。

**一句话**：AITRAS 把 RAN 控制和 AI/GPUaaS 放进同一 GH200 + OpenShift 平台，SoftBank 用东京城区场景估计 AI-heavy 配置可到 219% 五年 ROI 和约 5x revenue/CAPEX。

## 核心要点

- 架构：NVIDIA GH200 Grace Hopper Superchips + Red Hat OpenShift；multi-tenancy；按实时需求动态分配 AI/RAN。
- 目标场景：城市聚合站/C-RAN，先服务人口、产业和 GPUaaS 需求密集区域。
- RAN Heavy 67%/33% 场景：五年 ROI 最高 33%，revenue/CAPEX 约 2x。
- AI-heavy 场景：五年 ROI 219%，revenue/CAPEX 约 5x；覆盖东京约 600 小区。
- GPUaaS 估算引用 NVIDIA GB200-NVL2：USD 20/hour、USD 15,000/month/server。
- AITRAS 已部署在 NVIDIA Santa Clara 总部，用于 smart city、工业自动化和机器人；SoftBank/Ericsson orchestration R&D 报告约 30% 5G 吞吐提升。
- SoftBank 正在开发 Large Telecom Model（LTM），用网络数据和运维 know-how 训练，计划接入 AITRAS；2026-02 开源 Dynamic Scoring Framework。

## 值得注意

219%/5x 是场景模型而非已审计商业结果；GPUaaS 价格、负载率、电力、机房改造、RAN KPI 保障和数据合规都会改变结论。

## 后续线索

- 跟踪 Dynamic Scoring Framework 的评分维度、隔离策略和开源实现。
- 找独立审计的 GPUaaS 利用率、站点改造成本、能耗和 SLA 数据。

依据来源：[[raw/2026-09-26-softbank-aitras-roi-and-architecture]]。
