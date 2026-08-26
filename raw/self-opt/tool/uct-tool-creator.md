---
title: "UCT — 从工具使用者到工具创造者的免训练经验复用"
layer: tool
source_urls:
  - https://arxiv.org/abs/2602.01983
  - https://arxiv.org/html/2602.01983v1
  - https://arxiv.org/pdf/2602.01983
accessed: 2026-08-26
local_pdf: raw/self-opt/tool/uct-2602.01983.pdf
---

# UCT

> 一句话：UCT（tool **U**ser to **C**reator via **T**raining-free experience reuse）是一个免训练（training-free）框架，让多模态推理 agent 在**推理过程中**把解题时积累的推理经验蒸馏成可复用工具——agent 发现没有趁手工具时自己写一个、测一个、入库一个，再由离线记忆整理模块合并去冗余，实现不加训练的自我进化。

## 是什么 / 背景

- **论文全名**：*Evolving from Tool User to Creator via Training-Free Experience Reuse in Multimodal Reasoning*（通过免训练经验复现实现从工具使用者到创造者的进化：多模态推理），arXiv:2602.01983，2026-02-02 提交（v1）。https://arxiv.org/abs/2602.01983
- **作者**：Xintian Shen、Jiawei Chen、Lihao Zheng（三人共同一作）、Hao Ma、Tao Wei（通讯）、Kun Zhan。致谢中感谢 "LiAuto Base Model" 对 "MindWatcher Team" 的支持——即**理想汽车**基础模型团队背景。https://arxiv.org/html/2602.01983v1
- **针对问题**（Tool-Integrated Reasoning, TIR 场景）：
  1. 固定工具集无法覆盖开放问题的需求；
  2. 缺乏自优化机制时，错误的工具输出会误导 LLM；
  3. 现有工具靠人工构建，成本高、适用面窄；
  4. 已有工具创造方法（CREATOR、CRAFT、LIVE-SWE-AGENT 等）生成的工具**一次性**（ephemeral）、用完即弃，无法沉淀为经验资产。
- **核心主张**：LLM 的推理轨迹（reasoning traces）内含隐式解题能力——把有效经验封装成持久化工具（harvest reasoning experiences and distill them into reusable assets），agent 即可从"用工具的"变成"造工具的"。

## 机制：怎么从轨迹/经验进化工具

三模块架构（https://arxiv.org/html/2602.01983v1 ，§3 + Figure 2）：

1. **Online Task Loop（在线任务循环）**：基于 ReAct 范式解题。动作空间 A = 思考 ∪ 工具调用（核心工具 T_core ∪ 自建工具 T_cre）∪ **工具创建请求**。需要工具时先在核心库与自建库里检索；检索失败则发出"创建票"（build ticket，含任务上下文摘要与子问题需求）。
2. **Online Build Loop（在线构建循环）**：收到创建票后进入与主任务**完全隔离**的构建工作流——主模型一次生成工具代码 + 配套测试脚本；**沙箱**（sandbox）执行测试，运行结果连同代码交给专门的代码评审模型（critic model）打分并给修改建议；测试与评审**双重验证**通过才注册为结构化 Tool Package（含工具代码、调用说明、环境依赖、测试结果），随后回到任务循环使用新工具。未通过则带着反馈迭代再生成：C^(k) = Ψ_build(C^(k−1), R_critic, R_sandbox | c_ticket)。
3. **Offline Memory Consolidation（离线记忆整理）**：库的演化被放到独立的离线阶段（避免在线去重/冲突消解的延迟与不稳定），依据使用日志 L 做两类操作——**Organize**（同类工具归类合并、去重）与 **Analysis & Discard**（淘汰低频使用或高失败率工具），保证库长期可扩展、检索高效：M_{t+1} = Φ_offline(M_t ∪ T_gen | L)。

一句话概括分工：**在线边用边造、离线慢速整理**——两个时间尺度（two timescales）分离。

## 关键事实与数字（附出处）

| 事实 | 数字/说法 | 出处 |
|---|---|---|
| 免训练声明 | "a novel training-free framework that transforms agents from tool users to tool creators"，"adaptive tool creation and self-updating during the inference process" | 摘要，https://arxiv.org/abs/2602.01983 |
| 性能提升 | 相对 Basic-CoT：基于 Qwen3-VL-235B-thinking **+20.86%↑**、基于 Gemini2.5-pro **+23.04%↑**（TRBench 上），并在各指标上达到 SOTA | §5.2，https://arxiv.org/html/2602.01983v1 |
| 自建基准 TRBench | **959** 个实例，3 大域（数学/科学/通用 VQA）、11 个子类；用 Min-Max 迭代采样保证多样性 | §4 |
| 生成的工具库规模 | **7 大类、64 子类、207 个具体计算工具**（含代数计算、几何操作、统计分析等） | Figure 4 |
| 工具复用率 | **93.1%** 的工具在测试集中至少被使用一次（低冗余的证据） | §5.3 Table 3 |
| 实现配置 | 底座 Qwen3-VL-235B-Thinking（temperature=1），8 张 NVIDIA H20 GPU；评测 judge 用 Qwen3-VL-235B-Instruct，浮点容差 10^-6 | §5.1 |
| 基线 | Basic-CoT、vanilla 代码解释器（无验证、无记忆）、CREATOR、CRAFT（统一升级到 Qwen3-VL-235B-thinking 底座公平比较） | §5.2 |
| 演化曲线 | 随推理查询量增加，工具库经记忆整理持续精化、性能持续上升；因数据集内题型有限，最终趋于平台期 | §5.3 Figure 6 |
| 对照工作定位 | CREATOR/CRAFT/LIVE-SWE-AGENT 的工具"one-off、不保留"；Voyager/ToolACE-DEV 面向具身/游戏场景 | §2.2 |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| UCT 是一篇论文 | arXiv:2602.01983，理想汽车团队，2026-02-02 | ✅ |
| training-free 的 tool creator | "training-free framework that transforms agents from tool users to tool creators" | ✅ |
| 推理时根据经验自动创建和更新工具 | "adaptive tool creation and self-updating during the inference process"；工具源自推理轨迹中沉淀的经验 | ✅ |
| （补充语境） | 论文场景是**多模态数学/科学/VQA 推理**（TIR），不是通用 agent 工作流；"更新"分两层——在线创建 + 离线记忆整理（合并/淘汰），并非全部发生在推理时刻 | ⚠️ 语境补充 |

**撞名提示**：检索 "UCT" 会大量命中 MCTS 里的 UCT（Upper Confidence bounds applied to Trees，如 arXiv:2503.04412、2502.12412），与本主题无关；确认本文以 arXiv 号 2602.01983 为准。

## 本地文件与延伸阅读

- 本地 PDF：`raw/self-opt/tool/uct-2602.01983.pdf`（200 KB，%PDF-1.7 已验证）
- arXiv 摘要页：https://arxiv.org/abs/2602.01983 ；HTML 全文：https://arxiv.org/html/2602.01983v1
- alphaXiv：https://www.alphaxiv.org/abs/2602.01983 ；Hugging Face Papers：https://huggingface.co/papers/2602.01983
- 相关：EvoSOP（见 `raw/self-opt/tool/evosop.md`，同为"经验→工具"路线，但 EvoSOP 走离线批量生命周期、UCT 走在线按需创建）；CRAFT（arXiv:2309.17428）、CREATOR（arXiv:2305.14318）、Voyager（arXiv:2305.16291）为论文自列的最近邻
