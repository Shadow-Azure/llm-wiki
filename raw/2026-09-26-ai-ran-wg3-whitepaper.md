---
title: AI-RAN WG3 AI-on-RAN Whitepaper
source_url: https://ai-ran.org/documents/AI-RAN-WG3-AI-on-RAN-Whitepaper.pdf
accessed: 2026-09-26
publisher: AI-RAN Alliance
language: en
---

# AI-RAN WG3 AI-on-RAN Whitepaper

## 核心摘录

- AI-on-RAN 关注在 RAN 边缘计算设施上承载 AI 应用，并把网络能力与 AI 应用需求对齐。
- 对交互式 AI、机器人、实时视觉等场景，**可预测性**比峰值速率更重要；上链路 micro-outage、移动切换中断和上行吞吐波动是可靠 AI 连接的主要障碍。
- 需要用可验证 KPI 定义 app-agnostic performance levels，包括 bounded latency、uplink throughput floor、reliability target，并配套测量方法以支撑 SLA、事件报告和准入控制。
- 3GPP、O-RAN、GSMA/CAMARA 已有 NEF、NWDAF、RAIE、R1 等接口基础；应避免另起平行接口，而应映射现有框架并补充 uplink telemetry、mobility prediction exposure 等缺口。
- 部署路径是渐进式：
  1. 提升上链路可观测性，识别 AI session 失败位置。
  2. 引入 slicing、verification、admission control 等可执行服务等级。
  3. 待 telemetry 和 policy 成熟后加入 intent-driven API 和 closed-loop orchestration。
- 安全和隐私必须贯穿各阶段：tenant isolation、API access controls、automation bounds、audit trails。
- 结论强调“industry does not need to wait for 6G”，可在 5G/5G-Advanced 网络上先行构建差异化 AI 连接能力。

## 附录要点

- 附录给出 AI workload profiles 示例，把 AI 应用类型映射到网络能力。
- 实时视频感知/控制类场景强调 bounded latency、session continuity、上链路突发、cell-edge 稳定性；远程专家或工业场景强调 sustained uplink throughput、coverage variation 下的稳定性和切片预留。
