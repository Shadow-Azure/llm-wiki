---
type: log
created: 2026-08-23
updated: 2026-08-23
sources: []
---

# 日志

> 追加式时间线，只增不改。条目格式：`## [YYYY-MM-DD] 操作 | 标题`，操作 ∈ init | ingest | query-filed | research | lint | schema。
> 快捷查看：`grep "^## \[" wiki/log.md | tail -5`

## [2026-08-23] init | 仓库初始化

按 Karpathy LLM Wiki 模式初始化本仓库。决策：通用个人知识库；中文正文 + 英文 slug；Obsidian 附件目录设为 raw/assets/。新建：CLAUDE.md（schema）、raw/{assets}、wiki/{sources,concepts,entities}、index.md、log.md、.gitignore、README。

## [2026-08-23] ingest | Karpathy《LLM Wiki》（gist，2026-04-04）

- 新增来源：`raw/2026-04-04-karpathy-llm-wiki.md`（首次 ingest，验证工作流）
- 新建页面：sources/karpathy-llm-wiki、concepts/llm-wiki、concepts/wiki-vs-rag、entities/andrej-karpathy、overview
- 更新：index.md（首次填充）
- 备注：Karpathy 原文 gist 的评论区含社区实践经验，登记为后续候选来源

## [2026-08-23] schema | CLAUDE.md 新增 research 工作流

- 新增 research（研究）操作：先查库 → 收集材料落 raw → 批量 ingest → 区分来源论断与 AI 推理（后者标注"待来源验证"）→ 修订综述 → 同步 index/log → 登记后续线索
- log 操作枚举新增 `research`

## [2026-08-23] query-filed | Obsidian 与笔记工具格局（含开源协议核实）

- 起因：用户 query（Obsidian 优势、与 LLM Wiki 关系、国内外同类开源情况、AI 适配性），两轮对话后回填
- 新建页面：entities/obsidian、concepts/note-tool-landscape（国内外开源/闭源盘点、AGPL/MIT/MPL/EE 协议商用含义、"Obsidian 最适合接 AI"论证——后者标注 AI 推断待验证）
- 更新：concepts/llm-wiki（"本库实例化"补 Obsidian 实体链接）、index.md
- 备注：开源协议当日经 GitHub API 逐仓库核实（思源/AppFlowy AGPL-3.0；AFFiNE 核心 MIT + 后端 EE 混合——"开源"实为 open core）；关键网页来源待剪藏为 raw，已登记于 note-tool-landscape 后续线索

## [2026-08-23] query-filed | 双向链接与关系图谱机制

- 起因：用户两轮 query（双向链接/图谱展开；反向索引作用澄清），用户示意回填
- 新建页面：concepts/bidirectional-links（三层结构祛魅：链接文本→反向索引→图谱；Obsidian 语法；linked/unlinked mentions；断链合法与多重上下文；图谱三种诊断信号；反向索引作用清单；思想谱系 Memex→Zettelkasten→Wiki→Roam→Obsidian；局限部分标注 AI 推断待验证）
- 更新：entities/obsidian（背景补机制详解链接）、concepts/note-tool-landscape（国外开源节补交叉引用）、index.md
- 关键澄清入库：图谱不需要反向索引也能画（边集合=前向链接并集）；"双向"指遍历能力而非双向存储；反向索引主战场是 backlinks 面板而非图谱
- 备注：Obsidian 官方文档待剪藏，已登记于页面"依据来源"

## [2026-08-23] schema | 新增 /backfill 回填命令

- 新建 `.claude/skills/backfill/SKILL.md`：将 query 工作流第 4 步固化为可 `/backfill` 触发的项目级技能（盘点→归类→写入→交叉引用→index/log 同步→汇报，含铁律边界）
- CLAUDE.md query 工作流第 4 步补一行命令指针（应用户要求将回填操作命令化，随仓库版本控制）

## [2026-08-23] schema | /backfill 命令更名为 /llm-wiki-backfill

- `.claude/skills/backfill/` → `.claude/skills/llm-wiki-backfill/`，SKILL.md frontmatter name 与 description 同步更新
- CLAUDE.md query 工作流指针同步更名
- 更名理由：命令名携带库标识，避免与其他项目/工具的通用 "backfill" 语义冲突

## [2026-08-23] schema | main 继承视频文件排除规则（.gitignore）

- 应用户要求，将 `demo/video-learning` 分支的 .gitignore 修改继承到 main：`*.mp4` / `*.mov` / `*.mkv` / `*.webm` / `*.ogv` 不入版本库，视频仅本地保留（元数据 / 字幕 / 截图照常入库）
- 背景：视频学习工作流演示（demo/q5-video-learning.md，Q5 大作文视频）验证了"视频内嵌播放 + AI 内容地图 + 批注讨论"形态；演示页与 6 张关键画面仍留在 demo 分支，未合入 main
- 后续：视频学习工作流是否固化进 CLAUDE.md（视频来源页模板、时间戳锚点约定），待用户确认演示效果后决定

## [2026-08-23] research | Obsidian 插件开发与分发全流程（官方文档落库）

