---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-digital-twin-ran-playback-sources.md"
---

# RAN 数字孪生回放技术来源

**身份**：NVIDIA AODT 官方文档与 O-RAN 数字孪生 arXiv 论文样本。

**一句话**：RAN 数字孪生回放数据由“场景/拓扑/配置 + EM/RAN 仿真 + 标定 + 时间轴运行记录”组成，能输出信道、射线、调度、telemetry、KPI 和反事实试验结果。

## 核心要点

- AODT 明确区分 EM mode（信道系数）和 RAN mode（EM + 5G RAN L1/L2 + deployment telemetry）。
- Batched 输出存成 S3-compatible Parquet 表并注册到 Iceberg catalog；RAN 可选 H5 waveform dump 存 IQ 样本。
- 结果 schema 覆盖 setup、channel results、ray paths、telemetry、RAN configuration。
- Calibration 用现场测量校准 UE/RU orientation、beams、建筑/植被材质等参数。
- O-RAN 数字孪生论文强调覆盖物理/逻辑组件，在不影响生产网的环境中做 emulation、test、optimization、monitoring 和 analysis。
- ARIADNE 将 RL link adaptation 接入 Sionna/数字孪生环境，宣称最高 11%/20% 频谱效率增益。

## 值得注意

AODT v1.5.1 的 RAN mode 有明确限制：100 MHz/273 PRB、30 kHz SCS、固定 HARQ 重传规则、SINR-to-MCS 查表等。数字孪生结果必须带版本、配置和标定误差，不能直接等同于生产网 A/B。

## 后续线索

- 精读 AODT `results-schemas` 的字段定义。
- 对比 AODT、Colosseum、HIL/OTA 在同一 O-RAN xApp 上的结论一致性。

依据来源：[[raw/2026-09-26-digital-twin-ran-playback-sources]]。
