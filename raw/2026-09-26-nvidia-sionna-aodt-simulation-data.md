---
title: NVIDIA Sionna and Aerial Omniverse Digital Twin
source_url: https://developer.nvidia.com/sionna.md
secondary_sources:
  - https://nvlabs.github.io/sionna/
  - https://developer.nvidia.com/topics/telecommunications/ai-aerial
  - https://docs.nvidia.com/aerial/aodt/welcome/
accessed: 2026-09-26
publisher: NVIDIA
language: en
---

# NVIDIA Sionna and Aerial Omniverse Digital Twin

## Sionna 核心摘录

- Sionna is a GPU-accelerated open-source library for communication-system research.
- It is differentiable and supports gradient-based optimization and machine learning.
- It includes a ray tracer for radio propagation, link-level simulation, and system-level simulation.
- Its differentiable components support neural-network integration and end-to-end communication-system research.
- It combines channel-, link-, and system-level simulation with machine learning and GPU support.

## Aerial Omniverse Digital Twin 核心摘录

- NVIDIA AI Aerial describes AODT as a platform for next-generation network digital twins.
- It enables physically accurate virtual 5G/6G wireless networks, from single towers to full cities.
- The AODT documentation groups workflows into installation, quickstart, AODT client, UI, results/data, tutorials/primers, limitations, and API reference.
- Quickstart includes EM simulation, RAN simulation, and scene-building workflows.
- Results/data documentation covers output schemas, waveform dumps, and result interpretation.
- Tutorials/primers cover EM physics, calibration, and mobility models.

## 阅读定位

这两类工具说明“合成数据”和“数字孪生数据”有重合但不等同：Sionna 偏可微分通信系统/信道-链路-系统仿真；AODT 更强调可标定、可视化、从单站到城市的网络数字孪生。工程上仍要按具体输出（CIR/信道矩阵/IQ/波形/KPI/事件轨迹）核对。
