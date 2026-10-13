---
type: index
created: 2026-08-23
updated: 2026-10-13
sources: []
---

<!-- 2026-09-06 四次更新：开源扩展盘点 + open-notebook 实读 + 类 Obsidian 开源格局实测（research）+ Tauri/Electron/cc-switch 架构分析回填（query-filed） -->

# 索引

> 查询第一入口：回答问题前先读本目录定位相关页面，再深入阅读。每次 ingest 后由 LLM 更新。

## 来源摘要（sources/）

- [[wiki/sources/karpathy-llm-wiki|Karpathy《LLM Wiki》（2026-04-04）]] — LLM 增量编译维护个人 wiki 的模式原文（gist），本库的方法论源头
- [[wiki/sources/obsidian-developer-docs|Obsidian 官方开发者文档（2026-08-23 快照，15 页）]] — 插件开发/构建/提交流程/政策红线/requestUrl 的官方规范合集
- [[wiki/sources/dsh-official-docs|DeepSeek Harness 官方文档（2026-08-23 快照，32 页）]] — 插件开发四部曲/Cordis 教程/实操手册/五子系统/CLI 参考合集；"everything is a plugin"的一手规范
- [[wiki/sources/dsh-source-runtime|dsh 源码仓运行时架构（2026-08-30 实读，v0.1.2-alpha.1）]] — agent 循环/会话日志/工具管线/协调子系统九份架构文档剪藏 + 本地源码实读（`/Users/zn-ice/2026/deepseek-harness` @ cd5ef814）
- [[wiki/sources/hkuds-videorag-vimo|HKUDS VideoRAG / Vimo（2025-02，KDD'2026）]] — 开源"chat with your videos"图 RAG 框架 + Electron 桌面应用（Beta 未发布）；README 全文剪藏
- [[wiki/sources/omagent-video-understanding|OmAgent video_understanding（EMNLP 2024）]] — ASR+场景切分+抽帧+向量索引+DnC 任务树的教科书开源管线；Rewinder 工具
- [[wiki/sources/hkuds-videoagent|HKUDS VideoAgent（2025-07，~1.8k stars）]] — 理解+剪辑+重制一体的多智能体框架；图驱动工作流生成；Q&A 仅需 Whisper（transcript QA）
- [[wiki/sources/player-embedded-ai-snapshot|播放器内嵌 AI 形态快照（7 来源，2023-10~2026-08）]] — YouTube/B站原生 AI + LunaNotes 等扩展品类拥挤；全部 transcript-based；Obsidian 生态无 AI
- [[wiki/sources/video-understanding-api-products|视频理解 API 层与垂直产品快照（7 来源）]] — TwelveLabs/VideoDB/通义听悟 API 层；tl;dv/Knowt/BibiGPT 垂直应用；MCP 成视频工具标配
- [[wiki/sources/video-agent-academic-landscape|视频 agent 学术与参考架构快照（6 来源）]] — VideoAgent ECCV/Video-RAG NeurIPS/NVIDIA 五段管线/Video-MME 基准（长视频墙 + ASR 融合 +7~10pt）
- [[wiki/sources/media-extended-v4-no-ai|Media Extended v4.0 发布说明（2025-09-17）]] — 官方一手证据：零 AI 能力，AI 是转闭源动因
- [[wiki/sources/oss-video-extension-landscape|开源在线视频分析项目格局（star 实测 2026-09-06）]] — B站/YouTube 方向开源高星盘点：BiliNote 7.2k（本地后端+MV3 插件+字幕优先策略）、BibiGPT-v1 6.2k、biliscope 827；纯扩展高星稀疏
- [[wiki/sources/open-notebook-architecture|Open Notebook 关键文件选辑（2026-09-06 本地实读）]] — ⭐38.3k NotebookLM 开源替代：ADR-004/007 全文 + 摄取图源码注解 + 容器编排配置
- [[wiki/sources/bilinote-architecture|BiliNote 关键源码选辑（2026-09-06 本地实读）]] — B站 player API 字幕直拉七步 + yt-dlp 412 风控 monkey patch + 编排核心 skip_download 决策
- [[wiki/sources/obsidian-oss-alternatives-landscape|类 Obsidian 开源项目格局（star/协议实测 2026-09-06）]] — 22 仓盘点：PKM 三强 SiYuan 46.2k/Logseq 44.8k/Trilium 37.7k 全 AGPL；Anytype 实为 source-available、Outline 转 BUSL；SiYuan 二次开发通道最全

## 概念（concepts/）

- [[wiki/concepts/llm-wiki|LLM Wiki]] — 由 LLM 持久编译维护的知识库：三层架构、三大操作、index+log 导航，维护成本趋零使知识复利
- [[wiki/concepts/wiki-vs-rag|编译式知识库 vs RAG]] — 查询时临时检索拼装 vs 摄入时编译一次持续保鲜；积累性、交叉引用、矛盾处理的全维度对比；视频模态同一对照的印证（预索引 vs 直塞）
- [[wiki/concepts/wireless-data-agent-insights|无线基站数据资产的 Data Agent 洞察]] — 湖仓/对象存储双通道统一为数据产品控制面；资产发现、发布数据集体检、仿真与现网对比三条链路 + 五阶段落地路线
- [[wiki/concepts/note-tool-landscape|笔记工具格局]] — 本地优先 vs 云端 SaaS 两大阵营；国内外开源/闭源盘点 + star/活跃度实测（2026-09-06）；协议商用含义谱系（MIT→AGPL→BUSL/源码可见）；二次开发支持分级；"Obsidian 最适合接 AI"论证（AI 推断待验证，开源侧印证其生态位无人完整占据）
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
- [[wiki/concepts/video-player-plugin-feasibility|本地视频播放器插件可行性]] — 完全可行（demo 已验证）；功能阶梯 L0–L4、合规检查；竞品小节 2026-09 修订：Media Extended v4.0 一手证据零 AI，空位收窄为"本地库+KM+播放器内嵌 AI"
- [[wiki/concepts/dsh-video-plugin-feasibility|dsh 视频播放插件可行性]] — Web UI 完全可行（conversation node 渲染 video + webServer 自建路由）；工具卡片路线不可行；agent 动态生成插件是独有玩法；MCP 对接视频理解 API 是现实通道
- [[wiki/concepts/ai-video-assistant-landscape|AI 视频助手格局]] — "AI agent + 视频"五层格局（平台原生/浏览器扩展/垂直应用/API 基础设施/开源工程）；transcript-first 压倒性主流；agent 动作三档谱系；空位假设的拆分裁决
- [[wiki/concepts/open-notebook-architecture|Open Notebook 架构分析]] — 本地仓五问（功能/视频能力/Docker 必然性/技术栈/学习路径）；SurrealDB+worker+Node 三运行时使 compose 成最小分发单元；视频=纯转写无时间戳；ADR 文化范本
- [[wiki/concepts/bilinote-architecture|BiliNote 架构分析]] — 本地仓五问同题异构对照：SQLite+线程池使 Docker 非必需（vs SurrealDB 使其必然）；B站三级取数通道+自维护风控补丁；字幕优先 skip_download 省下载；与 open-notebook 合读=个人视频工具架构课
- [[wiki/concepts/yt-dlp|yt-dlp]] — ⭐189k 公有领域下载器；冒充官方客户端问 InnerTube 私有 API + 签名解密 + PO Token 猫鼠对抗（2026：token 绑视频 ID，插件化现生成）；视频-AI 工具链最上游取料工
- [[wiki/concepts/dsh-agent-loop|dsh agent loop]] — turn/step 状态机、inbox durable 投影（followup/steer/inject 唤醒语义）、pre-step 权威决策、取消恢复与续接三层、scope per-agent 注册
- [[wiki/concepts/dsh-session-log|dsh 会话日志]] — append-only 单一事实源、"model-visible means logged" 运行时不变量、surface 投影与 51 事件词表、compaction 原地替换、崩溃修复、spill 豁免
- [[wiki/concepts/dsh-tool-pipeline|dsh 工具管线]] — 三道 waterfall + monotonic guards + fail-closed 审批 + fs 闸门 + 沙箱世界共享；PTC 代码模式（run_code 子调用携父 token 重入管线）
- [[wiki/concepts/dsh-coordination-subsystems|dsh 协调子系统]] — skill 渐进披露/写入即生效、subagent 变体与 depth 记账、goal/Ralph/jobs 的自治限幅（人工 re-arm、唤醒预算）、feedback 留白与元级自证（.agents/notes 1646 篇）
- [[wiki/concepts/dsh-docs-workflow|dsh 文档工程工作流]] — docs-as-code 十步机器强制流水线：分层 AGENTS.md+字数预算、Agent Note 决策落档、JSDoc 即契约、type-equiv 逐字等价围栏、gen/verify 生成目录、双语三件套哈希公证、run-gates 门禁组合、CI 内嵌、文档站投影、归档冻结
- [[wiki/concepts/tauri|Tauri]] — 借系统 WebView + Rust 后端的桌面应用框架；2.0 插件化/capabilities/移动端；体积优势的根源
- [[wiki/concepts/electron|Electron]] — Chromium+Node+C++ 胶水全家桶；主/渲染双进程 + contextBridge 安全桥；VS Code 与 Obsidian 的地基
- [[wiki/concepts/tauri-vs-electron|Tauri vs Electron]] — 桌面应用「借 vs 带」两范式全维度对比；后端语言被体积约束锁死的分叉树论证；中间形态收敛
- [[wiki/concepts/self-contained-binaries|自包含二进制分发]] — 静态链接（musl/crt-static/rustls）与 glibc 地狱归因；「依赖消灭 vs 搬家」；Node native addon ABI 对照

## 实体（entities/）

- [[wiki/entities/andrej-karpathy|Andrej Karpathy]] — AI 研究者与教育者，LLM Wiki 模式提出者（2026-04）
- [[wiki/entities/obsidian|Obsidian]] — 本地 Markdown 知识库的查看器/编辑器，LLM Wiki 模式中人类一侧的"IDE"；软件闭源、数据本地开放
- [[wiki/entities/claudian|Claudian]] — 把 Claude Agent SDK 嵌入 Obsidian 的桌面插件（本库 agent 侧）；同进程 spawn CLI + 聊天内联批准；"插件里养插件"的生态桥实例
- [[wiki/entities/deepseek-harness|deepseek-harness（dsh）]] — DeepSeek 开源 agent harness；Cordis 插件框架（vendored，上游 MIT），"everything is a plugin"（形态统一；能力边界 30 core/28 seam/1 bundle）；双端插件 + 动态插件（agent 自写自挂）；官方文档已落库
- [[wiki/entities/cc-switch|cc-switch]] — 六款 AI CLI 的供应商切换器（⭐131k 实测）；Tauri 2 实读样本：Rust 58% 五层架构 + axum 本地反向代理子系统

## 综述

- [[wiki/overview|综述（overview）]] — 全库知识图景的演化叙述
