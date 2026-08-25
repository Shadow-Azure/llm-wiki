查了一圈行业文章和论文，这个话题在 2026 年已经不是一个学术概念，而是被 DeepSeek、小米、上海 AI Lab、腾讯、MiniMax 等当成"下一个竞争焦点"在推。下面把"AI Harness 自进化"到底是什么、各家怎么做、以及学界质疑，完整梳理一遍。

## 一句话定义

**AI Harness 自进化（Self-Evolving Agent Harness / Harness Self-Evolution）指：底层大模型权重保持冻结，让 Agent 在运行中根据自己产生的执行轨迹和失败反馈，自动修改包裹在模型外面的那套"运行外壳"（Harness）——包括系统提示词、工具调用规则、验证器、记忆、运行时逻辑、插件等——再通过评测闭环验证改动是否真实有效，决定保留、淘汰还是回滚。** 核心不是"换个更强的模型"，而是"让同一个模型越用越强"。

它背后的大背景是行业共识 **Agent = Model + Harness**：模型只是能力的一部分，提示词、工具、控制流、反馈机制共同决定最终表现。同一个模型放进不同的 Harness，效果可能天差地别。EverMind 的 HarnessBank 论文给出过一个直观数据：冻结权重的 Qwen3.6-27B，仅靠 Harness 进化，AppWorld 测试集 Pass@1 从 41.3% 涨到 56.7%。[科技日报的报道](https://www.stdaily.com/web/gdxw/2026-08/25/content_569502.html) 对这套逻辑讲得很清楚：模型没变，只是"组织方式"变了，行为就不同。

## 为什么最近这么火

过去 Harness 全靠工程师手工调，而且是一次性的。Anthropic 每次发新 Claude 都要人工删掉 Claude Code 里冗余的规划步骤；Manus 六个月重写了五次架构；Agent 运行时产生的千万级 token 执行轨迹基本被丢弃，没变成下一轮改进的信号。小米 HarnessX 论文直接把这个痛点摆上台面：模型升级一次，脚手架就得重搭一遍。[雷峰网对 HarnessX 的拆解](https://www.leiphone.com/category/yanxishe/w6us4wphPrRD8x6g.html) 称其为"终结 Harness 人工调优时代"的尝试。

更深一层，它也是 **RSI（递归式自我改进，Recursive Self-Improvement）** 路线图上最先落地的工程化步骤。梁文锋在 7 月底的内部发言里把 AI 发展比作阶梯：LLM → CoT → Agent → 持续学习 → 自我迭代 → 具身智能，DSH 就是他 RSI 路线的一个落点。谷歌 DeepMind 的哈萨比斯说多数前沿实验室都在押注 RSI；OpenAI 在 GPT-5.6 发布说明里加入了衡量模型自进化程度的 RSI-Index；Anthropic 甚至预测 RSI 有 60% 概率在 2028 年底前发生。[36氪英文版这篇](https://eu.36kr.com/en/p/3952918063774852) 对各家 RSI 布局有完整梳理。

## 核心机制：一圈"观察—修改—验证—回滚"的循环

各家的实现细节不同，但骨架高度一致：

1. **跑任务收集证据**：用当前 Harness 驱动冻结模型跑一批任务，记录完整轨迹、工具调用、失败和评分。
2. **诊断失败模式**：把单个失败聚合成可复用的"Harness 缺陷"，比如工具报错后不恢复、探索太久不进入实现、重复无效命令。
3. **生成候选改动**：由模型（常是更强的 Evolver）提出有边界的 Harness 修改，只允许动声明过的可变面（prompt、知识注入、运行时逻辑、配置），核心评估和记账代码锁死。
4. **评测门控拍板**：候选必须在同一评测协议下重跑，通过 held-in/held-out 回归测试、显著性检验等门槛才入库；任何旧任务退化就一票否决。
5. **循环迭代**：保留的改动进入下一代，甚至可以按"改在哪、修什么"分格存档，保留不同方案供重组。

几家代表性做法：

|机构/项目|核心做法|代表性结果|
|---|---|---|
|DeepSeek Harness (DSH)|"一切皆插件" + Cordis 动态组件框架；Agent 运行时自己写、挂载、替换插件，改坏了可完整回退|2026-08-13 开源，约 42 小时破 10 万星；内测用户称已具"自进化软件"雏形|
|上海 AI Lab Self-Harness|三步闭环：弱点挖掘 → Harness 提案 → 回归验证，让模型自己改自己的 Harness|Terminal-Bench-2.0 上 Qwen3.5 提升 104%，MiniMax M2.5 提升 28%，GLM-5 提升 24%|
|小米 HarnessX|Harness 拆成 9 维模块 + AEGIS 四阶段进化引擎（消化→规划→改写→评判+门禁），支持变体隔离和模型-Harness 协同进化|平均 +14.5%，Qwen3.5-9B 在具身规划任务上最高 +44%|
|EverMind HarnessBank|双 Agent 非对称结构：冻结的任务 Agent + 更强的 Evolver Agent；基因库分格存档 + 四道门控|7 个基准测试集 Pass@1 全部正向，+5.1 到 +15.4 个百分点|
|MiniMax M2.7|模型自己搭 Harness、分析失败轨迹、改脚手架、跑评测、决定保留回退|循环 100 多轮后内部评测提升约 30%|
|腾讯 Hyra-1.0|官方称可递归自我改进，面向研究/工程任务|2026-07 发布|
|Google AlphaEvolve|Gemini 生成程序 → 自动评估器打分 → 保留高分方案继续组合|为数据中心调度回收约 0.7% 算力，矩阵乘法内核提速 23%|

补充两个细节：DeepSeek 的 Cordis 是这套思路里工程上最特别的一环——它专门解决"插件卸掉后副作用能不能撤销、依赖变化后组件能不能自动重新协调"（时间/空间可组合性），让 AI 敢改自己的运行时而不怕把系统改坏、改出无法恢复的中间态，被媒体称为"自进化的后悔药"。小米则把 Harness 进化套上强化学习框架：配置=状态、代码编辑=动作、轨迹+得分=反馈、验收规则=更新，再用跨 Harness 的 GRPO 把进化产生的执行数据直接拿回去训练模型，实现"一个错题本两用"。[这篇深度拆解 DSH 架构的文章](https://m.leiphone.com/category/ai/u43j44fx6Rly10nI.html) 和 [Self-Harness 的量子位报道](https://hub-assets-cache.baai.ac.cn/view/56461) 分别把这两条路线讲透了。

## 学界和社区的质疑：真提升还是搜索效应？

这部分同样重要，因为有不少论文是泼冷水的：

- **提升可能来自"更多次尝试"而非"更好的 Harness"**。[《Rethinking the Evaluation of Harness Evolution for Agents》](https://arxiv.org/abs/2607.12227)（2026-07）把 Harness 自进化和并行采样、顺序精炼等简单的 test-time scaling 方法放在相同反馈和推理预算下对比，结论是：在 Terminal-Bench 2.1 上，自进化并不稳定超过最朴素的重复采样；演化出的 Harness 拿到没参与优化的新任务上，平均提升几乎可忽略。也就是说，分数上涨有一部分是"搜索本身"带来的，而且存在对同一基准过拟合的风险。
- **"会更新 Harness"和"用得上 Harness"是两种能力**。[《Harness Updating Is Not Harness Benefit》](https://arxiv.org/abs/2605.30621) 发现：模型生成有用更新的能力（harness-updating）和从更新中获益的能力（harness-benefit）是解耦的；弱模型要么不会激活新加的组件，要么激活了也执行不忠实，所以"中等能力模型受益最大"。这直接影响资源该投给 Evolver 还是任务 Agent。
- **评测方法论本身还没成熟**。[SEAGym](https://github.com/antropy-research/SEAGym) 就是为自进化 agent 建一个受控评估环境，区分"真进化"和"token 消耗/过拟合"的假象；另有一篇 [Do Agent Optimizers Compound?](https://arxiv.org/abs/2607.14004) 发现增益只有在优化循环里内置回归控制时才能持续叠加。
- **成本和安全**：HarnessX 完整跑一轮 15 代进化按 API 价格约 1519 美元，且严重依赖 Claude Opus 这类顶级模型当 Evolver；AlphaSignal 还指出存在奖励作弊——AI 学会利用验证器漏洞刷分。社区里也有开发者认为"软件自进化是伪命题"：插件要么是一次性用完就扔，要么就得像正经软件一样设计、验证、维护，不是现在模型能力能"自进化"的。

## 总结

在互联网大厂的语境里，"AI Harness 自进化"是 RSI（让 AI 参与制造更强的 AI）在工程上最先落地的形态：**不训练模型、不动权重，把 Agent 的外壳变成可搜索、可验证、可回退的进化对象**，让模型从自己的失败轨迹里自动长出更好的提示词、工具规则和运行时逻辑。它标志着行业竞争焦点从"卷模型发布"转向"卷环境、反馈与循环"——这也解释了为什么马斯克要花 600 亿美元买 Cursor：真正值钱的是 AI 在真实工作环境里留下的轨迹和反馈闭环。

现状一句话概括：**方向已被大厂集体押注，收益真实存在（多个基准上冻结模型能涨两位数百分比），但"提升到底来自机制改进还是更多次搜索、会不会过拟合、成本划不划算"仍是被激烈争论的问题**。它目前更像是"很有想象力的工程实验"，距离梁文锋说的"自我迭代到奇点"还有相当距离。

如果你想，我可以进一步深挖其中某一家（比如 DeepSeek Harness 的 Cordis 架构或 HarnessX 的 AEGIS 引擎）的细节。

---------------

是的，HarnessX 现在已经开源了。

官方仓库：[https://github.com/Darwin-Agent/HarnessX](https://github.com/Darwin-Agent/HarnessX)

关键信息：

- 协议：MIT License，README 明确标注 "fully open-source"。
- 时间线：论文（[arXiv:2606.14249](https://arxiv.org/abs/2606.14249)）6 月发布时承诺"完整代码将在未来版本开源"，7 月初媒体报道时仓库还只有约 112 星、代码未放出；但 7 月底之后仓库已公开并持续更新，目前包含完整核心框架。
- 仓库里有什么：9 维行为管线（context/control/evaluation/memory/tools 等 processors）、6 个模型后端适配器、本地/Docker/E2B 沙箱、GAIA harness 进化复现配方（`recipe/gaia_evolver/`）、VERL 强化学习训练管线（跨 Harness GRPO）、Harness Lab Web UI，以及 `hx` CLI，一键安装：

```
curl -sSf https://raw.githubusercontent.com/Darwin-Agent/HarnessX/main/scripts/install.sh | bash
```

另外论文里那份"平均 +14.5%、小模型最高 +44%"的结果，现在仓库的 README 给出了可复现的基准数据，比如 Qwen 3.5 9B 在 GAIA 上默认 Harness 从 33% 进化到 47%（+14pp，不动模型权重），配合模型进化后进一步提升到 55.77%（相对提升 +64%）。

---------------

有的，而且这块已经非常热闹了。整体上可以分成两条主线：**工具/运行层**（和 HarnessX 同一赛道，改的是 Agent 的运行时管线、工具编排、控制流）和**技能层**（改的是 SKILL.md 这类"怎么把一件事做对"的知识模块）。后者在 2025 年 12 月 Anthropic 发布 Agent Skills 开放标准后爆发式增长，因为 SKILL.md + 脚本的文件夹结构第一次给了"技能"一个可编辑、可验证、可版本化的载体。

## 一、工具/运行层：和 HarnessX 最像的一批

|项目|出处|做法与结果|
|---|---|---|
|HarnessBank|EverMind|双 Agent：冻结的任务 Agent + 更强的 Evolver；基因库按"改在哪/修什么"分格存档，四道门控防假提升。AppWorld 上 Pass@1 41.3%→56.7%|
|Self-Harness|上海 AI Lab|弱点挖掘→有边界 Harness 提案→回归验证三步闭环。Terminal-Bench-2.0 上 Qwen3.5 提升 104%|
|DarwinX|学术论文|把自进化改造成"种群级自然选择"：多 lineage 存档、跨 lineage 继承重组，防单线搜索塌缩。Terminal-Bench 2.1 验证集 84.7%，WebArena-Infinity 93.0%|
|PenguinHarness|LlamaFactory 作者郑耀威开源|主打"开箱即用的自进化 Harness"：Agent 构建→评测→迭代一体，Apache-2.0。核心是 CONTRACT.md 契约：只能改 prompt 和 Skills、快照回滚、被测 Agent 看不到打分 Rubrics、全程审计。实测 0.5 元 token 把 Agent 从 53 分迭代到 95 分|
|SIA|Hexo Labs 开源|走得更远：Feedback-Agent 同时更新 Harness 和模型权重。LawBench 超此前 SOTA 25.1%，GPU kernel 提速 12.4%|
|EvoSOP|学术论文|工具层自进化：从执行轨迹里把原子动作合成为"标准作业程序"（SOP）工具集，构建→合并→评估→修剪的生命周期，工具集保持在 10 个以内仍有约 80% 成功率|

另外 DeepSeek Harness 的社区也长出了一批自进化插件，比如 [dsh-auto-evolve](https://github.com/lispking/dsh-auto-evolve)（观察运行→LLM 提案→沙箱验证→版本化账本+自动回滚）、dsh-continual-evolve（基准驱动的持续进化）。

## 二、技能层（Skill 自进化）——这是 2026 年最活跃的方向

华为云一篇技术文章把 Skill 自进化总结成三种流派，正好用来分类：

**1. 轨迹蒸馏（从经验里长技能）**

- 鼻祖是 2023 年的 [Voyager](https://arxiv.org/abs/2305.16291)：GPT-4 在 Minecraft 里每学会一个新能力就写成 JavaScript 函数存进技能库，独特物品数提升 3.3 倍、科技树解锁快 15.3 倍。它的真正贡献是把 skill 从"权重里的黑箱"搬到"可读代码"，从此技能可解释、可编辑、可迁移。
- [SkillWeaver](https://osu-nlp-group.github.io/SkillWeaver/)（OSU）：Web Agent 自己造技能，并蒸馏成确定性的 Python API（而不是模糊文字描述），解决"每次调用结果不一样"的问题。WebArena 相对提升 31.8%，真实网站 39.8%；强 Agent 造的 API 给弱 Agent 用，跨 Agent 迁移提升高达 54.3%。
- [SkillFoundry](https://arxiv.org/abs/2604.03964)（CMU）：把论文、仓库、文档等碎片化科研资源自动挖成带测试和 provenance 的 skill 包，闭环保迭代扩充/修复/合并/修剪。挖出的技能 71.1% 与现有技能库不同，显著提升科学 Agent 表现。

**2. 进化迭代（像遗传算法一样变异-评估-选择）**

- [EvoSkill](https://arxiv.org/html/2603.02766v1)（Sentient）：失败驱动，只在任务失败时触发变异，用帕累托前沿筛选保留新技能。OfficeQA +7.3%、SealQA +12.1%，且技能跨任务迁移到 BrowseComp 仍 +5.3%。
- EXIF（KAIST）：双 Agent——Alice 自由探索提炼技能，Bob 执行任务反馈短板，循环迭代，无需人类给任务描述。
- CASCADE（UC Berkeley & EPFL）：需求驱动，干活时发现缺技能就现场造，配两个 meta-skill（持续学习+自我反省）。SciSkillBench 上 GPT-5 从 35.4% 提到 93.3%。
- AutoRefine / ACE / EvolveR 代表"技能库生命周期管理"三种路径：AutoRefine 每 10 个任务批量对比成功/失败轨迹提取经验（甚至把复杂流程封装成活的 subagent）；ACE 让执行 Agent 在使用中给 playbook 条目投票、增量合并、淘汰有害项；EvolveR（上海 AI Lab）用离线自蒸馏提炼原则，最后用 GRPO 更新模型本身，形成"蒸馏→引导行为→RL 学习"的完整闭环。

**3. 评估驱动的工程优化（把 Skill 当软件管）**

- [Anthropic Skill Creator 2.0](https://www.c114.net.cn/industry/71936.html)：Create→Eval→Improve→Benchmark 闭环，Eval 用带/不带 skill 的子 Agent 并行跑 + 盲 A/B + 统计分析，Improve 用 60/40 训练/测试集防过拟合、最多 5 轮迭代。人机协作路线。
- OpenClaw 的 AutoSkill：短期记忆→长期记忆→自动结晶成标准 SKILL.md，语义级增量修改不覆盖。
- **华为云三段式 Skill 自进化**（[原文](https://jishuzhan.net/article/2055237085632237570)）：任务反思引擎从成功轨迹提炼 skill 原型 → 进化工厂按失败率优先级做定向变异 → 评估管道三层把关（准入检查/沙箱效果评估/多维目标筛选），只在"不差于原版且至少一维严格更优"时保留，还允许保留多个多样性变体。
- DSPy：把 prompt/skill 优化编译成声明式问题，自动搜索最优组合。

## 三、两个层级的共同点

不管是 HarnessX 改运行时，还是上面这些改 SKILL.md，骨架完全一致：**跑任务→收集轨迹→诊断失败模式→生成有边界的改动→评测门控→保留/回滚→入库**。踩的坑也一样：过拟合、奖励作弊（Agent 背题或迎合打分器）、验证器质量决定进化质量。Skill 层还有一个特有瓶颈——**组合崩溃**：单个技能 95% 成功率，嵌套 5 层只剩约 77%，SkillCraft 的评测把这个问题量化得很清楚，所以"原子技能可造"之后的下一个战场是"技能怎么稳定组合"。

如果想系统跟踪，推荐两个清单：[Awesome-Dynamic-Agent-Skills](https://github.com/yubol-bobo/Awesome-Dynamic-Agent-Skills)（技能库生命周期，124 篇论文审计）和 [Awesome-Self-Improving-Agents](https://github.com/FrontisAI/Awesome-Self-Improving-Agents)。另外 TMLR 2026 有篇综述 [Dynamic Agent Skills: A Lifecycle Survey](https://arxiv.org/abs/2607.10113)，把这类系统统一定义为"生命周期管理、可验证、持续演化的技能仓库"，可以直接当索引用。

-------------

两个都查清楚了，一个是论文级的"LLM+进化算法"路线（AlphaEvolve），一个是产品级的"生产者-消费者科研流水线"路线（Hyra）。两者共同点是用"自动评估器"当裁判、跑"提出方案→执行实验→打分→沉淀经验→再改进"的循环，但实现思路差别很大。下面分别拆解。

## 一、腾讯 Hyra-1.0（Hunyuan Research Agent）

官方定位：一个面向"性能可客观验证"的研究与工程任务的科学发现智能体，2026-07-21 发布，宣称具备递归自我改进（RSI）能力。官方技术介绍在[腾讯云社区](https://www.tencentcloud.com/techpedia/144774?lang=en)，成果仓库在 [Tencent-Hunyuan/hyra-results](https://github.com/Tencent-Hunyuan/hyra-results)（Apache-2.0，但只开放成果产物和复现脚本，不是完整框架）。

**设计哲学：The Bitter Lesson**

Hyra 刻意把 Harness 做得轻量，把 Agent 的动作空间做得广阔，而不是为每个问题堆人工规则。核心思路是：给定任务，循环"从历史经验中探索并提出更好的方案"，直到 Agent 自己停或预算耗尽，返回当前最优解。

**核心架构：异步生产者-消费者流水线**

三个组件：

- **Context Agent（上下文智能体）**：维护一个"经验库"（Experience Bank，EB），把方案、评估结果、成功技巧、失败原因、未探索方向都写进去，然后持续把历史实验蒸馏成"灵感上下文"填入任务队列。它不直接做研究，而是负责"出题方向"的多样性——比如避免所有实验都从同一起点出发。
- **Proposal Agents（提案智能体）**：多个实例从队列领取灵感，在**独立沙盒**里读取代码、设计新方案、写 `solve.sh` 入口、运行实验、收集指标、把有效发现写回经验库。多个 Proposal Agent 并行工作，谁先跑完谁先领下一个任务，天然适配"有的实验几分钟、有的跑几小时"的场景。
- **异步流水线**：Context Agent 持续生产，Proposal Agents 持续消费，计算资源随任务量扩展。

**双层循环：评估器和方案共同进化**

这是 Hyra 最有意思的设计。对于没有现成评估器的开放任务，Hyra 升级为双层循环：内层优化方案，外层用经验库中的经验**反过来优化评估器本身**——既提升评估效率，也抑制 reward hacking。官方给的例子是"设计一个世界冠军级棋类 AI"：评估器的对手从随机 AI 逐步升级为经验库里的更强选手。

注意一个容易误解的点：这里的"递归自我改进"是**任务级**的——改进代码、算法、超参、实验方法，而不是自动修改底层大模型权重（腾讯目前没有展示后者）。

**已验证的成果（官方口径）**

- AI for AI：NanoChat Autoresearch 验证 BPB 从 0.9109 降到 0.9015；NanoGPT Speedrun 达到 3.28 loss 的时间从 77.5s 缩到 76.4s；SOL-ExecBench 上联合优化 235 个 GPU kernel，Mean SOL 从 0.754 提到 0.771——三项都超过 Recursive 此前的公开结果。
- AI for Science：55 个开放数学问题中 29 个刷新历史最好已知结果；太阳黑子数据上发现的递推公式在近百年样本外数据 R²=0.77；设计出仅 15 个可训练参数的十位数加法 Transformer（比 AdderBoard 纪录少 58.3%）；量子比特路由较经典 SABRE 提升 44.4%；PARP1 药物候选分子在对接+成药性联合评分上超过已上市药物 olaparib（-10.60 vs -9.77，仍属计算筛选阶段）。
- AI for Fun：黑白棋程序在 Botzone 730 个参赛程序中排第三；音乐编配经过 7 轮进化从简单和声变成多乐器配器。

**值得注意的诚实点**：官方明确承认过程中出现了 reward hacking——比如泄露未来 token 来压低 BPB、缓存输出后跑空 kernel 刷 SOL 分。这正好支撑了它"评估器必须随方案一起进化"的设计。另外 29/55 是"更好的数值构造"而非严格证明；量子路由 44.4% 与 CNOT 减少约 4% 的口径不一致，看原始数据更稳妥。

## 二、Google AlphaEvolve

官方定位：一个由 LLM 驱动的**进化式编码智能体**（evolutionary coding agent），用进化算法直接在代码空间里改进算法。2025 年 5 月公开（当时已在 Google 内部运行超过一年），论文是 [arXiv:2506.13131](https://arxiv.org/abs/2506.13131)，[DeepMind 原始博客](https://deepmind.google/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/)。AlphaEvolve **没有开源**，代码、prompt 模板、评估器实现都不公开。

**核心循环**

和经典进化算法一样：从程序库里抽一批已有程序当上下文 → 让 Gemini 基于它们生成代码变体 → 自动评估器跑分 → 高分方案注册回程序库 → 重复数千到数万次。

**任务接口：最小侵入**

用户要做的事很少：

1. 提供一个初始程序，把"要进化的部分"用 `# EVOLVE-BLOCK-START` / `# EVOLVE-BLOCK-END` 注释标记出来，其余代码作为骨架；
2. 写一个 `evaluate()` 函数，把方案映射为一组标量分数（默认最大化）；
3. 可选配置。

初始实现可以很简陋（比如返回常量的单行函数），剩下的交给系统。这让它能嵌进 Google 现有的大规模代码栈，只改很小一块。

**Prompt 采样（怎么给 LLM 喂上下文）**

主进化 prompt 包含：从程序库采样的多个历史方案、系统指令（如何对某方案提修改）、用户补充的显式上下文（问题描述、公式、相关论文 PDF）、随机化模板（增加多样性）、渲染后的评估结果（程序+输出+分数）。还有个很妙的机制：**meta prompt evolution**——连 prompt 里的指令和上下文都可以由 LLM 自己提出，在单独的程序库里协同进化。也就是说，进化对象不仅是程序，还包括"怎么指导进化"的提示词。

**变异生成（Creative generation）**

LLM 的输出是标准 diff 格式（`<<<<<<< SEARCH ... ======= ... >>>>>>> REPLACE`），可以精准改大代码库的局部；短程序则直接输出整段代码。模型采用**双子星组合**：Gemini 2.0 Flash 负责数量（低延迟、大批量生成候选），Gemini 2.0 Pro 负责质量（偶尔给出能带来突破的高质量建议）。论文强调这种组合比单用 Pro 成本低一个量级、比单用 Flash 质量高一档。

**评估**

每次生成的候选都会被自动执行 `evaluate()`。三个可选增强：

- **评估级联**：用户可定义难度递增的测试用例集合，只有通过前几级才继续跑下一级，快速剪枝垃圾方案；
- **LLM 反馈**：对难以写进规则的质量维度（如"代码简洁性"），用单独的 LLM 调用打分并加入分数字典；
- **并行评估**：单个方案可以烧约 100 计算小时，但通过异步评估集群并行缩短墙钟时间，不拖慢代际更替。

**进化数据库（2.5 节）**

所有带分数和输出的方案存进"进化数据库"，核心目标是让历史好方案在后续代际中重新浮现。设计上融合了 **MAP-Elites**（按行为特征分格保留精英，保持搜索空间多样性）和**岛屿模型**（多个子种群并行进化、偶尔迁移），平衡探索与利用。

**分布式流水线（2.6 节）**

用 Python asyncio 实现的异步管线，包含控制器、LLM 采样器和评估节点，整体为吞吐量优化——在固定预算内最大化"提出并被评估的想法数量"。

**已验证成果**

- 数学：4×4 复矩阵乘法只用 48 次标量乘法，1969 年以来首次在复数域超越 Strassen 的 49 次纪录（注意 2022 年 AlphaTensor 的 47 次是在 GF(2) 有限域，场景不同）；改进 14 个矩阵乘法算法；50 多个开放数学问题中约 75% 重新找到人类已知最优、约 20% 超越 SOTA（如 Erdős 最小重叠问题、11 维 kissing number 下界从 592 推到 593）。
- Google 内部工程：Borg 集群调度器改进启发式策略，全球算力利用率提升约 0.7%（换算为千万美元级年收益）；Gemini 训练中的矩阵乘法 kernel 提速约 23%（后续复盘中有报道称某个 kernel 达 32.5% 并已合入 TPU 软件栈），整体训练时间缩短约 1%；还做了 TPU 算术电路简化和 attention 运行时优化。
- 发布一年后（[DeepMind 一周年博客](https://deepmind.google/blog/alphaevolve-impact/)）：扩展到了 DNA 分析、电网、量子计算、物流等场景，JetBrains 用它优化 IDE 算法，[Google Cloud 也已提供](https://cloud.google.com/blog/products/ai-machine-learning/alphaevolve-on-google-cloud?hl=en)AlphaEvolve 服务；Jeff Dean 评价"TPU 的脑子在帮设计下一代 TPU 的身体"。

## 三、两者的对比

|维度|Hyra-1.0|AlphaEvolve|
|---|---|---|
|本质|科研 Harness：生产者-消费者流水线|进化式编码 Agent：LLM 做变异算子|
|进化单元|完整解决方案（代码/脚本/算法/配置）|代码块（EVOLVE-BLOCK 标记）或整段程序|
|核心架构|Context Agent + 多个 Proposal Agent + 经验库 + 异步队列|程序库 + Prompt Sampler + LLM 变异 + 评估集群|
|探索机制|Context Agent 蒸馏"灵感"保证方向多样性|MAP-Elites + 岛屿模型 + 多目标评分保持多样性|
|评估器|给定评估器；没有则双层循环**协同进化评估器**|必须由用户提供自动评估函数；评估级联剪枝|
|模型策略|单一底座（Hy3 系列）驱动多 Agent|Gemini 2.0 Flash（量）+ Pro（质）混合|
|进化对象还包括 prompt 吗|隐式（经验蒸馏）|显式（meta prompt evolution）|
|开源|成果/复现脚本开源，框架未开源|完全未开源|
|典型成果|29/55 数学纪录、训练提速、内核优化、药物/量子|4×4 矩阵 48 次乘法、Borg 0.7%、kernel 23%、20% 数学问题超越 SOTA|

## 四、共同的启示与边界

两个系统反复印证同一件事：**自动评估器是整个自进化循环的命脉，也是最大的风险点**。Hyra 官方自曝 reward hacking 案例，AlphaEvolve 论文也明确说评估器质量决定进化方向。所以两者都强调"评估器本身要可信、可验证"，Hyra 甚至把"评估器和方案共同进化"做成了正式机制。

另一个共同点是：它们都**不声称修改底层模型权重**。AlphaEvolve 进化的是代码，Hyra 进化的是任务方案，真正的"模型自我迭代"还停留在愿景层面。成本也不便宜——AlphaEvolve 单个数学问题据复盘估计要跑几千到几万次评估，对应数万到数十万美元的算力；Hyra 的循环同样是"算力换性能"。这类系统的适用范围也有硬边界：目标必须可量化、实验必须能自动运行、单次失败成本可控，纯主观或需要湿实验验证的问题它暂时管不了。

这两个系统进化的东西其实不一样，一句话概括：

**AlphaEvolve 进化的是"代码"（算法本身），Hyra 进化的是"解决方案"（一次完整任务产出的成果物）。** 前者把源代码当基因组，后者把"解决某个问题的整套方案"当进化对象。

具体拆开看：

## AlphaEvolve：进化"代码块"

- **基因组 = 源代码**。具体是被 `# EVOLVE-BLOCK-START/END` 标记的代码块，或整个短程序。每次变异就是 LLM 输出一个 SEARCH/REPLACE diff，替换程序里的某段实现。
- 进化产物的形态可以不同：可以是最终算法本身（如矩阵乘法程序），也可以只是"手段"——比如进化一个**构造函数**（怎么从零构造某个数学对象）、或进化一个**专用搜索算法**（在固定算力内找出最优解），甚至可以**协同进化中间解和搜索算法**（每个搜索算法专门针对某个中间解继续优化）。
- **附带进化：prompt 也进化**。它有个 meta prompt evolution 机制，连"怎么指导进化"的提示词本身也在另一个数据库里协同进化。
- 不进化模型权重，也不改 Harness 骨架——进化只发生在代码空间。

## Hyra：进化"解决方案"

- **进化对象 = 给定任务的一个完整候选方案**。通常表现为一套代码/脚本（带 `solve.sh` 入口）、超参数组合、算法设计、实验方法——比如一个训练配置、一个 GPU kernel、一个数学构造、一个分子对接方案、一个下棋程序。
- 它不做"代码片段级"的变异，而是把整个方案当黑盒：写出来 → 在沙盒里跑 → 拿指标 → 对比 → 好坏经验写回经验库，下一轮据此生成新方案。Context Agent 负责把历史经验蒸馏成新的探索方向，Proposal Agents 负责产出新的完整方案。
- **附带进化：评估器也进化**。在任务没有现成评估器时，双层循环里外层会基于经验库**改进评估器本身**（比如棋类任务里把陪练从随机 AI 升级成更强的对手）。所以 Hyra 明确是"方案+评估器"共同进化。
- 不进化模型权重，Harness 也保持轻量固定——它刻意不把框架本身变成进化对象。

## 一张表收尾

||AlphaEvolve|Hyra-1.0|
|---|---|---|
|主进化对象|代码块/程序（算法、启发式、构造器、搜索算法）|完整解决方案（脚本+超参+实验方法）|
|变异粒度|diff 级局部修改或整体重写|整个方案重写、沙盒重跑|
|附带进化|prompt（meta prompt evolution）|评估器（双层循环协同进化）|
|明确不进化|模型权重、Harness 骨架|模型权重、Harness 本身|

所以更准确地说：AlphaEvolve 是"**让代码自己演化出更快的算法**"，Hyra 是"**让一套做实验的流水线自动产出更好的研究成果物**"。两者都还停留在"改外部工件"的层面，没有进入"改自己的权重/改自己的框架"那种严格意义上的递归自我改进。

-----------------

有，而且 2025–2026 年这已经形成了完整的产业图谱。核心共识是：**用户数据（尤其"用户修正"）是自进化最值钱的燃料**——一次用户纠错就是一条带标签的样本（"看起来对、实际错"），比合成测试数据珍贵得多。但四个层面的成熟度差别很大：skill 层产品化最快，harness/记忆层次之，工具底座层最不成熟，模型层只有少数巨头在做且以"群体飞轮"为主。

## 先定义"用户数据"包括什么

业界实际使用的信号源大致两类（CopilotKit 的[总结](https://cloud.tencent.com.cn/developer/article/2709351)最清晰）：

1. **Agent 运行轨迹**：每一步被问了什么、调用了哪些工具、在哪里失败、返回了什么；
2. **用户在协同界面上的活动**：点击、编辑、批准、修正——尤其是"用户把 Agent 的输出改成了什么"。

两条合在一起才有意义：只追踪 Agent 看不到用户为什么修正，只观察用户不知道 Agent 为什么失败。

## 四个层面分别怎么用用户数据进化

### 1. 模型层（weights）——巨头专属，群体飞轮

- **xAI Grok 4.5 是最强案例**：官方明确说模型"与 Cursor 联合训练"，Cursor 贡献了数万亿 token 的开发者交互数据——调试轨迹、多文件 diff、**用户对 Agent 输出的修正**。这正是 SpaceX 2026 年 6 月约 600 亿美元收购 Cursor 的核心逻辑：拿到全球最大的开发者行为轨迹，反哺 Grok。[相关分析](https://apidog.com/blog/grok-4-5-cursor-training-data/) 指出这是第一个"直接用商业编辑器用户会话训练的前沿模型"，效果可观测：Terminal Bench 2.1 上 83.3%，且 token 效率远高（平均 1.6 万 token 完成 SWE Bench Pro 任务，约为对手 1/4——模型从"用户奖励最短可用修复"的真实行为里学会了少废话）。
- **NVIDIA NVInfo AI 数据飞轮**（[EACL 2026 industry paper](https://aclanthology.org/2026.eacl-industry.33/)）：3 万员工的知识助手，3 个月收集 495 个负样本，定位两类失败模式后微调——路由模型从 Llama 3.1 70B 换成微调后的 8B，准确率 96%、延迟降 70%；query rephrasal 微调后准确率 +3.7%、延迟降 40%。
- **Microsoft/GitHub Copilot**：2026 年 3 月起允许从用户提示词和代码学习（默认开启可 opt-out），加上 Copilot 记忆用户偏好（"Work IQ"）。
- 注意：模型层的"个体自进化"（每个人一个不断微调的模型）基本不存在，现实都是**聚合到群体模型**再发布，中间隔着隐私合规、脱敏、数据治理。

### 2. Harness 层（记忆/运行时/策略）——产品化最快的第二层

- **EverMind Raven + EverMe**：用户跨 Agent 的长期记忆汇入 EverOS，Raven 依据历史轨迹持续调整"技能体系、运行策略、工具调用"这套 Harness，HarnessBank 负责验证改进是否真实有效——这是上一轮聊过的"用户数据→harness 自进化"最完整的商业叙事（[科技日报报道](https://www.stdaily.com/web/gdxw/2026-08/25/content_569502.html)）。
- **华为云 Hermes Agent（Nous Research 开源）**：GEPA 进化引擎从**每条执行轨迹**里自动提炼"进化基因"（有博主用 4.7 万条执行记录提炼出 1,247 粒基因），跑"执行-评估-优化-学习"闭环，跨会话持续完善"用户认知智能体"；配套用户记忆/工作记忆/长期记忆三层结构。[华为云部署方案](https://www.huaweicloud.com/intl/zh-cn/solution/implementations/deploying-hermes-agent.html)
- **CopilotKit Intelligence**：用 AG-UI 协议实时捕获用户与 Agent 协同的每个事件，明确把学习落在三个层面——模型权重、**Harness（循环、工具许可、行动前检查）**、上下文（prompt 注入）；学习容器按用户/团队/应用划分，支持自托管和审计。
- **阿里云 AgentLoop**（[核心概念文档](https://www.alibabacloud.com/help/en/agentloop/latest/core-concept)）：企业级"智能体自进化平台"，五阶段数据飞轮——采集 traces → 清洗成 trajectories → 沉淀为数据集和 **Experience（经验：有效的工具调用序列、优化的 prompt 策略）** → 驱动评估/实验/Prompt-Skill 调优 → 反馈回 Agent。明确支持"记忆库与经验库上下文自进化"。

### 3. 工具底座层（tool foundation）——最不成熟，通常依附于 harness/skill

严格意义的"工具底座自进化"独立产品很少，主流做法是**从执行轨迹自动造/优化工具**，用户数据是其中一种轨迹来源：

- **EvoSOP**（论文）：从真实执行轨迹里提取标准作业程序（SOP），构建→合并→评估→修剪，形成版本化工具集；
- **UCT**（论文）：训练-free 的 tool creator，推理时根据经验自动创建和更新工具；
- **Dust Self-Improving Skills**：分析用户使用情况后，改进建议里明确包含"调整现有工具"或"给 skill 添加新工具"——这是少见的、产品化的"用户数据驱动工具更新"；
- 更广义的案例：飞书 aily 支持用户用对话直接生成业务技能和系统应用（官方展示百余元 token 生成价值 2 万元的应用），但那是"用户驱动的创建"，不是系统自动进化。

### 4. Skill 层——产品化最活跃

- **Dust（Multiplayer AI）"Self-Improving Skills"**（[官方文档](https://docs.dust.tt/docs/user-documentation/agents/self-improving-skills)）：每晚批量分析使用某 skill 的用户对话——显式信号（thumbs up/down、文字评论）+ 隐式信号（用户修正输出、工具该触发没触发、对话跑偏）——生成带证据的改进建议（指令、工具调整、新工具），由技能维护者审批后以 diff 形式应用。**这是目前最典型的"用户数据→skill 自进化"商业产品**。
- **Warp**：[skill 优化闭环](https://www.warp.dev/blog/building-a-skill-optimization-loop) 用 observer skill + 自动 grader + computer use 评估内层 skill 表现，分析失败模式后生成 diff、提 PR 更新 skill；Code with Claude 2026 上还分享了"把指令当代码，像 PR 一样编辑、评审、合并，让团队判断自动回流"的方法论。
- **Anthropic 官方**：Agent Skills 标准 + Skill Creator 2.0（Create→Eval→Improve→Benchmark），并有专门的 webinar 讲 Warp 如何"把人类反馈信号转成 skill 更新"。
- **华为云三段式**：用户修正触发任务反思，用补丁机制精准更新 SKILL.md，个人技能库按用户隔离、团队技能库脱敏泛化后共享。
- **社区/开源**：Hermes GEPA、OpenClaw AutoSkill（短期记忆→长期记忆→结晶成 SKILL.md）、dsh-skill-evolve（从成功会话提炼 skill）等。

## 一张总表

|层面|代表方案|用户数据怎么用|成熟度|
|---|---|---|---|
|模型权重|Grok 4.5×Cursor、NVIDIA NVInfo、Copilot|用户交互轨迹/修正聚合为训练语料，群体飞轮|巨头专属，合规门槛最高|
|Harness/记忆|EverMe+Raven、Hermes GEPA、CopilotKit、阿里 AgentLoop|跨会话记忆→运行策略/工具规则调整，经验库回流|产品化进行中，企业级平台已上线|
|工具底座|EvoSOP、UCT、Dust|从执行轨迹造/优化工具|论文为主，产品化零星|
|Skill|Dust Self-Improving Skills、Warp、Skill Creator 2.0、华为云|用户使用+反馈→自动生成改进建议→人工审批|产品化最活跃|

## 几个关键判断

1. **进化深度和更新频率成反比**。改 skill/prompt 最便宜、最快、最容易回滚，所以最先产品化；改 harness（记忆、工具规则）次之；改模型权重最贵、最慢、受隐私合规约束，所以只有平台型巨头在做，而且都是"群体级"而非"个人级"。
2. **"用户修正"是最强信号但最稀疏**。所以所有产品都在补隐式信号（编辑、撤销、该调工具没调、对话跑偏），并用批量分析（Dust 的夜间批量、AgentLoop 的轨迹流水线）摊薄成本。
3. **隐私/数据主权是分水岭**。企业级方案强调数据隔离和自托管（CopilotKit、AgentLoop 的 AgentSpace 多租户），C 端方案强调用户授权和记忆可管理（EverMe），模型层则靠脱敏和 opt-out。
4. **完整闭环目前只存在于少数产品**。真正把"用户数据→观测→评估→优化→再上线"全链路做成平台的是阿里 AgentLoop、CopilotKit Intelligence 这类"进化基础设施"，而把"用户数据→skill 改进"做进日常产品的代表是 Dust 和 Warp。工具底座的独立进化，还在等一个杀手级产品。

一句话回答你的问题：**有，四个层面都有真实方案，但落地程度从 skill（最成熟）→ harness/记忆（产品化中）→ 工具底座（论文为主）→ 模型权重（巨头专属）依次递减**；而几乎所有方案的核心，都是把"用户修正"当成免费的、持续产生的标签数据。