- 触发：用户 research（插件开发看什么文件、构建打包、发布到社区市场、调外部服务接口、动态下载加载资源），应用户要求下载官方文档
- 落库 raw ×15：obsidianmd/obsidian-developer-docs@main 官方开发者文档 markdown 快照（getting started 3 页 + settings、manifest/versions/requestUrl 参考、releasing 4 页、community directory 4 页），每页 frontmatter 记 source URL 与 repo path
- 新建：sources/obsidian-developer-docs（15 页合集摘要）、concepts/obsidian-plugin-development、concepts/obsidian-plugin-distribution、concepts/obsidian-plugin-networking
- 更新：entities/obsidian（新增"插件机制"节）、overview（规模/主题/开放问题）、index.md
- 关键入库事实：现行提交通道是 community.obsidian.md 网站 + 自动审核四节（旧博客常写的 obsidian-releases PR 流程已过时）；requestUrl 官方免 CORS；政策明文禁自更新/自装依赖，网络使用须 README 披露；可执行代码动态加载为灰色地带（页面已标注 AI 推断）
- 备注：上一轮"三方插件机制对比"（Obsidian vs deepseek-harness vs VSCode，含 Claudian 实例）尚未回填，待用户示意

## [2026-08-23] query-filed | 插件机制三方对比 + 本地视频播放器可行性

- 覆盖两轮 query：①Obsidian（[[wiki/entities/claudian|Claudian]] 实例）vs [[wiki/entities/deepseek-harness|deepseek-harness]] vs VSCode 的插件机制对比；②本地视频播放器插件可行性分析
- 新建：concepts/plugin-mechanism-comparison（12 维度对比表 + 五条洞察）、concepts/video-player-plugin-feasibility（L0–L4 功能阶梯 + 合规检查 + 竞品）、entities/claudian、entities/deepseek-harness
- 更新：entities/obsidian（插件机制节与相关页面补对比/Claudian 链接）、concepts/obsidian-plugin-development（关联节）、overview（"正在形成的主题"落地、开放问题改链）、index.md
- 溯源约定：对比页 Obsidian 列有 raw 支撑；dsh / Claudian 列基于库外代码仓实读（路径已注明）；VSCode 列基于官方文档（待剪藏）；五条洞察整体标注 AI 综合
- 待剪藏登记：VSCode 官方文档三页（extension host / activation events / contribution points）、Obsidian help file-formats、HEVC 论坛帖、Media Extended 文档、dsh 的 architecture.md 与 cordis-primer.md、claudian README
- lint 线索：claudian / deepseek-harness 两实体页 sources 为空（库外代码仓），是下次 lint 的优先补源对象

## [2026-08-23] research | deepseek-harness 插件开发与分发全流程（官方文档落库）

- 触发：用户 research（dsh 插件开发看什么文件、打包构建、发布到市场、外部接口调用、动态下载加载、视频播放插件可行性），应用户要求下载官方文档
- 落库 raw ×32：deepseek-ai/deepseek-harness 官方 docs 中文版快照（commit b150a551，2026-08-21 提交；自本地 clone 剪藏）——user/develop 全部 9 页、cordis-tutorial 8 页、cookbook 5 页、subsystems 5 页、architecture/cordis-primer/glossary、CLI 参考、README
- 新建：sources/dsh-official-docs（32 页合集摘要）、concepts/dsh-plugin-development、concepts/dsh-plugin-distribution、concepts/dsh-plugin-networking、concepts/dsh-video-plugin-feasibility
- 更新：entities/deepseek-harness（补 sources + 双端/动态插件/分发要点，**清偿上一条 lint 债**）、concepts/plugin-mechanism-comparison（"能否改 UI" dsh 行修正：web profile 有正式扩展点；溯源说明改为已剪藏）、overview、index.md
- 关键入库事实：分发单位是组合包（npm 包 + dsh.bundle manifest），安装 dsh plugin --profile add 四来源（npm/tarball/git/本地），bundle 变更需重启、patch 层热重载；**官方无插件市场**（docs 无 marketplace 一词），README 仅约定 dsh-plugin topic，社区市场（dsh.deepseek404.com 等）与应用内市场插件均为第三方；外部服务调用同进程全权 + 凭据 seam（env 引用按操作解析）；动态加载三层——HMR / dsh.client lazy bundle / dynamicCordisRunner（agent 自写自挂插件，带审批闸门）；视频播放插件 Web UI 完全可行（conversation node 渲染 React + webServer 自建路由），工具卡片为封闭枚举不可行，附件 v1 仅图片
- AI 推断已标注待验证项：webServer Range 流式细节、CSP/外部源约束、Electron IPC 下路由行为、Cordis-Koishi 渊源
- 后续线索登记：社区市场与 discussion #961 实战帖待剪藏；claudian 实体页 sources 仍空（下次 lint）

## [2026-08-23] query-filed | dsh 插件前端接入（slots 系统与浏览器半）

