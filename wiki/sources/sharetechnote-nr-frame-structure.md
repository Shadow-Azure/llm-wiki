---
type: source
created: 2026-09-26
updated: 2026-09-26
sources:
  - "raw/2026-09-26-sharetechnote-nr-frame-structure.md"
---

# ShareTechnote NR Frame Structure

**身份**：ShareTechnote 的 5G NR 帧结构技术笔记，引用 3GPP 38.211/38.300/38.101。

**一句话**：NR 用可变的 numerology 组织时间-频率资源栅格：radio frame 10 ms、subframe 1 ms，slot 数随 subcarrier spacing 变化，normal CP 每 slot 14 个 OFDM symbol，1 RB 有 12 个 subcarrier。

## 核心要点

- NR 支持 15/30/60/120/240/480/960 kHz subcarrier spacing。
- subcarrier spacing 越大，slot 时间越短，这解释了“大 numerology 更利于低时延，但也带来更大频率粒度和相位噪声压力”的直觉。
- normal CP 下 1 slot 有 14 个 OFDM symbols；这是调度器最常操作的时间粒度之一。
- 1 RB 有 12 个 subcarriers；实际 RF 带宽比配置 RB 总和更宽，因为要考虑 guardband。

## 值得注意

这是技术教学页，不是 3GPP 官方原文；本库用它降低资源栅格概念门槛，正式引用规范时应回到 3GPP 原文。

## 后续线索

- 补 3GPP 38.211 resource grid 原文表格和 38.101 FR1/FR2 maximum RB 表。
- 单独整理 BWP、CORESET、mini-slot/symbol-level scheduling 和 TDD pattern。

依据来源：[[raw/2026-09-26-sharetechnote-nr-frame-structure]]。
