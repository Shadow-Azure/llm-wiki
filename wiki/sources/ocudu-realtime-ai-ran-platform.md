---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ocudu-realtime-ai-ran-platform.md"
---

# OCUDU dApp Platform

**身份**：arXiv 论文，提出面向 real-time AI-RAN 的 open runtime 和 E3 interface。

**一句话**：AI-RAN 的硬核问题不是“能否训练模型”，而是如何让第三方 AI 应用安全地进入 5G DU 内低于 10 ms、甚至 scheduler 100 μs 级别的实时控制面。

## 核心要点

- 有价值的 ML 区间包括 link adaptation、per-slot scheduling、channel estimation、receiver。
- OCUDU 定义三类 timing contracts：GPU receive chain、scheduler 100 μs admitted deadline、非阻塞 observer。
- dApp 被签名后才可执行；常规路径不被替代。
- 该平台强调权限类型化、安全治理和实时契约，而非简单容器化。

## 值得注意

它把 AI-RAN 与普通边缘 AI 区分开：普通边缘推理可以容忍几十毫秒到秒级波动，RAN 内实时推理必须受严格截止时间和隔离约束。

## 后续线索

- OCUDU 与 O-RAN E2/RIC、NVIDIA Aerial 的兼容关系需要继续核实。
- Class A/B/C 安全边界和调度公平性值得精读论文。

依据来源：[[raw/2026-09-26-ocudu-realtime-ai-ran-platform]]。
