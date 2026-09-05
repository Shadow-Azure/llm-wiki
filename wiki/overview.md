---
type: overview
created: 2026-08-23
updated: 2026-08-30
sources:
  - "raw/2026-04-04-karpathy-llm-wiki.md"
  - "raw/2026-08-23-obsidian-docs-build-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-submit-your-plugin.md"
  - "raw/2026-08-23-obsidian-docs-developer-policies.md"
  - "raw/2026-08-23-dsh-docs-plugin-publish.md"
  - "raw/2026-08-23-dsh-docs-subsystem-extensions.md"
---

# 综述

本库是一个**通用个人知识库**，按 [[wiki/concepts/llm-wiki|LLM Wiki]] 模式运行：人收集来源和提问，LLM 编译并维护全部 wiki 页面。

## 当前图景（2026-08-23）

- **规模**：48 个来源（raw），21 个 wiki 页面。处于最早期。
- **已确立的核心认识**：知识管理的胜负手不在检索而在**维护**——RAG 每次查询重新推导、没有积累；把知识编译进互相链接的持久页面，维护成本由 LLM 压到趋近于零，知识才能复利。见 [[wiki/concepts/wiki-vs-rag|编译式知识库 vs RAG]]。
- **本库即方法的自证**：仓库自身就是该模式的实例，方法论源头是 [[wiki/sources/karpathy-llm-wiki|Karpathy 的原文]]。

## 正在形成的主题

- 知识管理 / LLM 与个人工具（等待第一批真实领域来源进来后自然分化）
- **平台扩展性 / 插件机制**：双锚点展开——Obsidian（[[wiki/concepts/obsidian-plugin-development|开发]] / [[wiki/concepts/obsidian-plugin-distribution|分发]] / [[wiki/concepts/obsidian-plugin-networking|网络访问]]，官方目录 + 审核的闭环）与 [[wiki/entities/deepseek-harness|deepseek-harness]]（[[wiki/concepts/dsh-plugin-development|开发]] / [[wiki/concepts/dsh-plugin-distribution|分发]] / [[wiki/concepts/dsh-plugin-networking|网络与动态加载]]，无特权内核 + 无官方市场）；[[wiki/concepts/plugin-mechanism-comparison|三方对比]]（含 VSCode、[[wiki/entities/claudian|Claudian]] 实例）是两条主线的汇合点
- **视频播放插件构想双平台化**：[[wiki/concepts/video-player-plugin-feasibility|Obsidian 版]]（demo 已验证）与 [[wiki/concepts/dsh-video-plugin-feasibility|dsh 版]]（Web UI 有官方扩展点，且多出"agent 动态生成插件"路线）——同一功能在两种插件哲学下的实现对照，本身正在成为理解两类平台的试金石
- **dsh 双文档面（2026-08-30 起）**：插件生态面（[[wiki/sources/dsh-official-docs|官方文档 32 页]] → 开发/分发/网络/前端/能力边界五页）+ 运行时架构面（[[wiki/sources/dsh-source-runtime|源码仓实读 v0.1.2-alpha.1]] → [[wiki/concepts/dsh-agent-loop|agent loop]] / [[wiki/concepts/dsh-session-log|session log]] / [[wiki/concepts/dsh-tool-pipeline|tool pipeline]] / [[wiki/concepts/dsh-coordination-subsystems|协调子系统]] 四页）。运行时面的核心发现：dsh 把"策略"统一为管线上的 waterfall 监听者，把自治全部限幅（goal 256 轮、jobs 唤醒预算 3、resume 必须人工 re-arm）
- **与 self-opt 检索库的汇流**：[[raw/self-opt/INDEX.md|自进化四层图谱]]（harness/skill 层的 17 个方法核实）与 dsh 源码研究在此相交——dsh 的 skill 写入即生效 + goal/continuable subagent + feedback 留白，是"用户数据驱动自进化"唯一的**开源可自托管**完整实现样本；对照页待做

## 开放问题

- Karpathy 原文评论区有大量社区实践（团队规模经验、并发 ingest、人工修正的存活机制），尚未剪藏——值得作为第二个来源。
- Karpathy 提到 "the wiki is a persistent, compounding artifact"，但**复利何时开始递减**（规模、矛盾积累、维护成本回升）原文未展开，暂无库内答案。
- Obsidian 插件"装了即全权"的信任模型（同进程、无沙箱）与市场审核之间的张力：官方治理完全靠入口审核 + 政策约束，无运行时隔离——见 [[wiki/concepts/plugin-mechanism-comparison|三方对比]] 洞察 3。
- dsh 动态插件的隔离边界：`dynamicCordisRunner` / `dsh-tool-cordis` 允许 agent 自写自挂插件（内存态、卸载即消失），官方自认"可能影响同一进程中的其他会话"——审批闸门能守住"谁批准"，守不住"批准后跑飞"，运行时隔离缺位与 Obsidian 同病（见 [[wiki/concepts/dsh-plugin-networking]]）。
- dsh 无官方插件市场：发现层完全外包给 npm/GitHub topic + 社区自建市场（300+ 插件收录），质量与安全把关机制如何演化值得跟踪（社区市场来源待剪藏）。

## 下一步

1. 剪藏并 ingest 第一篇非方法论来源（任意你感兴趣的领域）
2. 之后每 ~10 次摄入跑一次 lint
