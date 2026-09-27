---
title: The OCUDU dApp Platform: An Open Runtime and E3 Interface for Real-Time AI-RAN
source_url: https://arxiv.org/abs/2609.07843v1
accessed: 2026-09-26
publisher: arXiv
language: en
---

# The OCUDU dApp Platform: An Open Runtime and E3 Interface for Real-Time AI-RAN

## 核心摘录

- 论文指出机器学习在 5G DU 中低于 10 ms 的区间最有价值，包括 link adaptation、per-slot scheduling、channel estimation 和 receiver。
- OCUDU dApp Platform 是 open runtime 和 E3 interface，让已签名的 AI-RAN applications 在 production DU 内执行。
- 论文定义三类 timing contracts：
  1. **Class A**：驻留在 GPU receive chain 上。
  2. **Class B**：进入 scheduler 的 100 μs admitted deadline。
  3. **Class C**：非阻塞 observer，结果可被 scheduler 消费。
- 常规路径不被替代；所有权限被 typed，平台提供签名、安全与治理约束。
- 该论文说明 AI-RAN 不只是把模型放在边缘服务器上，真正的难点是把推理安全地放进非常紧的 RAN 实时循环。
