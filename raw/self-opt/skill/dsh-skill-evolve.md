---
title: "dsh-skill-evolve — DeepSeek Harness 社区插件：从成功会话提炼 skill"
layer: skill
source_urls:
  - https://github.com/dmsobtl/dsh-skill-evolve
  - https://github.com/search?q=dsh-skill-evolve&type=repositories
accessed: 2026-08-26
local_pdf: null
---

# dsh-skill-evolve

> 一句话：一个 DeepSeek Harness（dsh）社区插件（TypeScript），把 agent 成功完成任务的会话记录（session.jsonl）自动提炼成可复用的 skill markdown 文件——"从成功会话中自动提炼可复用 skill，越用越聪明"。

## 是什么 / 背景

- **仓库**：https://github.com/dmsobtl/dsh-skill-evolve （GitHub 仓库搜索 "dsh-skill-evolve" 仅此一个精确匹配，2026-08-26 检索）。
- **仓库元数据**（GitHub API，2026-08-26 查询）：描述"DSH 插件：Agent 自我进化引擎 — 从成功会话中自动提炼可复用 skill，越用越聪明"；语言 TypeScript；topic `dsh-plugin`；**0 star、2 commits**；创建于 **2026-08-14**、最后推送 2026-08-14——非常早期、极小众的个人社区插件，非官方组件。
- **生态背景**：DeepSeek Harness（dsh）是"everything is a plugin"的 agent harness（官方站 https://deepseek.com/harness/en/ ），GitHub 上有 700+ 仓库打 `dsh-plugin` 标签（BAAI hub 文章 https://hub.baai.ac.cn/view/57162 ）。本库 raw/ 已收录一批 2026-08-23 的 dsh 官方文档剪藏（`raw/2026-08-23-dsh-docs-*.md`），含插件机制（plugin lifecycle/events/config）。
- MIT 许可。

## 机制：怎么从会话/经验进化技能

README 给出的原理链（https://github.com/dmsobtl/dsh-skill-evolve ）：

```
Agent 完成任务 → session.jsonl 记录全过程
              → extract_skill 分析 session
              → 提炼工具调用序列 → 生成 skill markdown
              → 下次类似任务 → Agent 直接按 skill 执行（更快更准）
```

- **两个工具**：
  - `extract_skill`：从 session JSONL 提炼为 skill 文件；
  - `list_learned_skills`：列出所有已学习的 skill。
- **配置项**：`skillsDir`（生成的 skill 存放目录，默认 `./skills/learned`）、`minSteps: 3`（少于 3 步的会话不提取）。
- **生成的 skill 形态**：markdown 文件，含"触发条件"（当用户需求类似某任务时使用）与"执行步骤"（如 `git pull origin main` → `npm run build` → `npm test` → 读配置 → `kubectl apply -f k8s/staging.yml`），并自动标注来源会话与生成日期（示例为 2026-08-14）。
- **触发方式**：README 的使用示例是**用户主动发起**——"刚才部署的流程挺顺利的，帮我把它提炼成 skill 以后复用"，agent 调 `extract_skill` 完成。即半自动、用户驱动的提炼，而非后台自动观察。
- **无验证/无版本管理**：仓库未见沙箱验证、评测或版本化账本设计；质量保障依赖配套插件的组合用法。
- **与其他插件配合**（README "与其他插件配合"节）：
  - `dsh-session-analyst`：先 `analyze_session` 确认会话质量好，再 `extract_skill`；
  - `dsh-agent-eval`：新 skill 生效后跑 eval 验证是否提升。

## 关键事实与数字（附出处）

| 事实 | 说法 | 出处 |
|---|---|---|
| 插件定位 | "Agent 自我进化引擎 — 从成功会话中自动提炼可复用 skill，越用越聪明" | https://github.com/dmsobtl/dsh-skill-evolve |
| 提取粒度 | "提取了 7 个步骤，使用了 bash, read, write 3 个工具"（README 示例输出） | 同上 |
| 最小提取阈值 | `minSteps: 3`（少于 N 步不提取） | 同上（配置节） |
| 成熟度 | 0 star / 2 commits / 2026-08-14 创建（GitHub API） | https://api.github.com/repos/dmsobtl/dsh-skill-evolve |
| 生态规模参照 | GitHub `dsh-plugin` 标签 700+ 仓库 | https://hub.baai.ac.cn/view/57162 |

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| dsh-skill-evolve 是 DeepSeek Harness 社区插件 | GitHub `dmsobtl/dsh-skill-evolve`，topic `dsh-plugin`，TypeScript，MIT | ✅ |
| "从成功会话提炼 skill" | README 描述与 `extract_skill` 工具完全对应 | ✅ |
| （隐含：自动进化） | 提炼动作由用户在对话中触发（示例话术"帮我把它提炼成 skill"）；无沙箱验证、无自动回滚 | ⚠️ 半自动：提炼是自动的，触发是用户驱动的 |

## 关联项：dsh-auto-evolve（不单独立档）

同生态中更接近"系统自动进化"的插件是 **dsh-auto-evolve**（https://github.com/lispking/dsh-auto-evolve ，维护者 @lispking；dsh.so 插件登记页 https://www.dsh.so/artifact/dsh-auto-evolve/ ）：

- GitHub API 官方描述（2026-08-26 查询）："A self-evolving plugin for DeepSeek Harness (dsh). **It observes how the agent runs, proposes improvements to its own assets via the LLM, validates each proposal inside a sandboxed trial agent, and applies only verified mutations — with a versioned ledger and automatic rollback on regression.**"
  ——与"观察运行→LLM 提案→沙箱验证→版本化账本+自动回滚"逐项对应 ✅。
- 元数据：2 stars；创建 2026-08-22，最后推送 2026-08-25；topics：`ai`、`deepseek-harness`、`dsh-plugin`、`self-evolving`。
- 两插件对照：dsh-skill-evolve = 用户触发、提炼会话为 skill、无验证；dsh-auto-evolve = 自动观察、LLM 提案、沙箱试验 + 版本账本 + 回归回滚。后者是 dsh 生态里"治理式自进化"的更完整实现（另见 oh-my-dsh 讨论串 https://github.com/deepseek-ai/deepseek-harness/discussions/3113 的 "governed self-evolution" 叙事）。

## 本地文件与延伸阅读

- 仓库：https://github.com/dmsobtl/dsh-skill-evolve ；关联：https://github.com/lispking/dsh-auto-evolve
- 本库已有的 dsh 背景：`raw/2026-08-23-dsh-docs-plugin-lifecycle.md`、`raw/2026-08-23-dsh-docs-plugin-events.md`、`raw/2026-08-23-dsh-docs-first-plugin.md` 等剪藏
- 论文侧对照：Voyager 的 skill 库、EvoSOP 的 SOP 工具集（`raw/self-opt/tool/evosop.md`）、综述 *Dynamic Agent Skills*（`raw/self-opt/refs-survey-dynamic-agent-skills.md`）——dsh 这两个插件即该综述所谓 "SKILL.md sense" 技能与"生命周期管理"思想的社区实现