- 起因：用户 query（插件如何接入前端界面）+ 一轮前端新手向简化解释（"后厨/前厅"直觉模型已并入新页定义句）
- 新建：concepts/dsh-frontend-integration（两半架构与自动发现、五接入点、slots 五纪律、~20 具名插槽清单、打包三坑、视频插件接线草图）
- 更新（交叉补链）：concepts/dsh-plugin-development（关联节 + 工具卡片 bullet）、concepts/dsh-plugin-networking（关联节）、concepts/dsh-video-plugin-feasibility（建议路径）、concepts/plugin-mechanism-comparison（溯源说明）、sources/dsh-official-docs（后续线索补 AGENTS.md/web-styling）、index.md
- 溯源：settings 卡片配对/conversation node/client modules/webServer 有 raw 支撑；**slots 纪律与插槽清单来自库外代码仓实读**（`packages/client/AGENTS.md` + 源码 grep），按 backfill 边界不写 raw/，已在页面标注待剪藏并登记 sources 页后续线索
- 待剪藏（优先）：packages/client/AGENTS.md、docs/web-styling.zh.md、slot-type-chain 与 gui-web-client-architecture 两份 agent note

## [2026-08-23] query-filed | Obsidian 插件 UI 接入指南（前端新手向）

- 起因：用户 query（插件在前端界面上怎么接入，前端不熟，要简单解释），答复末尾已提议并入 development 页，用户以 /llm-wiki-backfill 确认
- 更新：concepts/obsidian-plugin-development 新增"UI 接入（前端新手向）"一节——心智模型（界面即网页、插槽即注册函数）、六插槽表（ribbon/command/notice/settingTab/statusBar/registerView）、最少前端知识 4 概念（DOM/createEl/CSS 类/事件回调）、视频面板 ItemView 最小示例（即播放器 L1 骨架）、DevTools 技巧、dsh slots 对照链接
- 无新建页（内容并入既有页面，符合"单薄不建新页"边界）；index.md 同步该页摘要
- 溯源：插槽表与 ItemView 示例来自官方 User interface 系列文档（未剪藏，页内已标注待复核）；createEl/DevTools/设置页部分有 raw 支撑
- 与 dsh-frontend-integration 成对照：Obsidian 命令式注册插槽 vs dsh 声明式挂载 slots（两页已互链）
- 待剪藏登记：官方 User interface 系列（Commands/Views/Ribbon/Modals/Status bar/HTML elements 等）

## [2026-08-25] query-filed | dsh 插件化能力边界与 Cordis vendoring

- 起因：用户四轮 query（插件化能力上限 / 通俗机制+Web 组装 / systemPrompt 与 webServer 详解 / 打点边界、脊柱位置、"实现皆可换"辨析），代码仓实读 `/Users/zn-ice/2026/deepseek-harness`；用户选定精简版回填方案（/llm-wiki-backfill）
- 新建：concepts/dsh-plugin-capability-boundaries — 30 core / 28 seam / 1 bundle 角色普查（capability-seams 总表）、可换/不可换清单、core 两种动机（systemPrompt 公告栏 / webServer 大门剖析）、"能拔≠能用"（inject 永不满足→fiber 永远 pending）、脊柱两层（cordis 机制层 + dsh 自有领域层）+ 宿主引导、打点辨析（sessionTelemetry=seam 默认 DISABLED 可整行不装+依赖反转订阅事件流 vs tokenMeter=core 进程内计量）
- 新建：concepts/cordis-vendoring — vendor/ 9 包清单（7/9 取自 deepseek-harness fork，AI 推断待验证）、@deepseek-ai rescope 防 squat、18 处本地修改（fiber 加固/事务化调和/懒配置反向移植 PR#41）、上游 cordiverse/cordis **MIT**（Shigma）三重证据与商用含义；**证实 Koishi 渊源推断**（原 AI 推断已在对比页标记已验证）
- 更新：concepts/dsh-frontend-integration 并入"官方 Web 的四层装配"（空根→dsh-base 78 行→dsh-web-app 445 行禁用 agent 平面改 preset 接管→浏览器 roster/__DSH_BOOT__ 领养→用户 patch）
- 精化标记（铁律 7，非事实矛盾，精度提升）：entities/deepseek-harness 与 concepts/plugin-mechanism-comparison 的"任何行（含 agent loop）可被 patch 替换/无特权内核"两处补 † 精化注记——"可 patch"≠"有得换、换了能用"
- 同步：index +2 行、改 2 行摘要（前端接入/实体页）、bump updated；sources/dsh-official-docs 后续线索增补待剪藏清单
- 待剪藏登记：vendor/README.md、THIRD_PARTY_NOTICES.md、capability-seams.zh.md（优先级提高）、system-prompt / session-telemetry 包 README、profile-boot.ts + 两份 bundle patch


## [2026-08-30] research | dsh 源码仓运行时架构（agent loop / session log / tool pipeline / 协调子系统）

