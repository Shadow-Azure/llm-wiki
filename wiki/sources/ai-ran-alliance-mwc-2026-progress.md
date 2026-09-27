---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-alliance-mwc-2026-progress.md"
---

# AI-RAN Alliance MWC 2026 进展

**身份**：AI-RAN Alliance 官方新闻稿、blog 和 2026 MWC demo 目录。

**一句话**：2026 年 AI-RAN 已从白皮书定义进入 33 个多厂商 demo 验证阶段，覆盖空口模型、LLM/agentic 运维、AI/RAN 共享算力、D4AI 数据质量、数字孪生和边缘 AI。

## 核心要点

- 联盟达到 132 家成员、33 个 demo、4 份 industry blueprint；新董事会包括 Qualcomm、SK Telecom、Vodafone。
- 空口侧出现了从仿真走向实时硬件/OTA 的信号：DeepSig/NVIDIA online-learning learned air interface、Nokia/R&S HybridDeepRx、Samsung/Keysight/NVIDIA/HPE PUSCH 工作流。
- AI 与 RAN 共享算力出现三种方法：GPU MIG 动态重配置、中心化 workload orchestrator、去中心化 agent swarm scheduler。
- SynaXG demo 在单块 NVIDIA GH200 上运行 5G FR1/FR2、LLM inference 和 AI training，宣称 36 Gbps、sub-10 ms、20 CC。
- SK Telecom/Keysight 用流量预测保障 RAN QoS，再用 bin-packing 把 LLM/SLM 放到剩余容量；SoftBank 用 Dynamic Scoring Framework 做执行目标评分。
- VIAVI 主导 Data-for-AI；VALOR 被描述为 NTIA 资助、首个与 AI-RAN Alliance 正式关联的 vendor-neutral lab，用于生成、清洗、验证和访问训练数据。
- 官方 blog 明确：LLM 是秒级策略/配置层，不应直接承担 sub-ms RAN L1 信号处理。

## 值得注意

这些是联盟/厂商 demo 数据，通常代表受控环境和可行性验证；尚未等同于全网商用或第三方复核结果。

## 后续线索

- 逐个核对 33 个 demo 的技术报告、吞吐/时延/能耗口径和硬件配置。
- 跟踪 D4AI 数据集是否开放、测试方法是否标准化、MLOps 工具链是否多厂商互操作。

依据来源：[[raw/2026-09-26-ai-ran-alliance-mwc-2026-progress]]。
