---
type: concept
created: 2026-08-23
updated: 2026-09-06
sources:
  - "raw/2026-09-06-obsidian-oss-alternatives-landscape.md"
---

# 笔记工具格局

**一句话定义**：个人知识工具市场沿"本地优先 vs 云端 SaaS"分化——开源产品几乎全部站本地优先阵营，闭源产品全部是云端阵营；[[wiki/entities/obsidian|Obsidian]] 例外地"软件闭源、数据本地开放"。且"高 star""开源协议""可二次开发"三个维度并不重合，需分轴评估（2026-09-06 实测，见下）。

> 本页 2026-08-23 由 query 回填建页；2026-09-06 research 补强：22 仓 star/协议/活跃度实测 + 协议原文核验 + 二次开发通道盘点，依据 [[wiki/sources/obsidian-oss-alternatives-landscape|类 Obsidian 开源项目格局实测]]。star 数为 2026-09-06 GitHub API 实测值，会漂移。

## 国内：开源三强 vs 云端闭源阵营

| 产品 | 开源 | ⭐（2026-09-06） | 协议（GitHub 核实） | 形态 |
|---|---|---|---|---|
| 思源笔记 | ✅ | 46.2k | **AGPL-3.0**（整仓） | 最接近 Obsidian 的对标：本地优先、块级引用 + 双链 + 大纲，端到端加密同步，Docker 自托管。注意原生数据为自有 `.sy` 格式，Markdown 靠导出；部分功能付费会员 |
| AppFlowy | ✅ | 76.3k | **AGPL-3.0** | Notion 替代，Rust + Flutter，AI 协作工作台；⚠️ 自托管后端 AppFlowy-Cloud 已于 2026-09-01 归档，替代结构未明，社区有"self-host 不真开放"批评 |
| AFFiNE | 部分 | 72.2k | **混合**：核心/前端 **MIT**；后端 server（`packages/backend/`）为**企业版协议（EE，生产环境需付费订阅）**；编辑框架 BlockSuite 单独仓库（6.0k⭐）为 **MPL-2.0** | 文档 + 白板 + 数据库；自托管版偏早期 |

**AFFiNE 的"开源"是 open core**：前端随便用，后端源码可见但上生产要订阅，且修改版权归 AFFiNE 保留——中文社区常直接称其"开源"，引用时需区分（Grafana / GitLab 同款模式）。

闭源阵营：语雀（阿里，云端知识库；资源向钉钉文档倾斜为 V2EX 社区观察，待可靠来源）、wolai 我来、FlowUs 息流（均为 Notion-like）、flomo（卡片轻记，重"记"不重"理"）、印象笔记、有道云笔记、幕布、Heptabase（台湾团队，视觉卡片）。

## 国外开源（star/协议/活跃度实测 2026-09-06）

**本地优先 PKM（形态最像 Obsidian）：**

| 产品 | ⭐ | 协议 | 状态 |
|---|---|---|---|
| Logseq | 44.8k | AGPL-3.0 | 活跃，但 **DB 版重写动荡期**：至 2026-05 无稳定安装版时间表（社区已等约两年），文件版有整文件重写/重命名级联的性能瓶颈 |
| Trilium（TriliumNext/Trilium） | 37.7k | AGPL-3.0 | 活跃。**社区接管活标本**：zadam 2024-01 维护模式 → 社区 fork TriliumNext → 原仓库转移给社区并拿回 "Trilium Notes" 原名 |
| Foam | 17.4k | MIT | 活跃；VS Code 内嵌 Markdown 双链 |
| TiddlyWiki5 | 8.6k | BSD 三条款 | 活跃；单文件 HTML，2004 年至今 |
| Dendron | 7.5k | Apache-2.0 | **事实停摆**（最后推送 2025-11-13） |
| SilverBullet | 6.0k | MIT | 活跃；自托管 Markdown，主打可扩展 |

**剪藏/同步笔记（Evernote 形态）：** Joplin 56.3k（**AGPL-3.0-or-later**，目录级例外 + JOPLIN SAS 商标；官方插件 API + CLI——"Joplin 是 MIT"为过时印象）、Notesnook 14.5k（GPL-3.0）、Standard Notes 6.6k（AGPL-3.0；2024-11 起 Proton 旗下，保持独立产品但社区有放缓担忧）。