- 起因：用户要求分析本地代码仓 `/Users/zn-ice/2026/deepseek-harness`（v0.1.2-alpha.1 @ cd5ef814，~50 packages、4700+ TS 文件）并生成知识文档 + 给出学习路径；先查库确认已有 32 页官方文档剪藏覆盖"插件生态面"，本次补"运行时架构面"
- 落库 raw（9 份源码仓架构文档剪藏，附 clip frontmatter，正文未动）：agent-lifecycle、tool-execution-pipeline、event-producer-consumer、capability-seams（销 2026-08-25 待剪账）、subsystems/{core,session,compaction,skills,tools}
- 新建 wiki：sources/dsh-source-runtime（来源页：四域综合 + 生成目录未剪清单 + 与 self-opt 的连接）；concepts/dsh-agent-loop（turn/step 状态机、inbox durable 投影与三种唤醒语义、pre-step 权威决策、request-error retry、continuation 三层、scope）；concepts/dsh-session-log（append-only 单一事实源、model-visible means logged 不变量、surface 投影、compaction 原地替换 + KV cache 前缀技巧、崩溃修复合成 closer、spill 豁免、invariant companion 体系）；concepts/dsh-tool-pipeline（三道 waterfall 职责表、monotonic guards 单调性、审批四层 fail-closed、fs intent 闸门、沙箱世界共享、PTC 完整图景）；concepts/dsh-coordination-subsystems（skill 渐进披露/写入即生效、subagent spawn/fork/continuable 与 depth 记账、goal armed/disarmed 与人工 re-arm、Ralph、jobs 唤醒预算、feedback 留白、.agents/notes 元级自证）
- 更新：entities/deepseek-harness（新增"运行时架构要点"节 + sources +5）；sources/dsh-official-docs（capability-seams 销账 + 双文档面互链）；overview（"dsh 双文档面"与"self-opt 汇流"两条主题）；index（sources +1、concepts +4）
- 知识性质标注：四概念页中"AI 综合自实读"的行号级论断已注明路径与 commit，行号随上游漂移风险已声明；与 self-opt 图谱的对照判断标为 AI 推断待专页验证
- 后续线索（登记于 sources/dsh-source-runtime）：config-catalog / module-graph / persistence-catalog / tool-catalog 四份生成目录、45 份子系统文档中未剪的 40 份、postmortem/ 与 cordis-api/、dsh×self-opt 对照专页

## [2026-09-05] research | AI agent + 视频结合体格局（deep-research）

- 起因：用户 query"开源工程和业界产品里，有没有 AI agent + 视频播放的结合体（预分析视频→提问→帮干活）"；先查库定位 [[wiki/concepts/video-player-plugin-feasibility]] / [[wiki/concepts/dsh-video-plugin-feasibility]] 的竞品假设，研究站在其上验证/推翻
- 执行：deep-research workflow（5 路搜索 → 45 源抓取 → 225 claims）；workflow 两次卡死于 web-reader MCP 半挂状态（fetch barrier 上 agent 工具调用 25 分钟无返回），改策略停跑收割 journal 已完成结果，GitHub 类来源改用 zread 抓 README 全文补强
- 落库 raw（6 篇）：hkuds-videorag-vimo / omagent-video-understanding / hkuds-videoagent（三篇 README 全文剪藏）；player-embedded-ai-snapshot（7 来源）、video-understanding-api-products（7 来源）、video-agent-academic-landscape（6 来源）三篇多源快照合集；media-extended-v4-no-ai（库内竞品一手证据）
- 新建 wiki：sources/ 6 页（对应 6 raw）；concepts/ai-video-assistant-landscape（核心综合：五层格局表、架构五模式、agent 动作三档谱系、基准硬依据、空位假设裁决）
- 关键裁决（AI 综合，多源交叉）：①"播放器内嵌 AI 是空位"作为泛化命题被推翻——YouTube/B站原生 + LunaNotes 等扩展已拥挤；②收窄后仍成立——"本地视频库 + 个人知识库 + 播放器内嵌 AI"无人占据（Media Extended v4.0 官方零 AI + AI 是其转闭源动因）；③新增空位：消费级播放器 AI 全部 transcript-based，视觉理解进个人笔记缺位；④时间戳=检索元数据已在 API 层商品化（NVIDIA/听悟/VideoDB），MCP 成"视频理解即工具"标配
- 更新：video-player-plugin-feasibility（竞品小节改写 + 假设修正 + frontmatter sources）；dsh-video-plugin-feasibility（新增外部格局参照节：MCP 对接通道 + 图驱动工作流同构）；index（sources +6、concepts +1 改 2）；overview（视频主题升级 + 开放问题）
- 知识性质标注：厂商自报数字（tl;dv 200 万/BibiGPT 100 万用户、TwelveLabs"Video-MME 第一"）、hoverNotes（厂商自述）、Vidizmo 章节管线（厂商描述）均已在页面标注；Video-MME 关键数字经库内独立全文抓取核对；YouTube Gemini Ask 可用范围（Premium/地区）未核实
- 工程备忘：web-reader MCP 对个别域名（twelvelabs.io、feishu.cn）挂起且批量调用时半失效，后续 research 优先 zread（GitHub）/WebFetch 兜底

