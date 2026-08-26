---
title: "Warp：Skill 优化闭环——observer 评分、PR 化技能演进与团队反馈回流"
layer: skill
source_urls:
  - https://www.warp.dev/blog/building-a-skill-optimization-loop
  - https://www.warp.dev/blog/self-improvement-loop-for-skills
  - https://www.anthropic.com/webinars/how-warp-builds-self-improving-agents-on-claude
  - https://zenn.dev/noah33/articles/teaching-agents-to-learn?locale=en
accessed: 2026-08-26
local_pdf:
---

# Warp 的 Skill 优化闭环（Skill Optimization Loop）

> 一句话：Warp 用"外层 agent 观察并评分内层 skill"的方式构建自动改进闭环——observer skill 调用内层 skill、用 computer use 实际检验产出、分析失败模式后生成 diff 提 PR 更新 skill；对无法自动判优的判断型任务，则把团队日常的 Slack 反应/修正行为变成学习信号，每日汇总成 skill 文件的 PR——"把指令当代码"，编辑、评审、合并全走 Git 流程。

## 是什么 / 背景

Warp 是终端 + agent 公司（云 agent 平台 Oz），2025 年底起全面转向 Agent Skills 生态。其 skill 优化方法论由两条互补的线构成：

1. **自动评估闭环**（可验证任务）：工程博客 [Building a skill optimization loop](https://www.warp.dev/blog/building-a-skill-optimization-loop)（2026-06-18，作者 Zach Bai）。核心论断："You can apply this technique to create improvement loops any time a Skill has clear validation criteria." 博客区分两层循环：内层 loop 保证"这一次 replatforming 成功"，外层 loop（本文重点）让"replatforming 这个 Skill 本身下次更可能成功"。
2. **人类反馈闭环**（判断型任务）：Zach Lloyd 的 [How to build a self-improvement loop for your Skills](https://www.warp.dev/blog/self-improvement-loop-for-skills)（2026-06-16）与 Petra Donka 在 Anthropic "Code with Claude 2026"（伦敦）上的演讲 "Teaching agents to learn from your team"（[演讲视频](https://www.youtube.com/watch?v=uGroRwlC9y4)，[与会者详尽笔记](https://zenn.dev/noah33/articles/teaching-agents-to-learn?locale=en)），以 Warp 内部社交监听 agent "Buzz" 为案例。

## 机制：用户数据怎么流动

### 线路 A：自动评分闭环（observer + grader + computer use）

（依据 [Building a skill optimization loop](https://www.warp.dev/blog/building-a-skill-optimization-loop)）

- **信号来源**：不是用户对话，而是自动生成的执行证据——observer skill 拿到 N 个待 replatform 的网站，逐个调用内层 skill（`/replatform-site`），把产出站点真实构建出来。
- **分析方式**：用 **computer use 和 browser use** 检查产站与原站的行为/视觉差异（例：下拉菜单图标缺失）；同时统计 replatforming 消耗的 token 数，在保质量前提下优化成本；最后用 SOTA 模型综合结构化结果，"looks for failure patterns and opportunities to improve"。observer 内置退出条件（exit criteria），避免无限烧 token 优化。
- **生成什么改进**：针对内层 skill 的 diff（博客附了实际生成的 diff 截图：发现 dropdown 图标问题后建议的改进）。
- **怎么上线**："Because Skills are just files, you can use any coding agent like Warp to do this analysis and submit a PR to update the Skill." 走 PR 评审合并。要在大语料上调优，就扩大输入站点数、持续迭代"直到 observer 生成的 diff 不再有实质意义"。
- **基础设施**：需要支持多 agent 编排 + computer use 的平台；作者用 Warp 内置的 Oz（跨多个 SOTA 模型提供 computer use），并另做了一个 `/oz-orchestrated-replatforming` skill。相关 skill 全部开源：[github.com/warpdotdev-demos/replatformer](https://github.com/warpdotdev-demos/replatformer)。
- 作者自述局限："there's only so much you can improve by tuning Skills and it's susceptible to finding local maxima"。

### 线路 B：团队反馈回流（人类判断即训练信号）

（依据 Zach Lloyd 博客与 Petra Donka 演讲笔记）

- **信号来源**：内层 agent 每次 skill 运行都留痕（文件 / agent trace / Slack 或 GitHub 上的交互）；对 Buzz 而言，是团队在其 Slack 建议消息上的 emoji 反应与线程评论——"repurposing existing signals that everyone already uses daily... as learning signals"（零摩擦反馈回路）。用户手工改掉 agent 的分类结果并留言原因，也是信号。
- **分析方式**：外层 agent 每天跑一次，"looks at all the runs of the inner agent"；Buzz 案例中："once a day, Buzz collects the reactions and thread feedback, compares its recommendations to what the team actually did, extracts durable learnings, updates the relevant skill files, and opens a PR." 关键设计：把"如何从反馈中学习"本身写成独立 skill（两层 skill：干活的原则层 + 学习如何学原则的元层），避免 agent 把每条修正都退化成死规则。
- **生成什么改进**：skill 文件的小 diff——粒度控制在"60 秒 PR review"可完成（几行、带上下文、插入相关小节）。
- **谁审批**：人。"The daily learning agent does not directly change production behaviour. It opens a PR... A human reviews it like any other change."
- **怎么上线/回滚**：skill 文件即 Git 版本控制的文本资产，"change history, reviews, and rollbacks are all covered by existing software engineering practices"，直接复用 Pull Request 文化。

### 方法论表述（对应"把指令当代码"）

Petra Donka 演讲的核心句（转引自 [Zenn 笔记](https://zenn.dev/noah33/articles/teaching-agents-to-learn?locale=en)）：

> "The best way to get leverage from agents is not to turn everyone into prompt engineers. It is to design workflows where the team's normal judgement and taste become a training signal for the systems around them."

即"让团队判断自动回流"的原始出处。配套原则：写 principles 不写 rules（Buzz 的 skill 文件缩到原来 1/5 长度而质量反升）；"Agents need feedback loops, not perfect prompts"。

## 关键事实与数字（附出处）

| 事实 | 出处 |
|---|---|
| observer/grader 博客发布于 2026-06-18，作者 Zach Bai | [warp.dev/blog/building-a-skill-optimization-loop](https://www.warp.dev/blog/building-a-skill-optimization-loop)（publishedTime 元数据） |
| 自改进循环博客 2026-06-16，作者 Zach Lloyd（Warp 创始人/CEO） | [warp.dev/blog/self-improvement-loop-for-skills](https://www.warp.dev/blog/self-improvement-loop-for-skills) |
| Buzz：每月处理数千条提及，约 50% 判定为 skip；约 15 个 skill 文件、4 类（Triage/Creation/Reporting/Analysis）；每月数千次云 agent 运行；执行平台 Oz（cron/Webhook/API 触发） | [Zenn 笔记](https://zenn.dev/noah33/articles/teaching-agents-to-learn?locale=en)转述 Petra Donka 演讲数据 |
| principles 化改写使 skill 文件缩短至 1/5，质量不降反升 | 同上 |
| Anthropic 官方 webinar "How Warp builds self improving agents on Claude"，2026-05-13，主讲含 Zach Lloyd；主题含 "how to capture human feedback signals, turn them into skill updates"，demo PR review agent 与社交监听 agent | [anthropic.com/webinars/...](https://www.anthropic.com/webinars/how-warp-builds-self-improving-agents-on-claude)；[Claude Academy 点播页](https://academy.claude.com/all?kind=webinar&status=on-demand) |
| replatforming 全套 skill 开源 | [github.com/warpdotdev-demos/replatformer](https://github.com/warpdotdev-demos/replatformer) |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 用 observer skill + 自动 grader + computer use 评估内层 skill 表现，分析失败模式后生成 diff、提 PR 更新 skill | 逐项命中："creating an 'observer' skill that grades the quality of the 'inner' Skill... examines them for behavioral and visual differences using computer use and browser use... looks for failure patterns... creates a diff to update the inner Skill... submit a PR"（[博客](https://www.warp.dev/blog/building-a-skill-optimization-loop)） | ✅ |
| 在 "Code with Claude 2026" 活动上分享"把指令当代码，像 PR 一样编辑、评审、合并"的方法论 | "Code with Claude 2026" 为 Anthropic 在伦敦举办的开发者大会，确有其事；Warp DevEx 负责人 Petra Donka 在会上演讲 "Teaching agents to learn from your team"，内容为 skill 文件 Git 版本控制 + PR 评审 + 团队判断回流（[Zenn 笔记](https://zenn.dev/noah33/articles/teaching-agents-to-learn?locale=en)、[YouTube](https://www.youtube.com/watch?v=uGroRwlC9y4)） | ✅ |
| "让团队判断自动回流" | "design workflows where the team's normal judgement and taste become a training signal for the systems around them"（同上，演讲原话） | ✅ |
| （隐含）observer/grader 闭环即在 Code with Claude 2026 上分享 | 需拆分：observer+grader 自动评估闭环出自 2026-06-18 工程博客（Zach Bai）；Code with Claude 2026 演讲讲的是**人类反馈**闭环（Buzz 案例）。两条线同属 Warp skill 优化方法论，但博客本身未提及该活动 | ⚠️ |

结论：原 passage 的两条 Warp 声明均属实，仅"方法论在何处分享"的归属需要拆开——自动评估闭环（observer/grader）见官方工程博客；"把指令当代码 + 团队判断回流"的演讲在 Code with Claude 2026 London（另有一场 Anthropic 官方 webinar，2026-05-13，与此互补）。

## 本地文件与延伸阅读

- 本地：`raw/self-opt/skill/anthropic-agent-skills-skill-creator.md`（Agent Skills 标准与 Skill Creator，Warp 方法论的底层格式）；`raw/self-opt/harness/`（harness 层对照）
- [Building a skill optimization loop（2026-06-18）](https://www.warp.dev/blog/building-a-skill-optimization-loop)
- [How to build a self-improvement loop for your Skills（2026-06-16）](https://www.warp.dev/blog/self-improvement-loop-for-skills)
- [How to build a cloud software factory – self-improving code review](https://www.warp.dev/blog/how-to-build-a-cloud-software-factory-self-improving-code-review)（外层 agent 每日复查 PR、把人类反馈综合成改进的又一案例）
- [Anthropic webinar 页面](https://www.anthropic.com/webinars/how-warp-builds-self-improving-agents-on-claude) / [演讲视频](https://www.youtube.com/watch?v=uGroRwlC9y4)
- [Zenn 与会笔记（英译，含大量演讲引文与数字）](https://zenn.dev/noah33/articles/teaching-agents-to-learn?locale=en)
