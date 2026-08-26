---
title: "Anthropic：Agent Skills 开放标准与 Skill Creator 的评测驱动改进闭环"
layer: skill
source_urls:
  - https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
  - https://agentskills.io/specification
  - https://github.com/anthropics/skills/blob/master/skills/skill-creator/SKILL.md
  - https://www.anthropic.com/webinars/how-warp-builds-self-improving-agents-on-claude
  - https://www.whytryai.com/p/how-to-test-claude-skills
accessed: 2026-08-26
local_pdf:
---

# Anthropic Agent Skills 标准 + Skill Creator（社区称 2.0）

> 一句话：Anthropic 把 skill 定义为"一个带 YAML frontmatter 的 SKILL.md + 可选脚本/参考/资源目录"，2025-12-18 将其发布为跨平台开放标准；官方 skill-creator 元技能则把 skill 的创建与改进工程化为"起草 → 带/不带 skill 并行子 Agent 评测 → 盲比较 + 统计基准 → 按反馈改写 → 触发描述自动优化（60/40 训练/测试集，最多 5 轮迭代防过拟合）"的闭环。

## 是什么 / 背景

**Agent Skills 标准**：2025 年 10 月 Anthropic 发布 Skills（工程博客 [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)，作者 Barry Zhang、Keith Lazuka、Mahesh Murag），随后于 **2025-12-18 将其发布为跨平台可移植的开放标准**（博客页首 Update 注记）。定义原文："organized folders of instructions, scripts, and resources that agents can discover and load dynamically to perform better at specific tasks"。规范独立站点为 [agentskills.io/specification](https://agentskills.io/specification)，官方仓库 [anthropics/skills](https://github.com/anthropics/skills)（含 `spec/agent-skills-spec.md`）。媒体报道称此举复制了 MCP 的标准化打法（[The New Stack](https://thenewstack.io/agent-skills-anthropics-next-bid-to-define-ai-standards/)），格式已获 OpenAI、Google DeepMind、Cursor、Codex CLI 等支持（[MindStudio 汇总](https://www.mindstudio.ai/blog/agent-skills-open-standard-claude-openai-google)）。

结构核心是渐进披露（progressive disclosure）三层：(1) frontmatter 的 `name` + `description` 常驻系统提示；(2) SKILL.md 正文（建议 <500 行 / <5000 token）在触发时载入；(3) 目录内捆绑文件（`scripts/` 可执行代码、`references/` 参考资料、`assets/` 模板资源）按需读取——代码可不进上下文直接执行。

**Skill Creator 演进**：1.0 于 2026 年 1 月随 Skills 推出（引导起草 SKILL.md）；**2026 年 3 月初 Anthropic 在 anthropics/skills 仓库上线大幅增强版**——[Why Try AI 报道](https://www.whytryai.com/p/how-to-test-claude-skills)（2026-04-23）："In early March, Anthropic rolled out an improved version of the Skill Creator. (Check it out on GitHub.) The new version is a serious step up... a suite of scripts, templates, sub-agents, and instructions that make Claude actually test your skill and improve it." 该版本被社区与界面称为 **"Skill Creator 2.0"**（如 [知乎详解](https://zhuanlan.zhihu.com/p/2021714938427647003) 描述 Create/Eval/Improve/Benchmark 四模式；华为云技术文章亦以 "Anthropic Skill Creator 2.0" 称之并描述为"提供可视化评估界面，自动重写触发描述并盲测比较输出质量"）。注意：Anthropic 官方仓库与博客使用的是 "improved version"/"skill-creator"，未见官方公告使用 "2.0" 字样——"2.0" 是产品界面与社区命名。

**能力愿景**：工程博客结尾预告"We hope to enable agents to create, edit, and evaluate Skills on their own"——skill 层自进化被官方列为方向。

## 机制：用户数据怎么流动

（依据官方 [skill-creator/SKILL.md 源文件](https://github.com/anthropics/skills/blob/master/skills/skill-creator/SKILL.md)，即该元技能的真实指令；辅以 [Why Try AI 流程综述](https://www.whytryai.com/p/how-to-test-claude-skills)）

Skill Creator 本身评测的是 skill 而非生产用户数据，其"数据"是测试提示词 + 人类评审反馈；整个闭环按功能可对应社区总结的 Create → Eval → Improve → Benchmark 四阶段：

**信号来源**
- 用户的自然语言需求与访谈（Capture Intent：从当前对话抽取工作流、修正、输入输出格式）。
- 测试运行的客观结果：断言（assertions）通过率、耗时、token 消耗。
- 人类评审反馈：eval-viewer 网页里逐例查看输出并留言（feedback.json；空反馈 = 满意）。

**分析方式（Eval）**
- 每个测试用例**同一轮并行 spawn 两个子 Agent——一个带 skill、一个不带**（新建 skill 时基线为无 skill；改进已有 skill 时基线为旧版本快照）。官方原文："For each test case, spawn two subagents in the same turn — one with the skill, one without."
- 断言在运行进行中并行起草；能程序化校验的断言写脚本判（比目测快且可靠）。
- 评分：grader 子 Agent（`agents/grader.md`）逐断言打 pass/fail，结果存 grading.json。
- 聚合：`scripts.aggregate_benchmark` 生成 benchmark.json/md——"pass_rate, time, and tokens for each configuration, with **mean ± stddev** and the delta"（统计均值±标准差与差值）。
- 分析师遍历（analyzer pass，`agents/analyzer.md`）：找出无区分度的断言、高方差（flaky）用例、时间/token 权衡等聚合统计掩盖的模式。
- **盲 A/B（blind comparison）**：官方原文 "give two outputs to an independent agent without telling it which is which, and let it judge quality. Then analyze why the winner won."（`agents/comparator.md`）。

**生成什么改进（Improve）**
- 依据人类反馈 + 定量基准重写 skill；官方改进哲学：从反馈泛化（防过拟合到少数例子）、保持 prompt 精简、解释"为什么"而非堆 MUST、把测试中反复出现的重复劳动固化为捆绑脚本进 `scripts/`。
- 迭代循环无固定轮数上限："Keep going until: The user says they're happy / The feedback is all empty / You're not making meaningful progress"，然后"Expand the test set and try again at larger scale"。

**触发描述优化（Description Optimization，独立子流程）**
- 生成 20 条真实风格的触发评测查询（8-10 条应触发 + 8-10 条近似不触发），用户经 HTML 模板审核。
- 自动优化循环（`scripts.run_loop`，官方原文）："It splits the eval set into **60% train and 40% held-out test**, evaluates the current description (running each query 3 times to get a reliable trigger rate), then calls Claude to propose improvements based on what failed. It re-evaluates each new description on both train and test, **iterating up to 5 times**... returns JSON with `best_description` — **selected by test score rather than train score to avoid overfitting**."（命令默认 `--max-iterations 5`。）

**谁审批 / 怎么上线**
- 人类（skill 作者）全程在环：审测试提示、审断言、在 eval-viewer 里逐例评审留言、决定何时停止；最终 skill 打包为 `.skill` 文件安装。无生产环境自动上线/回滚概念——它是创作期工具，不是线上自进化系统。

## 关键事实与数字（附出处）

| 事实 | 出处 |
|---|---|
| Agent Skills 工程博客发布（2025-10），2025-12-18 更新为开放标准 | [anthropic.com/engineering](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) |
| 开放标准规范站（SKILL.md 建议 <5000 token 等规格） | [agentskills.io/specification](https://agentskills.io/specification) |
| skill-creator 增强（社区称 2.0）2026 年 3 月初上线 GitHub | [whytryai.com（2026-04-23）](https://www.whytryai.com/p/how-to-test-claude-skills) |
| 四组件：Grader / Blind Comparator / Analyzer / Skill Description Improver | 同上；对应仓库 `skills/skill-creator/agents/` |
| 带/不带 skill 双子 Agent 并行、mean ± stddev、60/40 split、最多 5 轮迭代、按测试集选优防过拟合、每查询跑 3 次测触发率 | [skill-creator/SKILL.md 官方源文件](https://github.com/anthropics/skills/blob/master/skills/skill-creator/SKILL.md) |
| Anthropic 官方 webinar（2026-05-13）"How Warp builds self improving agents on Claude"：讲 "how to capture human feedback signals, turn them into skill updates"，Claude Academy 点播描述为 "learn from human feedback to rewrite their own skills" | [webinar 页](https://www.anthropic.com/webinars/how-warp-builds-self-improving-agents-on-claude)；[academy.claude.com](https://academy.claude.com/all?kind=webinar&status=on-demand) |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| Agent Skills 开放标准（SKILL.md + 脚本目录结构） | 标准发布于 2025-12-18；skill = SKILL.md（YAML frontmatter name/description）+ 可选 scripts/references/assets 目录（[工程博客](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)、[spec](https://agentskills.io/specification)） | ✅ |
| Skill Creator 2.0：Create→Eval→Improve→Benchmark 闭环 | 四阶段结构与官方 SKILL.md 流程一一对应；但 "Create/Eval/Improve/Benchmark" 四模式名出自产品界面/社区描述（[知乎](https://zhuanlan.zhihu.com/p/2021714938427647003)），官方仓库原文未用此措辞，官方亦未用 "2.0" 名称 | ✅（内容）/ ⚠️（命名归属） |
| Eval 用带/不带 skill 的子 Agent 并行跑 + 盲 A/B + 统计分析 | 三项全部命中官方源文件（见上"机制"节引文） | ✅ |
| Improve 用 60/40 训练/测试集防过拟合、最多 5 轮迭代 | 数值属实，但归属需修正：60/40 split 与 max 5 iterations 属于**触发描述优化**（Description Optimization）脚本循环，是 Create/Eval/Improve 之后的独立步骤，而非通用 Improve 阶段；通用改进循环以"用户满意/无空反馈/无实质进展"为停机条件，无固定轮数（[官方源文件](https://github.com/anthropics/skills/blob/master/skills/skill-creator/SKILL.md)） | ⚠️ |
| 有官方 webinar 讲 Warp 如何"把人类反馈信号转成 skill 更新" | 2026-05-13 Anthropic 官方 webinar 原文 "how to capture human feedback signals, turn them into skill updates"（[webinar 页](https://www.anthropic.com/webinars/how-warp-builds-self-improving-agents-on-claude)） | ✅ |

结论：原 passage 声明基本属实。两处需精确化：(1) "Skill Creator 2.0" 为社区/界面命名，官方称 improved version；(2) 60/40 与 5 轮迭代是触发描述优化的参数，不是 Improve 阶段通用参数。另需区分：Skill Creator 是**创作期评测驱动改进**（测试集 + 人工评审），不同于 Dust/华为云那种**生产环境用户数据回流**——两者同属"评估驱动的工程优化"流派但数据源不同。

## 本地文件与延伸阅读

- 本地：`raw/self-opt/skill/warp-skill-optimization-loop.md`（Warp 在此标准上的实践）、`raw/self-opt/skill/huawei-skill-patch.md`（该文以 Skill Creator 2.0 为"评估驱动工程优化"流派代表之一）
- [工程博客原文](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [Agent Skills 规范](https://agentskills.io/specification) / [anthropics/skills 仓库](https://github.com/anthropics/skills)
- [skill-creator/SKILL.md（官方源文件，机制第一手出处）](https://github.com/anthropics/skills/blob/master/skills/skill-creator/SKILL.md)
- [Why Try AI：How To Tell If Your Claude Skills Are Any Good](https://www.whytryai.com/p/how-to-test-claude-skills)（含 1.0→2.0 时间线与流程图解；另指出官方断言编写指引薄弱的批评）
- [知乎：Skill Creator 2.0 四模式详解](https://zhuanlan.zhihu.com/p/2021714938427647003)