## [2026-09-06] research | 开源在线视频分析项目盘点（扩展态，B站方向聚焦）

- 起因：用户追问"浏览器扩展做在线视频分析的高星开源项目，例如 B站"——上轮（2026-09-05）格局研究的扩展层以闭源商业为主，本次补开源一侧；规模可控故未走 workflow，直接搜索 + GitHub API 实测
- 落库 raw（1 篇）：oss-video-extension-landscape（10 repo star/license/pushed 实测表 + BiliNote README 关键事实节录 + 4 条未深挖线索）
- 新建 wiki：sources/oss-video-extension-landscape
- 核心发现：①"高星+扩展+视频 AI"三条件交集稀疏——星数集中在"链接→笔记"应用形态（BiliNote 7,235⭐ / BibiGPT-v1 6,204⭐），纯扩展最高 biliscope 827⭐（B站）/ chatGPTBox 10,757⭐（视频总结非核心）；②BiliNote（MIT，v2.4.0，活跃）为开源标杆：本地 FastAPI + MV3 插件 + Tauri 桌面端 + Docker，"B站字幕优先"（插件带登录态 cookie 浏览器内抓字幕→player API→yt-dlp→本地 Whisper 四级兜底），v2.0.0 起 RAG 问答+Function Calling——chat with video 已下探进开源个人工具；③开源与闭源差距在视觉理解与跨视频聚合，不在 transcript 问答
- 更新：concepts/ai-video-assistant-landscape（格局表扩展行补开源项 + 新增"开源侧补充"节 + 依据来源）；index（sources +1）；overview（规模数字）
- 后续线索登记：Video to Text Assistant（MIT 扩展，Obsidian 导出）商店页待剪藏；Bilibili-Evolved 插件市场 AI 组件未核实

## [2026-09-06] query-filed | 回填盘点：视频两轮研究 + wiki-vs-rag 反向补链

- 盘点结论：本会话两轮 query（AI agent + 视频格局 / 开源扩展盘点）的价值内容已分别在 2026-09-05、2026-09-06 的 research 条目中完整落库（7 raw / 7 sources / 1 新概念页 / 2 可行性页更新），无剩余需建页内容
- 实质变更（交叉引用检查发现的缺口）：concepts/wiki-vs-rag 补反向链接——"边界与张力"节新增"视频模态的同一对照"（长上下文直塞 vs 预索引 = 查询时拼装 vs 摄入时编译的视频域重现，Video-MME 数据佐证；标注 AI 类比综合、对照数据可溯源），frontmatter sources + updated 同步
- 未建页决策：web-reader MCP 故障应对（已记 log 工程备忘，单薄）；BiliNote/TwelveLabs/VideoDB 实体页（内容已被 sources/concepts 页充分覆盖，无持续跟踪信号，按"单薄不建页"原则跳过）

## [2026-09-06] research | open-notebook 本地仓架构分析（五问）

- 起因：用户要求分析本地仓 `/Users/zn-ice/2026/open-notebook`（lfnovo/open-notebook ⭐38.3k，MIT，v1.14.0 @ 2d2df8a）：功能面/能否处理视频/为何依赖 Docker/语言与架构/学习入手点；与 2026-08-30 dsh 源码实读同型任务
- 实读路径：根目录 + README + pyproject + docker-compose/supervisord → graphs/source.py（摄取图）→ commands/（任务定义）→ docs/7-DEVELOPMENT/architecture.md（891 行）+ content-processing.md + ADR-004/007 → GitHub API（star）+ content-core 定位（作者自有提取库，带 MCP server）
- 落库 raw（1 篇选辑）：2026-09-06-open-notebook-architecture（ADR-004/007 全文剪藏 + source.py 关键常量与软失败哨兵 + supervisord/compose 配置注释 + content-processing.md 要点）
- 新建 wiki：sources/open-notebook-architecture + concepts/open-notebook-architecture（五问结构）
- 关键结论：①视频=transcript-first 纯文本化（YouTube 字幕优先 11 语言含 zh-CN/TW，无字幕 STT 兜底，无时间戳/视觉/播放器）——AI 视频助手格局中"transcript-first 个人 KM 实例"，比 BiliNote 更彻底；②Docker 必然性四层：SurrealDB 独立服务（文档+图+向量+全文合一，ADR-001）→ 任务队列也建在 DB 上（surreal-commands，零新增基础设施，ADR-004）→ 单容器三/四进程三运行时（FastAPI/worker/Next.js Node [+SurrealDB]）→ 重运行时 opt-in 首启装+卷缓存（ADR-007）——compose 是架构事实推论出的最小分发单元；③语言=Python 3.11(uv)+TS(Next.js 16/React 19)，作者自有四件套 esperanto/content-core/podcast-creator/surreal-commands；④学习路径六步：跑起来→读 ADR（8 ADR+2 PDR 含被否方案，范本级）→摄取链→问答链（ask vs chat + context_builder token 二分截断）→esperanto 抽象层→AGENTS.md meta 学习（与 LLM Wiki 同构）
- 发现的文档-代码矛盾（登记入概念页）：architecture.md 写 Next.js 15（实为 ^16）、source 工作流描述含 topics 节点（当前源码无）、providers 数 17 vs 18+ vs 20 行矩阵三处不一——以源码/README 矩阵为准
- 同步：index（sources +1、concepts +1）；overview（规模数字）

