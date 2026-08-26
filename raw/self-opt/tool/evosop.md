---
title: "EvoSOP — 从原子动作到标准作业程序（SOP）的迭代工具优化"
layer: tool
source_urls:
  - https://arxiv.org/abs/2607.07321
  - https://arxiv.org/html/2607.07321v1
  - https://arxiv.org/pdf/2607.07321
accessed: 2026-08-26
local_pdf: raw/self-opt/tool/evosop-2607.07321.pdf
---

# EvoSOP

> 一句话：让 LLM agent 从自己的执行轨迹（execution trajectories）里把反复出现的原子动作序列提炼成可调用的"标准作业程序"（SOP，作为高阶工具），再通过 构建→合并→评估→修剪 的生命周期迭代优化整个工具集——不更新模型权重，只进化工具。

## 是什么 / 背景

- **论文全名**：*From Atomic Actions to Standard Operating Procedures: Iterative Tool Optimization for Self-Evolving LLM Agents*（从原子动作到标准作业程序：面向自进化 LLM Agent 的迭代工具优化），arXiv:2607.07321，2026-07-08 提交，cs.AI / cs.CL / cs.MA，许可 CC BY-NC-ND 4.0。https://arxiv.org/abs/2607.07321
- **作者**：Haipeng Ding, Yuexiang Xie, Zhewei Wei, Yaliang Li, Bolin Ding——单位为中国人民大学（Renmin University of China）+ 阿里巴巴集团（Alibaba Group）。https://arxiv.org/abs/2607.07321
- **动机**：现有 agent 框架依赖由细粒度原子动作（如基础文件 I/O、单轮搜索）组成的**静态工具集**，agent 每次遇到重复性工作流都要重新发明底层逻辑，推理想法开销大、失败率高。论文提出：把原子动作合成为可复用 SOP（封装多步逻辑的可调用高阶工具），即可实现 agent 的自进化（self-evolution）。https://arxiv.org/html/2607.07321v1
- 类比人类：人不会每件事都从零推理，而是靠 SOP 把多步逻辑打包成高级例程。

## 机制：怎么从轨迹/经验进化工具

EvoSOP 的工具优化生命周期（lifecycle）由四个模块驱动（https://arxiv.org/html/2607.07321v1 ）：

1. **Constructor（构建器）**：在执行轨迹 ξ 中识别反复共同出现的原子动作逻辑段，合成为可调用 SOP——附带可执行代码（executable code）与语义 schema（semantic schemas）。
2. **Merger（合并器）**：检查新生成 SOP 的功能冗余，把重叠例程合并成更泛化的高阶工具，防止上下文 C 膨胀、维持精简工具集。
3. **Evaluator（评估器）**：给 agent 装上更新后的工具集 F′ 重新执行任务，产生反映演化后 SOP 实际效用与可靠性的新轨迹。
4. **Reviewer（审查器）**：作为批评者分析合成工具的性能指标，修剪（prune）错误率高或与现有工具显著冗余的 SOP。

配套设计：

- **"符号反传"（symbolic backpropagation）**：作者把整个过程类比为训练——任务执行是前向传播，轨迹是可观测行为输出；改进不靠梯度更新权重，而是"反向"分析轨迹中的推理低效与执行失败，抽取并合成可复用 SOP。
- **合并与修剪 = 正则化**：防止对狭窄任务上下文"过拟合"；随着迭代进行，工具集趋于稳定，类似学习率衰减，最终收敛到优化的工具层级。
- **Checkpointing（检查点）**：为避免随机性导致误删有益 SOP，EvoSOP 不做严格线性更新——每轮迭代结束时**归档完整评估日志与当前 SOP 工具集**，最终选择训练成功率最高的那个迭代作为输出工具集 S*（无需单独验证集，任务反馈直接来自环境、自监督）。
- **SOP 生成约束**：Prompt 规定每条归纳出的 SOP 含**不超过 5 个 `tool_call` 动作**，保证泛化与可迁移。

