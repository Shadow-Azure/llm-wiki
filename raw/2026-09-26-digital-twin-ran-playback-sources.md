---
title: Digital Twin RAN Playback Technical Sources
source_url: https://docs.nvidia.com/aerial/aodt/welcome/
secondary_sources:
  - https://docs.nvidia.com/aerial/aodt/quickstart/
  - https://docs.nvidia.com/aerial/aodt/results-and-data/
  - https://docs.nvidia.com/aerial/aodt/limitations/
  - https://arxiv.org/abs/2308.02644
  - https://arxiv.org/abs/2404.17317
  - https://arxiv.org/abs/2605.29772
  - https://arxiv.org/abs/2509.10097
accessed: 2026-09-26
publisher: NVIDIA / arXiv authors
language: en
---

# Digital Twin RAN Playback Technical Sources

## NVIDIA AODT execution model

- Quickstart defines four workflows:
  1. **EM mode**: generate channel coefficients for a configured scenario without running the 5G RAN Tx/Rx chain.
  2. **RAN mode**: run channel generation plus the 5G RAN L1/L2 stack, producing deployment telemetry.
  3. **GIS pipeline**: convert OSM/CityGML maps into USD and 3D Tiles for simulation/visualization.
  4. **Calibration**: tune parameters so field measurements align with simulation results.
- Batched EM supports duration and slot modes.
- Batched RAN runs the EM solver and 5G PHY Tx/Rx; the reviewed documentation says it is slot-mode-only to preserve 5G timelines.
- Interactive EM mode connects a system-level simulator to an AODT worker over gRPC; the SLS requests channel data for selected time steps. RAN is not supported in interactive mode.
- Results are structured products. Batched outputs are S3-compatible Parquet tables registered in an Iceberg catalog. RAN waveform dumps are optional H5 files for received I/Q samples.
- Results schemas include setup records, channel results, ray paths, telemetry, and RAN configuration fields.
- Calibration covers UE orientation, RU orientation, RU beams, building/surface materials, vegetation materials, and maximum trainable simulation size.

## NVIDIA AODT limitations v1.5.1 excerpts

- EM limits include up to 40 million rays per RU over up to 25 km² on L40S; up to 5 scattering events per ray, or 10 with transmission; up to 4096 wideband CFR samples; up to 10,000 UEs; 64 RU antenna elements; 8 UE antenna elements.
- RAN simulation limits include 100 MHz / 273 PRBs, 30 kHz SCS, 64-antenna RU or 4-antenna RU, 4-antenna UE.
- With a 64-antenna RU, regularized ZF beamforming is applied. With 4 antennas, SU-MIMO is supported and beamforming is not applied.
- PRBs are scheduled at PRB-group level, 4 PRBs per group.
- HARQ can operate per slot with one new transmission plus three retransmissions; retransmission is non-adaptive and reuses original PRB allocation, layer selection, and MCS.
- MCS selection uses a SINR-to-MCS lookup table derived for single-layer AWGN and may be inaccurate for multi-layer transmission.

## O-RAN digital twin papers

### Digital Twin for O-RAN Towards 6G

- arXiv: <https://arxiv.org/abs/2410.02954v1>
- O-RAN supports dynamic resource adaptation, but real-time resource orchestration remains difficult.
- Digital twin can test/analyze complex systems and support dynamic O-RAN operation.
- Challenges include data exchange, training-data availability, network dynamics, processing limits, interdisciplinary collaboration, and lack of standardized methods.

### Colosseum: The Open RAN Digital Twin

- arXiv: <https://arxiv.org/abs/2404.17317v1>
- Practical Open RAN needs datasets for AI/ML training, facilities that test models without disrupting production networks, continuous/automated RAN software validation, and integration testing.
- Colosseum is presented as an Open RAN digital twin/tutorial.

### Network Digital Twin for Open RAN

- arXiv: <https://arxiv.org/abs/2308.02644v1>
- Network Digital Twin covers physical and logical components, including infrastructure, protocols, and algorithms.
- It is a real-time virtual representation supporting emulation, test, optimization, monitoring, and analysis of a new configuration in a risk-free environment.

### ARIADNE: AI-RAN Informed Link Adaptation

- arXiv: <https://arxiv.org/abs/2605.29772v2>
- Describes digital twins as a safe playground for testing online AI/ML before RAN deployment.
- Integrates an online RL module with SIONNA for link adaptation.
- Claims up to 11% and 20% spectral-efficiency improvements over industry-standard and state-of-the-art methods respectively; these are paper/claimed results.

### Hybrid xApps and Digital Twin for Energy Efficiency

- arXiv: <https://arxiv.org/abs/2509.10097v1>
- Proposes hybrid heuristic/unsupervised-ML xApps and digital twin integration for large-scale Open RAN energy efficiency while preserving QoS.

## 阅读定位

数字孪生不是“只生成一堆射频样本”。AODT 的官方分类已经显示：EM mode 是信道生成；RAN mode 才包含 5G L1/L2 stack 和 deployment telemetry；calibration 负责用现场测量把虚拟环境拉近真实。