## [2026-09-06] query-filed | open-notebook 播放能力与非 YouTube 平台验证

- 起因：用户追问 open-notebook 能否播放本地视频、能否处理 B站/爱奇艺
- 验证（源码 + content-core README 一手证据）：①前端全库无 <video>/player/hls 组件——不能播放任何媒体，SourceTypeStep 的 .mp4 等只是上传 accept 过滤；②grep 全仓无 bilibili/iqiyi 逻辑（TIKTOKEN 命中为 token 计数器误名误报）；③content-core Supported Formats 表：视频平台专门支持仅 YouTube（唯一平台级配置 CCORE_YOUTUBE_LANGUAGES），本地 MP4/AVI/MOV/MKV=音频转写
- 结论：B站/爱奇艺 URL 走通用网页提取链只得到页面文字非视频内容；结构性原因=open-notebook 后端提取拿不到用户登录态，BiliNote 的浏览器内 cookie 抓字幕策略因此成立；变通路径三条（yt-dlp 下载→本地上传 / 扩展导字幕→文本导入 / 给 content-core 提 processor PR）已登记，标注 AI 推断待实测
- 落库：raw/2026-09-06-content-core-formats（content-core README 关键节选）；concepts/open-notebook-architecture 增补"二·一 播放与其它平台"节 + sources 页 sources/后续线索更新

## [2026-09-06] query-filed | yt-dlp 是什么及其 YouTube 支持机制

- 起因：用户三连问（yt-dlp 是什么 / 为何支持 YouTube / 怎么支持的）
- 核实：GitHub API（yt-dlp ⭐189,227 Unlicense vs youtube-dl ⭐141k）+ WebSearch PO Token 2026 现状（token 绑定 video ID、手动提取已死、yt-dlp-get-pot 插件 + BGutil/无头浏览器现生成）
- 新建：concepts/yt-dlp——身份（youtube-dl 2020 DMCA 后的接管 fork）+ 三层"为什么"（无官方下载 API→逆向、ToS vs 互操作性灰区、猫鼠对抗即核心工程）+ 五步流水线（冒充客户端问 InnerTube → 签名/n 参数解密 → PO Token 插件化 → DASH 下载 ffmpeg 合并 → nightly 发布机器）+ 工具链位置（只取料不做理解、字幕直下比转写便宜、B站 extractor 需 SESSDATA）+ 双护城河对照（ASData 护城河 vs 反下载护城河）
- 知识性质：PO Token 现状有 wiki 直接出处；InnerTube/签名机制为 AI 综合自社区资料（页内标注待剪藏）；PO Token Guide 登记待剪藏
- 同步：index（concepts +1）

## [2026-09-06] query-filed | open-notebook 处理 YouTube 是否用 yt-dlp（源码级确证：否）

- 起因：用户问 open-notebook 处理 YouTube 是否用 yt-dlp 抓取
- 验证方法：GitHub API 拉取 lfnovo/content-core 源码（extraction.py / processors/url/youtube.py / processors/media/{video,audio}.py）实读
- 确证结论：①content-core 全库零提及 yt-dlp/pytube——YouTube 分支用 **youtube-transcript-api** 库（四级字幕尝试：手动→自动生成→任意→直接 fetch，偏好语言可配），无字幕抛 NoTranscriptFound；②extraction.py 按 hostname 分发，无音频下载逻辑；③media 管线只处理本地文件（ffprobe 选最优音频流→ffmpeg 抽 MP3→15 分钟分段→逐段 STT，含 .opus→.ogg 扩展名别名处理）
- 矛盾标记（铁律 7）：open-notebook 报错文案"Try configuring a Speech-to-Text model to transcribe the audio"有误导——STT 只作用于上传的本地文件，对 YouTube URL 不会下载音频转写；已在 concepts/open-notebook-architecture 修正并标注
- 概念页更新：open-notebook-architecture（"二"节第 2/3 条重写为源码级确证版本）；yt-dlp（新增反例：并非所有视频工具都用它——取数策略谱系：纯字幕 API open-notebook 轻但脆 ↔ 字幕+下载兜底 BiliNote 重但全）
- 未落库说明：本轮证据已在回答中引用 GitHub 源码（fetch 时未剪藏全文）；youtube-transcript-api 机制值得后续单独剪藏

## [2026-09-06] query-filed | 回填盘点：近四轮已全部落库，仅补 yt-dlp 反向链接

