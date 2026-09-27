---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-nvidia-ai-aerial-developer-portal.md"
---

# NVIDIA AI Aerial Developer Portal

**身份**：NVIDIA AI Aerial 官方开发者门户。

**一句话**：AI Aerial 用 Sionna、Aerial Framework、CUDA-Accelerated RAN、数字孪生和 ARC 系列硬件，把 5G/6G 研究、仿真、训练和部署串成一条 GPU 生态链。

## 核心要点

- Sionna 覆盖 ray tracing、link-level PHY 和 system-level simulation，适合研究原型。
- Aerial CUDA-Accelerated RAN 定位为 commercial-grade、AI-native、3GPP/O-RAN compliant 的 gNB SDK；官方说明 O-RAN 7.2x compliant 并支持 O-DU。
- ARC-OTA 把 Aerial RAN、OAI L2+/5G Core 放在 GH200 或 DGX Spark 上做 OTA 测试。
- ARC-Compact 面向站点级/distributed RAN；ARC-Pro 面向 AI-native 5G/6G 和升级/greenfield 站点。
- 页面提供 build/train、simulate、deploy 的工具入口，包括 pyAerial、Aerial Data Lake 和 Aerial Omniverse Digital Twin。

## 值得注意

这是厂商门户：适合理解 NVIDIA 路线和工具链，不能单独证明实际商用规模、功耗、TCO 或全网性能。

## 后续线索

- Aerial CUDA-Accelerated RAN 的开源代码范围、许可证、O-RAN 接口完成度值得核对。
- Sionna/AODT 到 OTA 的数据一致性和模型漂移治理需要找实测论文。

依据来源：[[raw/2026-09-26-nvidia-ai-aerial-developer-portal]]。
