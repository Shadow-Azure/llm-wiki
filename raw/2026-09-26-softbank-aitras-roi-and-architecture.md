---
title: SoftBank AITRAS / AI-RAN Return Expansion Mechanism
source_url: https://www.softbank.jp/en/corp/technology/research/story-event/073/
secondary_sources:
  - https://ai-ran.org/member-spotlights/softbank
accessed: 2026-09-26
publisher: SoftBank Corp. / AI-RAN Alliance
language: en
---

# SoftBank AITRAS ROI and architecture

## Business architecture

- AI-RAN integrates RAN control functions and AI server functions on the same hardware platform, enabling mobile services and GPUaaS from a unified platform.
- During off-peak mobile traffic, server resources can shift to AI workloads; if GPUaaS demand is strong, operators can add capacity and allocate more resources to AI.
- Initial target: urban areas with concentrated population, industry, AI demand and GPUaaS demand. SoftBank uses a C-RAN/aggregation-site architecture where servers control multiple cell-site RUs.

## Quantified return scenario

- Case study: covering about 600 cells in urban Tokyo / Shibuya area.
- RAN Heavy scenario: 67% RAN / 33% AI; five-year ROI up to **33%**; revenue-to-CAPEX ratio about **2x**.
- AI-heavy scenario: larger AI allocation increases TCO but adds GPUaaS revenue; five-year ROI **219%**; revenue-to-CAPEX ratio about **5x**.
- NVIDIA Aerial RAN Computer-1 / GB200-NVL2 is cited as a deployment platform; GPUaaS estimate **USD 20/hour** and **USD 15,000/month/server**.
- Important caveat: these are SoftBank/NVIDIA scenario estimates, not independently audited commercial results.

## AITRAS platform details

- AI-RAN Alliance spotlight describes AITRAS as an integrated AI-RAN solution on **NVIDIA GH200 Grace Hopper Superchips** and **Red Hat OpenShift**.
- It converges AI and RAN workloads with multi-tenancy and real-time-demand-based dynamic allocation while preserving carrier-grade performance.
- AITRAS was deployed at NVIDIA headquarters in Santa Clara for smart-city, industrial automation and robotics edge AI applications.
- Joint SoftBank/Ericsson AI-RAN orchestration R&D is reported to have achieved about **30% 5G throughput improvement**.
- SoftBank is developing a **Large Telecom Model (LTM)** trained on network data and operational know-how for automated network operations; it plans integration with AITRAS.
- SoftBank open-sourced the AITRAS Orchestrator **Dynamic Scoring Framework** in February 2026.