## 关键事实与数字（附出处）

| 事实 | 数字/说法 | 出处 |
|---|---|---|
| 工具集规模与成功率 | "EvoSOP maintains the toolset on a compact scale (**typically fewer than 10 SOPs**) while achieving a task success rate of **approximately 80%**"（附录 C Toolset Maintenance and Convergence） | https://arxiv.org/html/2607.07321v1 |
| 工具集演化曲线 | Epoch 1–5 快速扩张，随后增长趋平，进入动态均衡——只有带来显著效用增益的合并后 SOP 才会被加入（Figure 5） | 同上 |
| 基准 | ACEBench（agent 子集）+ Tau2Bench（Telecom 子集 solo 模式） | 同上 |
| 提升 | 在 ACEBench 两个子集上相对 base 方法提升 **2.5%–13.4%**（视 backbone 而定）；同时显著减少交互轮数（interaction rounds） | 同上 |
| Backbone | 执行/评估用 GPT-4o；核心算法跨模型测试 GPT-4o、Gemini-3-Flash-Preview、Qwen-Max；方法 parameter-free，仅 CPU 上的 API 推理成本 | 同上 |
| 基线 | ReAct、DFSDT（原始原子动作）；ASI（一次性诱导 SOP/skill）；DRAFT（改写工具描述） | 同上 |
| 消融 | **Reviewer 的迭代审查与修剪对最终性能影响最大**——关闭修剪后两个数据集成功率显著下降 | 同上 |
| 瞬态 SOP 例 | `manage_and_send_message` 在 Epoch 1 合成后因关键失败率被 Reviewer 立即驱逐；`retrieve_flight_details` 因边际效用低、调用少而被修剪 | 同上 |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| EvoSOP 是一篇论文 | arXiv:2607.07321，*From Atomic Actions to Standard Operating Procedures: Iterative Tool Optimization for Self-Evolving LLM Agents*，人大+阿里，2026-07-08 | ✅ |
| 从真实执行轨迹里提取标准作业程序（SOP） | "empowers agents to extract SOPs from execution trajectories"（摘要） | ✅ |
| 构建→合并→评估→修剪 | "a systematic lifecycle of construction, merging, evaluation, and pruning"（摘要，对应 Constructor/Merger/Evaluator/Reviewer 四模块） | ✅ |
| 形成版本化工具集 | 论文无 "versioned" 措辞；实质机制是 **Checkpointing**：每轮迭代归档评估日志与当前 SOP 工具集、回选最优迭代（§Checkpointing）。功能上接近版本管理，用词有出入 | ⚠️ |
| 从执行轨迹里把原子动作合成为 SOP 工具集 | 标题即 *From Atomic Actions to SOPs*；SOP 是封装多步逻辑的可调用高阶工具 | ✅ |
| 工具集保持在 10 个以内仍有约 80% 成功率 | 附录 C 原文："typically fewer than 10 SOPs … task success rate of approximately 80%" | ✅ |

## 本地文件与延伸阅读

- 本地 PDF：`raw/self-opt/tool/evosop-2607.07321.pdf`（458 KB，%PDF-1.7 已验证）
- arXiv 摘要页：https://arxiv.org/abs/2607.07321 ；HTML 全文：https://arxiv.org/html/2607.07321v1
- 相关：UCT（同为"从经验造工具"，见 `raw/self-opt/tool/uct-tool-creator.md`）；综述 *Dynamic Agent Skills*（见 `raw/self-opt/refs-survey-dynamic-agent-skills.md`，注意：该综述 v1 未引用 EvoSOP，其语料截止 2026-05-31，早于本文 2026-07-08 提交）
- 论文中提到的相邻工作：ASI（一次性 SOP 诱导）、DRAFT（工具描述改写）、Voyager、ToolACE-DEV、Alita、DeepAgent
