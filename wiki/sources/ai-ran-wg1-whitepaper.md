---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-wg1-whitepaper.md"
---

# AI-RAN WG1 AI-for-RAN Whitepaper

**身份**：AI-RAN Alliance WG1 技术白皮书，聚焦用 AI/ML 改进 RAN。

**一句话**：AI-for-RAN 把 AI 从“外挂网管优化”推进到链路自适应、信道估计、接收机、MIMO、能耗等 RAN 模块，并规划从参数优化到 AI-native air interface 的演进。

## 核心要点

- 演进阶段：Baseline → Stage 1 参数/控制优化 → Stage 2 替换部分模块 → Stage 3 几乎全块 AI/ML 化。
- 典型例子包括 AI 辅助信道估计、MCS/链路自适应、AI receiver、DeepRx、MIMO receiver、节能接收机。
- DeepRx 输入 FFT 后频域信号，预测 bit log-likelihood ratios，再交给信道译码器。
- 真实部署难点是泛化：仿真信道训练后仍要 over-the-air 验证。

## 值得注意

该白皮书证明 AI-for-RAN 已经从传统网管层的趋势预测深入到物理层；但 Stage 3 是长期愿景，不是当前所有网络都已完成的状态。

## 后续线索

- 需要补充 3GPP Rel-18/19 对 NR air interface AI/ML study item 和 normative work 的最新状态。
- DeepRx、neural receiver 和 neuromorphic receiver 的性能/功耗/成本对比值得单独建页。

依据来源：[[raw/2026-09-26-ai-ran-wg1-whitepaper]]。
