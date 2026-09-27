---
title: O-RAN SC Architecture Overview
source_url: https://docs.o-ran-sc.org/en/latest/architecture/architecture.html
accessed: 2026-09-26
publisher: O-RAN Software Community
language: en
---

# O-RAN SC Architecture Overview

## 核心摘录

- O-RAN SC architecture follows the O-RAN Alliance architecture.
- The radio side includes **Near-RT RIC**, **O-CU-CP**, **O-CU-UP**, **O-DU**, and **O-RU**.
- The management side includes a **Service Management and Orchestration Framework**, which contains a **Non-RT RIC** function.
- **Near-RT RIC** enables near-real-time control and optimization of O-RAN elements/resources through fine-grained data collection and actions over the **E2** interface.
- **Non-RT RIC** enables non-real-time control/optimization, AI/ML workflows including model training and updates, and policy-based guidance of Near-RT RIC applications.
- **O-CU** hosts RRC, SDAP, and PDCP. It can be split into O-CU-CP and O-CU-UP.
- **O-DU** hosts RLC, MAC, and High-PHY layers based on a lower-layer functional split.
- **O-RU** hosts Low-PHY and RF processing; it includes functions such as FFT/IFFT and PRACH extraction.
- **O1** is the management interface between the Service Management and Orchestration Framework and managed O-RAN elements, supporting FCAPS, software, and file management.
- An **xApp** is an independent software plug-in on the Near-RT RIC platform that extends RAN functions for third parties.

## 阅读定位

这份社区文档适合补充 O-RAN 术语的最小公倍数：它不是 AI-RAN 专属方案，而是解释 AI-RAN 所继承的 O-RAN 架构、RIC、SMO 和开放接口词汇。
