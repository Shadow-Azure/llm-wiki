---
title: AI-RAN Architecture Overview and Component Definitions v1.2
source_url: https://ai-ran.org/documents/AI-RAN-Architecture-v1.2.pdf
accessed: 2026-09-26
publisher: AI-RAN Alliance
language: en
---

# AI-RAN Architecture Overview and Component Definitions v1.2

## 核心摘录

- AI-RAN Architecture 是把 AI 直接集成进 Radio Access Network（RAN）的功能框架，目标是更智能、高效、自主的网络平台。
- AI-RAN 支持两类主功能：
  - **AI-for-RAN**：用 AI/ML 优化 RAN，包括资源管理、信号质量、流量预测、能耗优化和预测性维护。
  - **AI-on-RAN**：用加速计算 RAN 基础设施在网络边缘承载和加速 AI、Generative AI、Extended Reality 应用。
- 架构分为三个功能域：
  1. **AI-RAN Infrastructure**：加速计算硬件、支持实时/确定性低延迟的 Host OS、CaaS/PaaS、基础设施管理、部署管理和 AI Runtime Platform。
  2. **Workloads（AI-and-RAN）**：gNB（RU/DU/CU）、AI-for-RAN Apps、AI-on-RAN Apps、dUPF 和 UE。
  3. **Management, Control and Orchestration**：AI-RAN Orchestrator、Telemetry/Analytics、MLOps & Data Platform、Multi-Agent Manager。
- gNB 拆成 CU（Centralized Unit）、DU（Distributed Unit）、RU（Radio Unit）。
- dUPF 是分布式 User Plane Function，做 local breakout，让用户数据可直接路由到同址 AI-on-RAN 工作负载。
- AI-RAN Orchestrator 包含 RAN Workload Orchestrator、AI Workload Orchestrator、Platform & Infra Orchestrator、Multi-Agent Manager。
- Observability 组件收集 metrics、logs、traces、events、alarms，可由 RIC、SMO、OSS、EMS 或 SON 等现有 RAN 管理组件实现。
- Multi-Agent Manager 负责管理自主 AI agents 的编排、部署和生命周期，被描述为电信环境 agentic AI 的“control tower”。

## 原文定位

- PDF 第 3 页定义 dual functions 与三类域。
- PDF 第 4-5 页定义基础设施、gNB/CU/DU/RU、AI Apps 与 dUPF。
- PDF 第 5-7 页定义编排、可观测性和 MLOps。