- 盘点结论：本会话近四轮问答（open-notebook 五问 / 播放与平台追问 / yt-dlp 三连问 / YouTube 取数机制确证）均在回答时同步落库，无未回填的分析内容、无新建页需求
- 实质变更（lint 清单"缺失交叉引用"项）：concepts/ai-video-assistant-landscape、concepts/open-notebook-architecture、sources/oss-video-extension-landscape 三处提及 yt-dlp 的兜底链/变通路径补 [[wiki/concepts/yt-dlp]] 反向链接（此前该概念页仅被 index 引用，无 wiki 页面入链——本会成为其孤儿页风险）
- 未建页决策：youtube-transcript-api（信息量不足以撑页，留 open-notebook-architecture 后续线索）；"Video to Text Assistant"扩展与 Bilibili-Evolved AI 组件（待剪藏项，非分析产出）
- lint 提示：raw 67 篇、lint 持续 overdue；本会话累计待剪藏：PO Token Guide、youtube-transcript-api、Video to Text Assistant 商店页、Bilibili-Evolved 插件市场、content-core 源码全文（本轮 fetch 未存档）

## [2026-09-06] research | BiliNote 本地仓架构分析（五问，与 open-notebook 同题异构对照）

- 起因：用户要求分析本地仓 /Users/zn-ice/2026/BiliNote（⭐7.2k，v2.4.5 @ 90940fd）五问：功能/视频处理/Docker 依赖/语言架构/学习路径；库内已有 2026-09-05 README 快照，本次实读深化
- 实读路径：根 CLAUDE.md（7KB 高质量 agent 文档，架构自述）→ docker-compose.yml → downloaders/（base/bilibili_downloader/bilibili_subtitle/bilibili_dm_patch/youtube_subtitle）→ services/note.py 编排 + task_serial_executor + vector_store
- 落库 raw（1 篇选辑）：2026-09-06-bilinote-architecture（CLAUDE.md 架构节 + bilibili_subtitle.py 七步注释全文 + bilibili_dm_patch.py 412 风控考据全文 + YDL_RETRY_OPTS yt-dlp Python API 坑 + docker-compose 要点）
- 新建 wiki：sources/bilinote-architecture + concepts/bilinote-architecture（五问 + 与 open-notebook 对照贯穿）
- 关键结论：①视频处理=字幕优先是编排层决策（NoteGenerator 先 download_subtitles，有字幕且无截图/视频理解需求则 skip_download 完全不下载）；B站三级取数通道（插件浏览器内抓字幕→后端 player/wbi/v2 直拉（AI 字幕需 SESSDATA）→yt-dlp 兜底）；②Docker 非架构必需（SQLite 嵌入式+进程内线程池+单进程可跑全功能；Tauri 桌面版无 Docker）——compose 只是 Web 分发便利（ffmpeg 打包/whisper 卷/nginx 门控/GPU 变体/国内镜像），与 open-notebook 的"架构必然"形成数据库选型决定部署形态的对照；③发现名实不符三连（task_serial_executor 实为 ThreadPoolExecutor、celery 死依赖零引用、contentScripts placeholder 描述滞后）④bilibili_dm_patch 是风控对抗鲜活样本（2026-06 B站 playurl 要求 dm_img_* 指纹参数否则 412，上游 yt-dlp 不发，自注入占位值）
- 同步：index（sources +1、concepts +1）；overview 规模数字
- 后续线索：video_understanding 实现深度、request_chunker/checkpoint 机制

## [2026-09-06] query-filed | BiliNote 进程模型与"为何不主推开箱即用"

- 起因：用户三问（画架构图 / 后端是否一个服务进程 / 为何 README 只讲 Docker+源码、不能打包可执行程序开箱即用）
- 验证：backend/main.py 尾部 uvicorn.run(:8483) 确认单服务进程；build.sh PyInstaller 打包 main.py → BiliNoteBackend 二进制（hidden-import uvicorn/fastapi + 打入 .env）；tauri.conf.json externalBin sidecar + _internal resources——桌面可执行程序已存在
- 回答要点：①进程模型图（三客户端→单后端服务进程，任务队列/SQLite/chromadb 全进程内，外部依赖 ffmpeg 子进程/whisper 运行时下载/云 LLM）；②前提修正：桌面版已打包，问题是"为何不主推+离开箱即用差多远"——whisper 模型必须运行时下载、ffmpeg 是未打包的外部进程依赖（最大破口）、首启配置不可免、PyInstaller×ML 栈脆弱（中文路径/杀软/平台矩阵）、三端统一架构使服务进程成为最大公约数、目标人群自托管优先+桌面双平台签名公证成本
- 更新：concepts/bilinote-architecture 新增"五·一 进程模型与分发形态"节（含 ASCII 图）

## [2026-09-06] query-filed | BiliNote 管线基础组件释疑（whisper/ffmpeg/faster-whisper/ctranslate2/chromadb）

- 起因：用户问 whisper 模型、ffmpeg、faster-whisper(ctranslate2)、chromadb 各是做什么的
- 回答要点：管线位置图（下载→抽音频→转写→LLM，chromadb 支撑 RAG 侧）；whisper=OpenAI 开源 ASR 模型（耳朵）；ffmpeg=多媒体命令行底座（剪刀：探测/抽流/切段/抓帧）；三层关系 whisper(权重)→faster-whisper(SYSTRAN 重实现，4x 速省半内存)→ctranslate2(C++ 推理引擎，INT8 量化)；chromadb=嵌入式向量库（如 SQLite 跑在应用进程内，BiliNote 每任务一 collection，Function Calling 检索）
- 落库决策：通用基础知识，单薄不建新页——并入 concepts/bilinote-architecture 新增"附：管线依赖速览"节，后续页面可直接引用该节不重复解释

