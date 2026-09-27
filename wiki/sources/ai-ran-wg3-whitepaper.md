---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-ai-ran-wg3-whitepaper.md"
---

# AI-RAN WG3 AI-on-RAN Whitepaper

**身份**：AI-RAN Alliance WG3 白皮书，聚焦 AI-on-RAN 和面向 AI 应用的网络能力。

**一句话**：AI-on-RAN 的商业点不是让平均网速更高，而是给机器人、实时视觉、交互式 AI 等 workload 提供可验证的 bounded latency、uplink throughput floor 和 reliability。

## 核心要点

- AI 应用最怕不是峰值速率不足，而是上行 micro-outage、切换中断、上行波动和控制循环抖动。
- “premium AI connectivity”必须可测量、可审计、可执行，否则无法形成 SLA 和商业产品。
- 应复用 3GPP NEF/NWDAF、O-RAN RAIE/R1、GSMA/CAMARA 等既有接口，避免碎片化。
- 部署路径：上链路可观测性 → 服务等级/slicing/准入控制 → intent-driven API 和闭环编排。
- 安全必须内嵌：租户隔离、API 访问控制、automation bounds、审计追踪。

## 值得注意

白皮书明确说不需要等 6G，5G/5G-Advanced 就可以开始建设差异化 AI 连接；这把 AI-RAN 从 6G 课题拉回到现实演进路径。

## 后续线索

- 附录中的 AI workload profiles 可扩成一个“AI 应用网络画像”概念页。
- CAMARA、NEF、NWDAF、RAIE 如何拼装成 AI-on-RAN API 链路值得深挖。

依据来源：[[raw/2026-09-26-ai-ran-wg3-whitepaper]]。