**Notion 形态：** AppFlowy 76.3k（见上表）、AFFiNE 72.2k（见上表）、Anytype 客户端 8.8k（⚠️ **Any Source Available License 1.0，非开源**：仅限非商用或 Allowed Networks 内商用，见"已知矛盾"）。

**自托管 web wiki / 团队向：** Memos 62.8k（MIT，速记向非双链 PKM）、Outline 40.5k（⚠️ **BUSL-1.1，源码可见非开源**：Change Date 2030-09-01 后转 Apache-2.0，商用需授权或等转换）、Docmost 21.6k（AGPL-3.0，Confluence 对标）、BookStack 19.0k（MIT，主仓已迁 Codeberg）、Zettlr 13.5k（GPL-3.0，学术）、VNote 12.9k（LGPL-3.0，C++/Qt）。

闭源对照：Notion、Roam Research、Tana、Reflect、Craft、Bear。

> 双向链接与图谱的机制原理（反向索引、backlinks、图谱诊断信号）详见 [[wiki/concepts/bidirectional-links|双向链接与关系图谱]]。

## 二次开发支持（插件 API 与可改性，2026-09-06 盘点）

> 完整实测依据见 [[wiki/sources/obsidian-oss-alternatives-landscape]]。粗分级：**插件生态**（不改内核能做什么）与**内核可改性**（fork/改内核的门槛与协议后果）是两条独立轴。

- **SiYuan（通道最全）**：kernel 本地 HTTP API（语言无关，任何程序可调）+ petal Plugin API + bazaar 社区集市（插件/主题/模板）+ Docker 自托管（含 OIDC）。减分：`.sy` 私有格式、部分功能付费。
- **Joplin**：官方插件 API + npm 脚手架 + CLI（headless 自动化）；AGPL 传染下分发衍生作品须整体开源。
- **AFFiNE/BlockSuite**：编辑器 BlockSuite 以 **MPL-2.0** 独立成仓，可嵌入自有产品（文件级 copyleft，可与私有代码组合不传染）——"拿编辑器做自己的东西"通道明确；后端 EE 是另一条线的限制。
- **TiddlyWiki**：一切皆插件、单文件可全改，BSD 宽松协议——可改性天花板，但非现代工程栈。
- **Trilium**：社区 fork 成功接管并回归原名，AGPL 治理开放的活证据；服务端脚本可扩展。
- **Foam / SilverBullet**：MIT，fork 无协议负担；Foam 生态已有"标准 Markdown 链接替代 WikiLinks"的 fork 先例（issue #1353）。
- **Logseq**：插件市场存在，但主代码库 Clojure/ClojureScript 上手门槛高 + DB 重写动荡期，深度贡献风险大。
- **AppFlowy**：Rust + Flutter 模块化，但自托管后端归档 + 社区批评，二次开发评估需跟进新结构。
- **不可商用二次开发**：Anytype 客户端（source-available，非商用/Allowed Networks）、Outline（BUSL 至 2030）、AFFiNE 后端（EE）。

## 开源协议的商用含义

AGPL-3.0（思源 / AppFlowy / Logseq / Joplin / Trilium / Docmost 均为，是笔记类开源项目的主流选择）：

| 使用方式 | 义务 |
|---|---|
| 自用 / 公司内部（不分发、不对外提供服务） | 随便改，无开源义务 |
| 分发修改版（哪怕免费） | 衍生作品须整体以 AGPL-3.0 开源 |
| 部署为网络服务（SaaS） | 即使不分发，也须向服务使用者提供完整源码（第 13 条，堵"云厂商白嫖改闭源托管"） |