## [2026-09-06] query-filed | 回填盘点：BiliNote 三轮已落库，补概念页反向链接

- 盘点结论：上次 backfill 后三轮（BiliNote 五问 research / 进程模型追问 / 基础组件释疑）均在发生时同步落库（1 raw + 1 sources + 1 concepts + 两轮概念页增补），无未回填内容、无新建页需求
- 实质变更（孤儿页防御，与上次 backfill 同型缺口）：concepts/bilinote-architecture 此前仅被 index/log 引用——已在四处提及 BiliNote 的页面补 [[wiki/concepts/bilinote-architecture]] 入链：ai-video-assistant-landscape（开源标杆句）、open-notebook-architecture（结构性格局句，原链接指向 sources 快照改指概念实读页）、yt-dlp（取数策略谱系句）、sources/oss-video-extension-landscape（标杆句）
- 簿记观察：本会话两次 backfill 的实质工作都是"孤儿页防御"——research 落库时新建页当日只有 index 入链，需次日回填时补内容页反向链接；后续新建概念页应在当次 research 同步时就补齐既有页面的反向链接，减少回填负担

## [2026-09-06] query-filed | dsh 编程语言与整体架构图

- 起因：用户要求分析 /Users/zn-ice/2026/deepseek-harness 的语言/架构并画架构图
- 库内核对：本地仓仍 @ cd5ef814 / v0.1.2-alpha.1，与 2026-08-30 实读版本完全一致——库内五概念页+实体页结论全部有效，未重复实读
- 本次新增事实（语言与工程栈）：TypeScript 4728 文件 + native/landlock-run 用 C（Linux Landlock 沙箱启动器）+ ~20 .py 辅助；pnpm 11.7 monorepo 五 workspaces（vendor/*、packages/*/* 两级分组、native/*、apps/{cli,web}、website）；包发布 lib/index.js + lib/types d.ts（根统一构建）
- 更新：entities/deepseek-harness 新增"语言与工程栈 + 架构图"节（ASCII 整体架构图：apps 产品面 → profile/bundle/patch 组合层 → Cordis 内核 → 运行时四支柱 + 侧翼集成包；一句话读图："循环写日志、日志投影一切、工具穿管线、策略横插监听、方框皆可 patch"）
- 未新建页决策：图与语言栈并入实体页（架构细节已有五概念页承载）；log 记 query-filed

## [2026-09-06] query-filed | 回填盘点：dsh 架构图轮已落库，补一处实体页链接

- 盘点结论：上次 backfill 后仅一轮（dsh 编程语言与架构图），已当场 query-filed（entities/deepseek-harness 增补"语言与工程栈 + 架构图"节）——无未回填内容
- 实质变更：concepts/open-notebook-architecture "与其他概念的关联"节中 "Claudian/dsh" 补 [[wiki/entities/deepseek-harness]] 链接（交叉引用检查发现）；bilinote-architecture 页的 "deepseek" 命中为 LLM 供应商名（DeepSeek 模型），非 harness 引用，不链接
- 无其他缺口：entity 页 frontmatter updated 已为 2026-09-06；index 无需变更

## [2026-09-06] lint | 结构体检全绿 + overview 滞后修订

- 触发：overview"下一步"自登记的 overdue lint（65→69 raw，远超每 ~10 来源节律）；用户确认只执行 overview 修订
- 检查结果：69 raw 中 68 已被 14 个来源页 frontmatter 覆盖（合集页计数与 index 声明吻合）；唯一未 ingest 来源 = raw/2026-08-26 self-improving.md（AI Harness 自进化综述，315 行）；42 页 frontmatter 全合规、引用 raw 路径 100% 存在；零孤儿页、零真实断链（初检"断链"均为 bidirectional-links 页内反引号语法示例）；index/log 同步到位；无新增未标记矛盾（深度限于结构性检查+抽样，全文矛盾复查留待 ingest self-improving 时顺带）
- 裁定（用户确认）：self-opt/ 维持"用户自管检索库"例外定位——wiki 直链 raw/self-opt/* 可接受，不建 source 页
- 实质变更：仅 wiki/overview.md——frontmatter updated 2026-09-05→2026-09-06、sources 补 4 个 09-06 raw 路径；规模行三处数字校正（41→42 页 / concepts 23→24 / 68→69 raw，消除页内"65 raw"自相矛盾）；AI 视频助手主题段补录 09-06 深化（open-notebook/bilinote 合读、yt-dlp 取料工与取数策略谱系）；self-opt 汇流段记录例外裁定；"下一步"重写（lint 收账 → ingest self-improving 列首位）并附本次 lint 记录
- 未执行（等用户后续指令）：ingest self-improving；dsh×self-opt 对照专页
