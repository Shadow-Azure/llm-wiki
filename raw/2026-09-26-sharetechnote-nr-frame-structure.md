---
title: ShareTechnote 5G/NR Frame Structure
source_url: https://www.sharetechnote.com/html/5G/5G_FrameStructure.html
accessed: 2026-09-26
publisher: ShareTechnote
language: en
---

# ShareTechnote 5G/NR Frame Structure

## 核心摘录

- 页面说明 5G NR 帧结构依据 3GPP 38.211/38.300/38.101 等资料组织。
- NR 支持多种 numerology/subcarrier spacing：15, 30, 60, 120, 240, 480, 960 kHz；LTE 使用 15 kHz。
- 层级为 radio frame → subframe → slot；1 radio frame 为 10 ms，1 subframe 为 1 ms，slot 数随 numerology 变化。
- normal CP 下 1 slot 有 14 个 OFDM symbols。
- 1 resource block 有 12 个 subcarriers；slot 在时间域的长度随 subcarrier spacing 增大而变短。
- NR resource grid 的物理尺寸随 numerology 变化；downlink/uplink 的 maximum RB configuration 在 FR1/FR2 中不同，RF 还要额外考虑 guardband。

## 阅读定位

这是技术笔记/教学页，适合直观理解 NR 时间-频率资源栅格；正式设计仍应回到 3GPP 38.211、38.300、38.101 等规范原文。
