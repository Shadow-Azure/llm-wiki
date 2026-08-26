---
title: Grok 4.5 × Cursor 联合训练（Grok 4.5 trained alongside Cursor）
layer: model
source_urls:
  - https://x.ai/news/grok-4-5
  - https://cursor.com/blog/grok-4-5
  - https://www.cnbc.com/2026/06/16/spacex-spcx-cursor-acquisition-ipo.html
  - https://www.reuters.com/legal/transactional/spacex-buy-anysphere-60-billion-2026-06-16/
  - https://techcrunch.com/2026/07/08/spacexai-releases-grok-4-5-which-elon-describes-as-an-opus-class-model/
  - https://apidog.com/blog/grok-4-5-cursor-training-data/
accessed: 2026-08-26
local_pdf:
---

# Grok 4.5 × Cursor 联合训练

> 一句话：SpaceXAI（原 xAI 品牌）2026 年 7 月发布的前沿模型 Grok 4.5，官方明确"与 Cursor 联合训练"，训练语料中混入了数万亿 token 的 Cursor 编辑器用户交互数据（开发者与代码库、软件工具的真实交互），是编辑器用户会话首次规模化进入前沿模型训练管道的公开案例。

## 是什么 / 背景

- Grok 4.5 于 2026-07-08 公开发布（Cursor 官方博客与 TechCrunch 报道日期；x.ai 页面元数据显示 2026-07-16，疑为后续更新时间）。官方定位：面向编码、agentic 任务与知识工作的旗舰模型，MoE 架构，定价 $2/M 输入、$6/M 输出 token。
- 发布方现名 **SpaceXAI**——xAI 已并入 SpaceX 上市后的公司家族（X 平台亦为其子公司）。Cursor 博客原文："Today we are releasing Grok 4.5 together with SpaceXAI"。原 passage 中"xAI Grok 4.5"的说法对应旧品牌，事实主体无误。
- 公司关系背景：2026-06-16，SpaceX 宣布以 **600 亿美元全股票**收购 Cursor 母公司 Anysphere（CNBC、Reuters 报道），预计 2026 年 Q3 完成交割。Grok 4.5 是收购后两家"同门"协作训练的首个旗舰模型。
- Cursor 自有的上一代模型 Composer 2.5 定位编程专家模型，将继续保留；Grok 4.5 训练数据配比刻意更广（STEM 任务、研究论文等知识工作语料）。

## 机制：用户数据怎么流动

信号来源 → 处理/提炼 → 更新什么 → 验证与上线闭环：

1. **信号来源**：Cursor 编辑器内用户与代码库、软件工具的交互会话（agent 会话、开发者-代理交互）。Cursor 官方表述："Training included trillions of tokens of Cursor data which capture a wide-range of user interactions with codebases and software tools. This dataset lets the model learn both from existing software as well as developer-agent interactions."（https://cursor.com/blog/grok-4-5）
2. **处理/提炼**：x.ai 侧声明在原始 token 量之外重点投入数据过滤与筛选：去重、质量打分、领域定向选择；RL 训练覆盖数十万任务，使用自动 + 模型打分。Cursor 侧用分布式 agent 系统批量构造 RL 环境（"用上一个模型加速下一个模型"）。（https://x.ai/news/grok-4-5；https://cursor.com/blog/grok-4-5）
3. **更新什么**：前沿模型权重本身（联合预训练 + RL），不是外挂记忆或提示词层。训练规模：数万张 NVIDIA GB300 GPU。
4. **验证与上线**：官方基准（Terminal Bench 2.1、SWE Bench Pro 等）+ Cursor 内部 CursorBench；发布即内置于 Cursor 全套餐（首周用量翻倍），同时经 SpaceXAI API/Grok Build 提供。值得注意的治理细节：Cursor 在博客脚注承认 CursorBench 上 Grok 4.5 有优势，原因是"an earlier snapshot of the Cursor codebase was accidentally included in training"，该数据已从未来模型中移除。
5. **隐私边界（官方未明说部分）**：Privacy Mode 会话是否进入语料、进入语料的用户群/时间窗/同意状态，两家均未披露（apidog 分析指出这一点，属实）。

## 关键事实与数字（附出处）

