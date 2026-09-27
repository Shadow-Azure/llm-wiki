---
title: AI-RAN WG1 AI-for-RAN Whitepaper
source_url: https://ai-ran.org/documents/AI-RAN-WG1-AI-for-RAN-Whitepaper.pdf
accessed: 2026-09-26
publisher: AI-RAN Alliance
language: en
---

# AI-RAN WG1 AI-for-RAN Whitepaper

## 核心摘录

- AI-for-RAN 的核心是用 AI/ML 改进 RAN 自身：资源调度、链路自适应、信道估计、接收机、能耗和覆盖等。
- AI 原生空口可分三阶段演进：
  1. **Baseline**：传统收发机。
  2. **Stage 1**：保留传统功能块，用 AI/ML 推断参数并优化控制。
  3. **Stage 2**：部分传统收发机块被 AI/ML 块替换。
  4. **Stage 3**：AI/ML 几乎集成到所有块。
- Stage 1 的例子包括按移动速度或视距状态给信道分类，再控制传统信道估计器。
- 文中提到 DeepRx：基于 ResNet 的 CNN OFDM 接收机，输入 FFT 后频域信号，预测每个 bit 的 log-likelihood ratios，再交给信道译码器；可兼容 5G NR resource grid。
- 近期研究包括 GPU 加速平台上的 5G NR neural receiver、只替换 demapper 的混合接收机、面向高段相位噪声的深度学习接收机、neuromorphic AI 原型接收机和 SpikingRx。
- 现有 AI receiver 研究的挑战是训练/验证大量依赖仿真信道数据；来源强调 over-the-air 测试和更合适信道模型对泛化能力很重要。

## 检索备注

- 该 PDF 偏技术细节，适合理解 AI-for-RAN 不是营销词，而是从链路自适应、信道估计、接收机、MIMO、能耗等具体模块逐步切入。
