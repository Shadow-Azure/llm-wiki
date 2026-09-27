---
title: AI-RAN Alliance MWC 2026 Progress and Demonstrations
source_url: https://ai-ran.org/press-releases/mwc-2026-momentum
secondary_sources:
  - https://ai-ran.org/blog/ai-native-ran-from-white-papers-to-validation
  - https://ai-ran.org/demonstrations?tab=mwc-2026
accessed: 2026-09-26
publisher: AI-RAN Alliance
language: en
---

# AI-RAN Alliance MWC 2026 Progress and Demonstrations

## Milestones

- 2026-02-26 press release: 132 members, 33 Innovation Demos, four Industry Blueprints.
- New Board Members include Qualcomm, SK Telecom and Vodafone; Japan MIC joined; collaboration with South Korea's AINA / MSIT supports use cases and trial contributions.
- Four publications: AI-RAN Reference Architecture; AI/ML techniques and models for RAN performance; AI-RAN Platform & Infrastructure Orchestration; AI-on-RAN differentiated connectivity.
- Official framing shifts from experimentation to AI-native RAN implementations across physical layer, orchestration and edge applications.

## MWC 2026 demo signals

### AI-for-RAN / air interface models

- **Learned Air Interface with Online Learning** — DeepSig + NVIDIA: two-sided autoencoder replaces traditional QAM modulation; runs live on GPU-accelerated gNB with online learning on NVIDIA DGX Spark; shares L1 and AI-RAN workload.
- **HybridDeepRx ML-Based Digital Post-Distortion Receiver** — Nokia + Rohde & Schwarz: hardware-in-the-loop verification of an AI receiver compensating real UE power-amplifier nonlinearities.
- **AI-based Workflow for PUSCH Channel Estimation** — Keysight + Samsung + NVIDIA + HPE: low-SNR coverage problem and Data-for-AI workflow aligned to the architecture MLOps/data platform.
- **AI-Based SRS Channel Estimation** — SK Telecom + Samsung Electronics.
- **AI Transmit Diversity and Model Lifecycle Management** — MediaTek.
- **AI Agent for Real-Time Wireless Link Adaptation** — AsiaInfo.
- **AI Switching Mechanism** — Northeastern University + SoftBank: dynamically switches between AI and non-AI channel estimators by mobility pattern.
- **User-Level Optimization of Mobility and Connection Stability** — Samsung Electronics + KT.

### LLM / agentic RAN

- **LLM-Based Robust 5G Beamforming** — SUTD + VIAVI + Yonsei: context-aware interference mitigation and power saving.
- **ALLSTaR** — Northeastern University: LLM generates scheduler code from research papers and deploys it as dApps to a production 5G network.
- **AI-Driven RAN Management via SMO** — Qualcomm: agentic interface on production network; energy efficiency use case.
- **Agentic AI Enabled Energy Efficiency Optimization for O-RAN** — University of Leeds + VIAVI + AWS + NVIDIA.
- **Large Telecom Models** — Northeastern University + SoftBank + Keysight + zTouch: agentic interpretation of operator intent.
- Architectural distinction from official blog: LLMs operate at seconds-scale latency; RAN L1 is sub-millisecond. Therefore LLMs are used for policy generation/configuration, not direct L1 signal processing.

### AI-and-RAN infrastructure / training-inference sharing

- **Dynamic Resource Sharing Between AI and RAN on GH200** — SynaXG: runs 5G FR1/FR2 with LLM inference and AI training on one NVIDIA GH200. Uses policy-driven MIG reconfiguration. Claimed 36 Gbps throughput, sub-10 ms latency, 20 component carriers; described as first AI-RAN implementation on FR2 bands.
- **AI-RAN Blueprint Platform** — zTouch + Northeastern University + Red Hat: Kubernetes/IaC reproducible foundation.
- **AI-RAN Orchestration** — zTouch + Northeastern University: real-time compute profiling, multi-cluster discovery, workload placement.
- **AI-and-RAN Workload Orchestrator** — SK Telecom + Keysight: clusters multiple AI-RAN servers; traffic prediction allocates RAN resources with QoS; bin-packing schedules LLM/SLM workloads on remaining capacity.
- **AITRAS Orchestrator** — SoftBank: Dynamic Scoring Framework scores candidate execution targets with configurable performance/energy criteria.
- **Agentic Workload Swarm Scheduler** — GlobalLogic + Keysight: AI Workload Agents at each gNodeB communicate peer-to-peer with A2A/MCP; Swarm Scheduler uses fitness-based distributed placement.

### Data-for-AI, testing and digital twins

- **Data Quality Validation for AI-RAN** — VIAVI.
- VIAVI leads the Data-for-AI Task Group and says VALOR is NTIA-funded and the first lab formally affiliated with AI-RAN Alliance; it provides a vendor-neutral environment to generate, clean, validate and access training data.
- VIAVI also participates in the Test Methodology group for reproducible, trustworthy AI-RAN results.
- Digital-twin demos include University of Oulu + NVIDIA multimodal digital twins, Hanyang University + Keysight beam tracking, Hanyang University + VIAVI tilt optimization, and Northeastern University + VIAVI AIRMap AI-generated radio maps.

### AI-on-RAN / edge inference

- **Edge AI-Powered Autonomous Driving** — SK Telecom.
- **AI-on-RAN Orchestration for Autonomous Systems** — SUTD + Red Hat + QCT + NeuroRAN: VLM inference for humanoid robots with SLA-based orchestration.
- **Adaptive Privacy-Preserving Edge AI Model Partitioning via Spectrum Sensing** — SUTD + Keysight + LITEON + NeuroRAN.
- **Intrusion Detection in Industrial Shop Floors Using Edge AI and ISAC** — Albesmart + LITE-ON.
- **Edge AI for Resilient Coastal Communications** — Amini.
- **ISAC Object Detection and Tracking** — Tiami Networks + Vodafone.

## Five future directions named by official blog

1. Scalable model lifecycle management.
2. End-to-end energy optimization, jointly accounting for AI inference energy and energy saved by AI.
3. Digital-twin infrastructure at scale.
4. Orchestration-stack convergence.
5. Commercial deployment path.
