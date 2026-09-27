---
title: NVIDIA AI Aerial Developer Portal
source_url: https://developer.nvidia.com/topics/telecommunications/ai-aerial.md
accessed: 2026-09-26
publisher: NVIDIA
language: en
---

# NVIDIA AI Aerial Developer Portal

## 核心摘录

- NVIDIA 将 AI Aerial 描述为面向 wireless research 与 commercial deployment 的软硬件资源组合。
- 软件组件包括：
  - **Sionna**：开源 ray tracer、link-level PHY simulator 与 system-level simulator，用于 5G/6G 算法原型和研究。
  - **Aerial Framework**：面向 AI-native wireless algorithm development、JAX PHY、MLIR-TensorRT lowering 和 Sionna datasets。
  - **Aerial CUDA-Accelerated RAN**：用于构建 commercial-grade、AI-native、3GPP/O-RAN compliant 5G/6G gNB software 的 SDK；文档说明其 O-RAN 7.2x compliant 并支持 O-DU functionality。
  - **Aerial Omniverse Digital Twin**：数字孪生工具。
- 学习/实验路径覆盖 build/train、simulate、deploy；相关组件包括 pyAerial、Aerial Data Lake、Sionna Research Kit 和 AODT。
- 研究与测试平台：
  - **Sionna Research Kit**：开源为基础，用 GPU 加速库支持 5G/6G 算法原型、训练和部署。
  - **Aerial Testbed（ARC-OTA）**：端到端系统，包含 Aerial CUDA-Accelerated RAN、OAI L2+ 与 5G Core，可运行在 NVIDIA GH200 或 DGX Spark 并进行 over-the-air 测试。
- 部署平台：
  - **Aerial RAN Computer-1**。
  - **ARC-Compact**：面向 cell sites/distributed RAN，强调能效与形态约束。
  - **ARC-Pro**：面向 AI-native 5G/6G，采用 NVIDIA Blackwell RTX PRO GPU，适合升级站点或 greenfield deployment。
- 兼容硬件还包括 Jetson Orin、DGX Spark、Grace Hopper 200、Grace Blackwell 300、L4 GPU 和 RTX PRO GPU。
- NVIDIA 称 AI Aerial supports centralized/distributed AI-RAN deployments for public/private 5G and future 6G networks。

## 阅读定位

这是厂商产品门户，适合理解 NVIDIA 的工具链、硬件形态和学习入口；不能直接等同于第三方验证的商用性能或全网部署规模。
