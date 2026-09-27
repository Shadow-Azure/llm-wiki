---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-platform-infrastructure-orchestrator.md"
---

# AI-RAN Platform Infrastructure Orchestrator

**身份**：AI-RAN Alliance 的平台基础设施编排白皮书。

**一句话**：AI-RAN 的公共底座不是“只装 Kubernetes”，而是用云原生平台封装实时节点、异构加速器、多站点和多租户，让 RAN 与 AI workload 可被统一调度。

## 核心要点

- Kubernetes 是基础，但必须补上确定性低延迟、加速器分区、调度约束、可观测性和高可用。
- 统一抽象 CPU、GPU、DPU、SmartNIC、FPGA/ASIC 等资源。
- 关键能力包括 IaC、站点模板、GPU partitioning、real-time node 配置、NUMA/CPU pinning、多集群 fleet management、闭环自动化、多租户隔离和审计。
- 升级、备份、容灾和自愈直接影响电信级可用性。

## 值得注意

AI-RAN 的工程难点在“两种脾气不同的负载”：AI 追求吞吐和弹性，RAN L1/L2 追求确定性时延和可预测性。

## 后续线索

- GPU MIG/time-slicing/preemption 对 RAN L1 的保障能力需要单独验证。
- 与电信云、NFV、O-RAN SMO 的编排边界需要补充。

依据来源：[[raw/2026-09-26-ai-ran-platform-infrastructure-orchestrator]]。
