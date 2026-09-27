---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-nvidia-sionna-aodt-simulation-data.md"
---

# NVIDIA Sionna 与 AODT 仿真数据

**身份**：NVIDIA Sionna 官方页面和 AI Aerial/AODT 文档摘录。

**一句话**：Sionna 用可微分、GPU 加速的 ray tracing/link/system 仿真生成通信系统训练与评估数据；AODT 进一步把 EM/RAN 仿真、标定、移动性和结果 schema 组织成从单站到城市的数字孪生工作流。

## 核心要点

- Sionna 是 GPU 加速开源库，支持 radio-propagation ray tracing、link-level simulation、system-level simulation 和神经网络集成。
- 可微分仿真支持梯度优化，适合端到端通信研究和可复现实验。
- AODT 定位为 physically accurate virtual 5G/6G network digital twin，覆盖单塔到城市。
- AODT 工作流包括 EM simulation、RAN simulation、scene building、calibration、mobility model、telemetry/YAML configuration、output schemas 和 waveform dumps。

## 值得注意

“合成信道数据”偏单样本/链路信号；数字孪生偏网络系统状态、时间轨迹和反事实试验。两者在 RAN simulation 下会有重叠，但数据契约和用途不同。

## 后续线索

- 核对 AODT waveform dumps/output schema 的具体字段。
- 用同一场景对比 Sionna、AODT、HIL/OTA 的分布差异和模型增益。

依据来源：[[raw/2026-09-26-nvidia-sionna-aodt-simulation-data]]。
