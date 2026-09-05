---
type: index
created: 2026-08-23
updated: 2026-08-30
sources: []
---

# 索引

> 查询第一入口：回答问题前先读本目录定位相关页面，再深入阅读。每次 ingest 后由 LLM 更新。

## 来源摘要（sources/）

- [[wiki/sources/karpathy-llm-wiki|Karpathy《LLM Wiki》（2026-04-04）]] — LLM 增量编译维护个人 wiki 的模式原文（gist），本库的方法论源头
- [[wiki/sources/obsidian-developer-docs|Obsidian 官方开发者文档（2026-08-23 快照，15 页）]] — 插件开发/构建/提交流程/政策红线/requestUrl 的官方规范合集
- [[wiki/sources/dsh-official-docs|DeepSeek Harness 官方文档（2026-08-23 快照，32 页）]] — 插件开发四部曲/Cordis 教程/实操手册/五子系统/CLI 参考合集；"everything is a plugin"的一手规范
- [[wiki/sources/dsh-source-runtime|dsh 源码仓运行时架构（2026-08-30 实读，v0.1.2-alpha.1）]] — agent 循环/会话日志/工具管线/协调子系统九份架构文档剪藏 + 本地源码实读（`/Users/zn-ice/2026/deepseek-harness` @ cd5ef814）

## 概念（concepts/）

- [[wiki/concepts/llm-wiki|LLM Wiki]] — 由 LLM 持久编译维护的知识库：三层架构、三大操作、index+log 导航，维护成本趋零使知识复利
- [[wiki/concepts/wiki-vs-rag|编译式知识库 vs RAG]] — 查询时临时检索拼装 vs 摄入时编译一次持续保鲜；积累性、交叉引用、矛盾处理的全维度对比
- [[wiki/concepts/note-tool-landscape|笔记工具格局]] — 本地优先 vs 云端 SaaS 两大阵营；国内外开源/闭源盘点、AGPL/MIT/MPL/EE 协议商用含义、"Obsidian 最适合接 AI"论证（AI 推断待验证）
- [[wiki/concepts/bidirectional-links|双向链接与关系图谱]] — 前向链接只存一份、反向索引算"谁指向我"、图谱是可视化投影；断链合法与多重上下文两大机制；与 LLM Wiki 模式的微观连接
- [[wiki/concepts/obsidian-plugin-development|Obsidian 插件开发]] — 三件套解剖（manifest/主入口/样式）、Plugin 生命周期、esbuild 构建、versions.json 回退、设置存 data.json、UI 接入六插槽（前端新手向，含视频面板最小示例）
- [[wiki/concepts/obsidian-plugin-distribution|Obsidian 插件分发]] — 社区目录闭环：GitHub Release 三资产 → 自动审核四节 → 发布；更新免重提；政策红线与披露义务
- [[wiki/concepts/obsidian-plugin-networking|Obsidian 插件网络访问]] — requestUrl 免 CORS 的官方通道；网络使用须披露、自更新禁令；动态资源可行性与代码加载的灰色地带
- [[wiki/concepts/dsh-plugin-development|dsh 插件开发]] — 官方阅读路线、插件三形态、fiber 生命周期、defineTool DSL、能力三角色分包
- [[wiki/concepts/dsh-plugin-distribution|dsh 插件分发]] — 组合包（npm+dsh.bundle manifest）；四安装来源与层叠规则；官方无市场，社区围绕 dsh-plugin topic 自建
- [[wiki/concepts/dsh-plugin-networking|dsh 插件网络与动态加载]] — 同进程全权调外部服务+凭据 seam；三层动态加载（HMR/lazy bundle/dynamicCordisRunner——agent 可自写自挂插件）
- [[wiki/concepts/dsh-frontend-integration|dsh 插件前端接入]] — 浏览器半（dsh.client）+ slots 系统：五个接入点、~20 具名插槽、官方 Web 四层装配、组装纪律与打包三坑
- [[wiki/concepts/dsh-plugin-capability-boundaries|dsh 插件化能力边界]] — 30 core / 28 seam / 1 bundle 角色普查；core 两种动机（公告栏 vs 大门）；"能拔≠能用"；脊柱两层；上报打点=seam 默认关 vs 计量=core
- [[wiki/concepts/cordis-vendoring|Cordis vendoring]] — dsh 源码收编框架层：9 包清单、@deepseek-ai rescope、18 处本地修改；上游 cordiverse/cordis 为 MIT（Shigma，即 Koishi 作者）
- [[wiki/concepts/plugin-mechanism-comparison|插件机制三方对比]] — Obsidian（同进程全权）vs deepseek-harness（无特权内核†）vs VSCode（进程隔离+受限 API）；声明式谱系、扩展对象、隔离换自由五条洞察（†2026-08-25 精化：形态无特权，30 core 脊柱固定）
- [[wiki/concepts/video-player-plugin-feasibility|本地视频播放器插件可行性]] — 完全可行（demo 已验证）；功能阶梯 L0–L4、合规检查、Media Extended 竞品与 AI 原生差异化
- [[wiki/concepts/dsh-video-plugin-feasibility|dsh 视频播放插件可行性]] — Web UI 完全可行（conversation node 渲染 video + webServer 自建路由）；工具卡片路线不可行；agent 动态生成插件是独有玩法
- [[wiki/concepts/dsh-agent-loop|dsh agent loop]] — turn/step 状态机、inbox durable 投影（followup/steer/inject 唤醒语义）、pre-step 权威决策、取消恢复与续接三层、scope per-agent 注册
- [[wiki/concepts/dsh-session-log|dsh 会话日志]] — append-only 单一事实源、"model-visible means logged" 运行时不变量、surface 投影与 51 事件词表、compaction 原地替换、崩溃修复、spill 豁免
- [[wiki/concepts/dsh-tool-pipeline|dsh 工具管线]] — 三道 waterfall + monotonic guards + fail-closed 审批 + fs 闸门 + 沙箱世界共享；PTC 代码模式（run_code 子调用携父 token 重入管线）
- [[wiki/concepts/dsh-coordination-subsystems|dsh 协调子系统]] — skill 渐进披露/写入即生效、subagent 变体与 depth 记账、goal/Ralph/jobs 的自治限幅（人工 re-arm、唤醒预算）、feedback 留白与元级自证（.agents/notes 1646 篇）

## 实体（entities/）

- [[wiki/entities/andrej-karpathy|Andrej Karpathy]] — AI 研究者与教育者，LLM Wiki 模式提出者（2026-04）
- [[wiki/entities/obsidian|Obsidian]] — 本地 Markdown 知识库的查看器/编辑器，LLM Wiki 模式中人类一侧的"IDE"；软件闭源、数据本地开放
- [[wiki/entities/claudian|Claudian]] — 把 Claude Agent SDK 嵌入 Obsidian 的桌面插件（本库 agent 侧）；同进程 spawn CLI + 聊天内联批准；"插件里养插件"的生态桥实例
- [[wiki/entities/deepseek-harness|deepseek-harness（dsh）]] — DeepSeek 开源 agent harness；Cordis 插件框架（vendored，上游 MIT），"everything is a plugin"（形态统一；能力边界 30 core/28 seam/1 bundle）；双端插件 + 动态插件（agent 自写自挂）；官方文档已落库

## 综述

- [[wiki/overview|综述（overview）]] — 全库知识图景的演化叙述