- 官方确认"与 Cursor 联合训练"：x.ai "It's our strongest model ever and was trained alongside Cursor."（https://x.ai/news/grok-4-5）；Cursor "a mixture-of-experts model that we trained jointly with SpaceXAI"（https://cursor.com/blog/grok-4-5）
- 数万亿 token Cursor 数据：Cursor 官方 "Training included trillions of tokens of Cursor data"（https://cursor.com/blog/grok-4-5）
- SpaceX 600 亿美元全股票收购 Anysphere，2026-06-16 宣布，预计 Q3 2026 交割（https://www.cnbc.com/2026/06/16/spacex-spcx-cursor-acquisition-ipo.html ；https://www.reuters.com/legal/transactional/spacex-buy-anysphere-60-billion-2026-06-16/）
- Terminal Bench 2.1：Grok 4.5 得分 83.3%；同表 Fable (max) 84.3%、GPT 5.5 (xhigh) 83.4%、Opus 4.8 (max) 78.9%（https://x.ai/news/grok-4-5）
- token 效率：SWE Bench Pro 任务平均输出 15,954 token，比 Opus 4.8 (max) 的 67,020 少约 4.2 倍（https://x.ai/news/grok-4-5）
- SWE Bench Pro resolve rate 64.7%（同表 Fable (max) 80.4%、Opus 4.8 (max) 69.2%）；SWE Marathon pass@1 29.0%（https://x.ai/news/grok-4-5）
- 发布前曾在 SpaceX 与 Tesla 内部私测，公开上市 11 天前进入 beta（apidog 二手说法：https://apidog.com/blog/grok-4-5-cursor-training-data/）
- Musk 定位："It is an Opus-class model, but faster, more token-efficient and lower cost"（TechCrunch 引述：https://techcrunch.com/2026/07/08/spacexai-releases-grok-4-5-which-elon-describes-as-an-opus-class-model/）

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 官方明确说 Grok 4.5 "与 Cursor 联合训练"，Cursor 贡献数万亿 token 开发者交互数据 | x.ai："was trained alongside Cursor"；Cursor："trained jointly with SpaceXAI…trillions of tokens of Cursor data…user interactions with codebases and software tools"（https://x.ai/news/grok-4-5 ；https://cursor.com/blog/grok-4-5） | ✅ 证实 |
| 交互数据具体为"调试轨迹、多文件 diff、用户对 Agent 输出的修正" | 官方仅表述为"user interactions with codebases and software tools / developer-agent interactions"。"debugging traces, multi-file diffs, user corrections"只见于二手分析 apidog，其自称引自 TechCrunch，但检索到的 TechCrunch 原文并无此表述（https://apidog.com/blog/grok-4-5-cursor-training-data/ ；https://techcrunch.com/2026/07/08/spacexai-releases-grok-4-5-which-elon-describes-as-an-opus-class-model/） | ⚠️ 部分证实——方向对（过程性交互数据），细节出处仅为低质量二手转述且引证不实，官方未列明数据构成 |
| SpaceX 于 2026 年 6 月以约 600 亿美元收购 Cursor（Anysphere） | 2026-06-16 宣布，600 亿美元全股票（非"约"，精确 600 亿），预计 Q3 2026 交割（https://www.cnbc.com/2026/06/16/spacex-spcx-cursor-acquisition-ipo.html ；https://www.reuters.com/legal/transactional/spacex-buy-anysphere-60-billion-2026-06-16/） | ✅ 证实 |
| Terminal Bench 2.1 得分 83.3% | 官方基准表 83.3%；注意并非全场最高（Fable (max) 84.3%、GPT 5.5 (xhigh) 83.4% 略高），但高于 Opus 4.8 (max) 78.9%（https://x.ai/news/grok-4-5） | ✅ 证实（得分属实；若原文暗示"领先"需注意排名第三） |
| SWE Bench Pro 任务平均约 1.6 万 token 完成（约为对手 1/4） | 官方：平均 15,954 输出 token/任务，约为 Opus 4.8 (max)（67,020）的 1/4.2（https://x.ai/news/grok-4-5） | ✅ 证实 |
| "第一个直接用商业编辑器用户会话训练的前沿模型" | apidog 编辑论断："the first frontier model openly trained on a commercial editor's user sessions"（https://apidog.com/blog/grok-4-5-cursor-training-data/）。官方无此宣称；Cursor 官方说的是"the first we've built for more than software engineering"（我方首个超出软件工程领域的模型），含义不同 | ⚠️ 未能从一手来源证实——作为二手分析性判断可引用，但不应写成官方定论 |

## 本地文件与延伸阅读

- 本方法无本地 PDF（官方发布页为网页）。
- 延伸：x.ai 基准全表（DeepSWE 1.0/1.1、SWE Marathon）；Cursor 博客脚注的 CursorBench 数据污染披露；apidog 对 Privacy Mode 边界的追问（两家均未说明 Grok 4.5 语料是否尊重 Privacy Mode 边界）；SpaceX IPO 与收购时间线（Reuters/CNBC）。
