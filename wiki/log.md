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

