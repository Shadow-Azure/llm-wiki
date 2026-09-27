---
title: AI in the Mobile RAN: A Transformational Opportunity for Telcos
source_url: https://www.softbank.jp/corp/set/data/technology/research/news/075/pdf/2025_ai-ran_opportunity_avidthink_softbank.pdf
accessed: 2026-09-26
publisher: AvidThink / sponsored by SoftBank
language: en
---

# AvidThink: AI in the Mobile RAN

## Architecture and methods

- Evolution: SON → AI/ML RAN optimization → AI-native RAN. AI-native approaches use larger datasets and continuous learning for channel estimation, beamforming, traffic prediction and network automation.
- Legacy RAN barriers: rigidity, node-based design, closed proprietary software/hardware, vendor lock-in.
- Migration pattern: hybrid architecture with well-defined interfaces between AI-enabled components and traditional RAN, avoiding rip-and-replace.
- O-RAN contributes Non-RT RIC for >1s policy/analytics and Near-RT RIC for 10 ms–1s control loops; xApps/rApps host third-party optimization.
- MLOps capabilities include data collection, model training and inference. Leading topology rethink: SoftBank's AI-and-RAN unified distributed computing.

## Use cases and operator/vendor data

- **China Mobile + ZTE**: AI algorithms improved live streaming, HD video and VIP user guarantees by more than **15%** in a joint RAN-native AI solution.
- **SoftBank**: UL channel interpolation +20% in poor conditions; SRS prediction +13% DL at 80 km/h; AI MAC scheduling +8% average throughput.
- **Ericsson**: Intelligent RAN Automation portfolio reported about **15% average spectral-efficiency gain**.
- **Nokia**: MantaRay SON at Vodafone Idea reported up to **700,000 autonomous changes/day** for RAN resiliency and performance.
- **Vodafone**: set an original goal to use AI to prevent 50% of faults before customer impact; AI features for RAN since 2017 according to AI-RAN Alliance spotlight.

## Edge / AI-on-RAN economics

- NVIDIA's 2021 “AI-on-5G” idea: colocate GPU servers at 5G sites or centralized RAN hubs to run RAN software and AI inference.
- NTT DOCOMO deployed the world's first GPU-accelerated 5G Open RAN with NVIDIA, Fujitsu and Wind River, proving GPU baseband feasibility.
- SoftBank AITRAS dynamically allocates telco and generative AI resources; SoftBank/NVIDIA example scenario reported **219% five-year ROI** and **5x return on CAPEX**.

## Hardware and training-inference infrastructure

- GPUs are attractive for matrix operations, vRAN and AI on one platform; ASICs offer power/performance optimization but less versatility; FPGAs are reconfigurable for specific AI/L1 offload.
- Likely end state is hybrid: GPU for vRAN/AI, plus ASIC/FPGA for specialized functions.
- Site constraints: power, cooling and real-time segregation/scheduling are non-trivial when adding GPUs to cell sites.
- Cloud vs distributed compute depends on latency, privacy and application. Federated learning is promising for training across decentralized RAN data without centralizing raw data.

## Data, governance and barriers

- Data collection/training/inference are difficult; carriers have privacy constraints and startups need access to curated, normalized, sanitized training corpora.
- AI-RAN Alliance and O-RAN Alliance can help curate, normalize, sanitize and host datasets.
- Key barriers: vendor lock-in, legacy inertia, telco AI talent gap, real-time orchestration, data governance, security/adversarial risks, ROI uncertainty and regulatory trust.
- Operators need business-linked KPIs: OPEX reduction, customer experience, new revenue—not only model accuracy.
