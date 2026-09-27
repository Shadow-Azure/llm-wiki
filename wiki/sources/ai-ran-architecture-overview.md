---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-architecture-overview.md"
---

# AI-RAN Alliance 架构总览 v1.2

**身份**：AI-RAN Alliance 的官方架构概览，回答“AI-RAN 是什么、由哪些功能域组成”。

**一句话**：AI-RAN 是把 AI 直接集成进 RAN 的功能框架；官方把其双重功能定义为优化 RAN 的 **AI-for-RAN** 和在 RAN 设施上跑应用的 **AI-on-RAN**。

## 核心要点

- 三层结构：加速基础设施、workloads（AI-and-RAN）、管理/控制/编排。
- RAN 工作负载拆为 RU、DU、CU；AI-for-RAN Apps 可靠近 gNB/CU/DU；AI-on-RAN Apps 与 RAN workload 并行。
- dUPF 做 local breakout，使边缘 AI 应用不必把流量全部绕回集中云。
- 编排层包含 RAN Workload Orchestrator、AI Workload Orchestrator、Platform & Infra Orchestrator、Multi-Agent Manager。
- 可观测性可复用或包含 RIC、SMO、OSS、EMS、SON 等既有 RAN 管理组件。
- MLOps & Data Platform 覆盖数据准备、训练、评估、部署、监控和治理。

## 值得注意

官方文档没有把 AI-RAN 简化成“用大模型运维基站”，而是强调 AI/ML、加速计算、实时 RAN、边缘应用、编排和数据闭环的组合。

## 后续线索

- 与 O-RAN Alliance 的 RIC/SMO 接口边界和实现互操作性值得单独调研。
- v1.2 与后续版本中 Multi-Agent Manager 的安全边界、Agent 权限模型需要跟踪。

依据来源：[[raw/2026-09-26-ai-ran-architecture-overview]]。
