---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-softbank-ai-for-ran-verification.md"
---

# SoftBank AI-for-RAN 实测

**身份**：SoftBank 2025-03 官方新闻稿，验证三个 AI-for-RAN 用例。

**一句话**：SoftBank 用 AI 做上行信道插值、SRS 预测和 MAC 调度，分别报告约 20%、13% 和 8% 的吞吐增益。

## 核心要点

- UL Channel Interpolation：约 20% UL 用户吞吐增益， poor network-quality 场景，实验室手机测试。
- SRS Prediction：MLP 在系统级仿真中预测非接收间隔的 SRS，80 km/h 设备 DL 吞吐约 +13%。
- AI-driven MAC Scheduling：MLP 做 MU-MIMO 用户配对/MAC 调度，平均用户吞吐约 +8%。
- 背景判断是多模态 GenAI 会提高 UL/DL 比例，AI-RAN 需要特别关注上行。
- SoftBank 构建系统级仿真平台评估 AI 对 RAN 性能的影响。

## 值得注意

三个场景分别是实验室实测和系统级仿真，不能直接等价为全网商用；仍需看小区负载、干扰、移动速度、硬件和功耗。

## 后续线索

- 找模型的训练数据来源、在线/离线更新、失败回退策略。
- 复查 UL/DL 增益是否与 GenAI 上行流量模型一致。

依据来源：[[raw/2026-09-26-softbank-ai-for-ran-verification]]。