- **MIT / BSD**（Foam / TiddlyWiki / SilverBullet / Memos / AFFiNE 核心）：保留版权声明即可，可闭源、可商用、可转售，最宽松
- **MPL-2.0**（BlockSuite）：文件级 copyleft——改动的文件保持 MPL 开源，可与私有代码组合，不传染整项目
- **LGPL-3.0**（VNote）：库级 copyleft，作为库链接可闭源，改库本身须开源
- **GPL-3.0**（Notesnook / Zettlr）：分发衍生作品须整体 GPL 开源，无 AGPL 的网络条款
- **EE**（AFFiNE 后端）/**BUSL-1.1**（Outline，2030-09-01 转 Apache-2.0）/**Any Source Available 1.0**（Anytype 客户端）：源码可见非开源，商用/托管受限，逐条读 LICENSE
- 通用坑：① 商标不在协议内，fork 分发须换名换 logo（Joplin® / JOPLIN SAS 为显式例子）；② 通过 API 调用**未修改**的 AGPL 服务，一般认为不构成衍生作品（有法律灰色地带，非法律意见）；③ 官方云订阅服务与协议无关

## 为什么 Obsidian 最适合接 AI（AI 推断，待来源验证）

1. **文件系统即 API**：coding agent 直接把 vault 当代码库读写，无 Notion 式"必须走 API/块模型不透明/限流"的中间层——本库整个 schema 建立于此。
2. **人机共用结构**：wikilink 与 frontmatter 人能读、agent 能 grep，index/log 可用 shell 导航——与 [[wiki/concepts/wiki-vs-rag|编译式知识库优于 RAG]] 的机制同源。
3. **生态补齐 AI 侧**：Smart Connections（本地 embedding）、Copilot、Smart Composer 等（见 [[wiki/entities/obsidian|Obsidian]]）。
4. **对比**：云端闭源产品（Notion / 语雀 / wolai）数据锁在厂商数据库，agent 无法直接读写全量内容，对"AI 维护型知识库"是结构性劣势；同为本地优先的 Logseq（缩进大纲）、思源（`.sy` 格式）对 LLM 的读写不如纯 Markdown 干净。**2026-09-06 印证**：开源侧形态最接近的产品（SiYuan/Logseq/Trilium）反而全部牺牲了"纯 Markdown 文件"这一 Obsidian 最大资产（.sy 私有格式 / DB 化 / 富文本文档库），纯 Markdown + 双链 + 宽松协议的开源组合只在 Foam（VS Code 内）与 SilverBullet 存在，且 star 规模低一档——Obsidian 的生态位在开源世界确实无人完整占据。

**反面**：Obsidian 本体闭源、无实时协作、移动端一般、同步付费——团队协作需求下 Notion / 语雀更合适；但"AI 替你长期维护个人知识库"这一场景下，纯 Markdown 本地方案目前没有真正对手。

## 已知争议/矛盾

- **Anytype 表述修正（2026-09-06 裁决）**：本页 2026-08-23 版称 Anytype"客户端开源、协议未逐条核实"；2026-09-06 实读 anytype-ts LICENSE.md，实为 **Any Source Available License 1.0**（"use, modify, and redistribute … but only (a) for Non-Commercial Use, or (b) for Commercial Use in Allowed Networks"）。以 LICENSE 原文为准：**源码可见，非 OSI 开源**。Anytype 的 any-sync 协议层是另一仓库，协议待单独核验。
- AFFiNE 的开源性质在不同来源中表述不一（"开源" vs "open core"），以 GitHub LICENSE 原文为准（本页已核实）。
- Outline 在不少文章中仍被列为"开源 wiki"，2026-09-06 核验其主仓为 BUSL-1.1——引用此类二手盘点需回查 LICENSE 原文。

## 后续线索

- ~~Trilium 2026 年维护状态未查~~（2026-09-06 收账：社区接管 + 原仓转移 + 拿回原名，见 [[wiki/sources/obsidian-oss-alternatives-landscape]]）
- ~~Anytype 协议未核实~~（2026-09-06 收账：Any Source Available 1.0，非开源；any-sync 层待单独核验）
- ~~本页无 raw/ 剪藏~~（2026-09-06 起有：[[wiki/sources/obsidian-oss-alternatives-landscape]]）
- AppFlowy 接替 AppFlowy-Cloud 的自托管后端形态（影响二次开发评估）
- AS Notes（VS Code wikilink + 纯 Markdown 新扩展，Foam 潜在后继）star/协议未实测
- Logseq DB 版稳定安装版发布节点；语雀现状仍缺权威来源

## 依据来源

- [[wiki/sources/obsidian-oss-alternatives-landscape]]（22 仓 star/协议/活跃度实测 + LICENSE 原文核验，2026-09-06）
- 关联库内来源：[[wiki/sources/karpathy-llm-wiki]]（Obsidian 在 LLM Wiki 模式中的角色）
- 2026-08-23 建页时的核实锚点：[siyuan-note/siyuan](https://github.com/siyuan-note/siyuan)、[AppFlowy-IO/AppFlowy](https://github.com/AppFlowy-IO/AppFlowy)、[toeverything/AFFiNE](https://github.com/toeverything/AFFiNE)、[toeverything/blocksuite](https://github.com/toeverything/blocksuite)
