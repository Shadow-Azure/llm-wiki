---
type: clip
source:
  - "https://api.github.com/repos/{siyuan-note/siyuan, logseq/logseq, TriliumNext/Trilium, foambubble/foam, TiddlyWiki/TiddlyWiki5, dendronhq/dendron, silverbulletmd/silverbullet, AppFlowy-IO/AppFlowy, AppFlowy-IO/AppFlowy-Cloud, toeverything/AFFiNE, toeverything/blocksuite, anyproto/anytype-ts, laurent22/joplin, streetwriters/notesnook, standardnotes/app, usememos/memos, outline/outline, docmost/docmost, BookStackApp/BookStack, Zettlr/Zettlr, vnotex/vnote, obsidianmd/obsidian-clipper}（star/license/pushed/archived 实测于 2026-09-06）"
  - "https://raw.githubusercontent.com/laurent22/joplin/dev/LICENSE（AGPL-3.0-or-later + 目录级例外 + 商标条款）"
  - "https://github.com/anyproto/anytype-ts/blob/main/LICENSE.md（Any Source Available License 1.0）"
  - "https://github.com/outline/outline/blob/main/LICENSE（BUSL-1.1，Change Date 2030-09-01 → Apache-2.0）"
  - "https://raw.githubusercontent.com/TiddlyWiki/TiddlyWiki5/master/license（BSD 三条款风格，UnaMesa 2007–2025）"
  - "https://raw.githubusercontent.com/foambubble/foam/master/LICENSE（MIT，Jani Eväkallio）"
  - "https://github.com/siyuan-note/siyuan README.md（kernel API / petal / bazaar / Docker OIDC / 付费条款节录）"
  - "https://github.com/orgs/TriliumNext/discussions/5867（TriliumNext 拿回 Trilium Notes 原名）"
  - "https://discuss.logseq.com/t/whats-new-with-logseq-db-may-16th-2026/35020（Logseq DB 进展帖）"
  - "https://github.com/logseq/docs/blob/master/db-version-changes.md（DB 版破坏性变更）"
  - "https://proton.me/blog/proton-standard-notes-join-forces + https://standardnotes.com/blog/2025-update（Proton 收购 Standard Notes）"
title: "类 Obsidian 开源项目格局（star/协议/活跃度/二次开发通道，实测 2026-09-06）"
author: 各 repo 与官方公告（见正文）
accessed: 2026-09-06
via: GitHub API 实测 + LICENSE 原文抓取 + 官方论坛/公告 web 检索
clip_note: [[wiki/concepts/note-tool-landscape|笔记工具格局]]页（2026-08-23 建页）三条未了线索（Trilium 维护状态 / Anytype 协议 / star 数据）收账，并新增"二次开发支持"维度。star 数为 2026-09-06 API 实测值，会漂移。
---

# 实测数据（GitHub API，2026-09-06）

## 本地优先 PKM（形态最像 Obsidian）

| repo | ⭐ | license | 最后推送 | 备注 |
|---|---|---|---|---|
| siyuan-note/siyuan | 46,186 | AGPL-3.0 | 2026-09-06 | 块级双链 + Docker 自托管；kernel HTTP API；部分功能付费会员 |
| logseq/logseq | 44,791 | AGPL-3.0 | 2026-09-05 | 大纲式双链；DB 版重写未稳（见下） |
| TriliumNext/Trilium | 37,732 | AGPL-3.0 | 2026-09-06 | 即原 zadam/trilium 转移而来；社区接管（见下） |
| foambubble/foam | 17,381 | MIT | 2026-09-05 | VS Code 内嵌 Markdown 双链 PKM |
| TiddlyWiki/TiddlyWiki5 | 8,640 | BSD 三条款（自定义文件） | 2026-09-05 | 单文件 HTML wiki；2004 年至今 |
| dendronhq/dendron | 7,463 | Apache-2.0 | 2025-11-13 | VS Code PKM；约 10 个月无提交，事实停摆 |
| silverbulletmd/silverbullet | 6,004 | MIT | 2026-09-03 | 自托管 Markdown 笔记，主打可扩展 |

## 块编辑协作（Notion 形态）

| repo | ⭐ | license | 最后推送 | 备注 |
|---|---|---|---|---|
| AppFlowy-IO/AppFlowy | 76,328 | AGPL-3.0 | 2026-09-01 | Rust + Flutter；AI 协作工作台 |
| toeverything/AFFiNE | 72,219 | 混合（核心 MIT + 后端 EE） | 2026-09-05 | open core；编辑器 blocksuite 单独仓 6,002⭐ MPL-2.0 |
| anyproto/anytype-ts | 8,753 | **Any Source Available License 1.0（非开源）** | 2026-09-05 | 桌面客户端；协议见下 |

## 剪藏/同步笔记（Evernote 形态）

| repo | ⭐ | license | 最后推送 | 备注 |
|---|---|---|---|---|
| laurent22/joplin | 56,252 | AGPL-3.0-or-later | 2026-09-05 | 官方插件 API + CLI |
| streetwriters/notesnook | 14,517 | GPL-3.0 | 2026-09-05 | 端到端加密 |
| standardnotes/app | 6,621 | AGPL-3.0 | 2026-09-04 | 2024-11 起 Proton 旗下 |

## 自托管 web wiki / 团队向

| repo | ⭐ | license | 最后推送 | 备注 |
|---|---|---|---|---|
| usememos/memos | 62,786 | MIT | 2026-09-05 | 速记（quick capture）向，非双链 PKM |
| outline/outline | 40,470 | **BUSL-1.1（非开源）** | 2026-09-06 | Change Date 2030-09-01 后转 Apache-2.0 |
| docmost/docmost | 21,594 | AGPL-3.0 | 2026-09-05 | Confluence 对标 |
| BookStackApp/BookStack | 19,025 | MIT | 2026-09-05 | 主仓已迁 Codeberg（GitHub 仓标注 "NOW MANAGED ON CODEBERG"） |
| Zettlr/Zettlr | 13,475 | GPL-3.0 | 2026-09-03 | 学术写作桌面端 |
| vnotex/vnote | 12,935 | LGPL-3.0 | 2026-09-06 | C++/Qt 原生 |

对照：obsidianmd/obsidian-clipper 5,139⭐ MIT（Obsidian 官方开源组件）；AppFlowy-IO/AppFlowy-Cloud 2,002⭐ AGPL-3.0，**2026-09-01 已归档**（见下）。

# 协议原文核验（三条线索裁决）

1. **Joplin = AGPL-3.0-or-later**。LICENSE 原文："All code in this repository is licensed under the AGPL-3.0-or-later License **unless a directory contains a LICENSE or LICENSE.md file**"（如 packages/server 有独立协议）；另有 "Joplin® is a trademark of JOPLIN SAS registered in the European Union"。→ 证实 2026-08-23 库内记录，推翻"Joplin 是 MIT"的常见旧印象。
2. **Anytype 桌面客户端不是开源协议**。anytype-ts LICENSE.md 原文（"Any Source Available License 1.0"）："Any Association grants you ("Licensee") a license to use, modify, and redistribute the Software, but only (a) for Non-Commercial Use, or (b) for Commercial Use in Allowed Networks"，且再分发必须保持本协议。→ **修正库内 2026-08-23"客户端开源"表述**：源码可见（source-available），商用二次开发受限。
3. **Outline = BUSL-1.1（Business Source License）**。Change Date 2030-09-01，Change License Apache-2.0；许可文本自称 "not an Open Source license"；存档镜像显示早期 Change Date 为 2025-11-11（后被延长）。→ 源码可见 ≠ 开源，商用需注意限制或等 2030 转换/购商业授权。

其余核验：TiddlyWiki5 license 为 BSD 三条款风格（"Copyright (c) 2004-2007, Jeremy Ruston / 2007-2025, UnaMesa Association"， Redistribution-and-use 条款）；Foam LICENSE 原文确认 MIT（Copyright 2020 - present Jani Eväkallio）；AFFiNE 主仓 2026-09-06 API 仍报 NOASSERTION，与库内 2026-08-23 逐文件核验一致（核心/前端 MIT + `packages/backend/` EE）。

# 三条线索收账（笔记工具格局页 2026-08-23 登记）

- **Trilium 维护状态**：zadam 于 2024-01 进入维护模式并归档原仓 → 社区（Elian Doran 等）fork 出 TriliumNext，2024 年中发首个稳定版 → zadam 同意将**原仓库转移给 TriliumNext 团队**并让其"拿回 Trilium Notes 原名"（GitHub Discussion #5867："the TriliumNext project is mature enough to regain the original name"）。沿革细节：fork 仓 TriliumNext/Notes（2,925⭐）已归档（最后推送 2025-06-24），开发收拢到转移来的 TriliumNext/Trilium（37.7k⭐，2026-09-06 仍活跃推送）。→ AGPL 社区接管并"姓名回归"的治理活标本。
- **Logseq DB 版**：至 2026-05-16 仍在双周进展帖（重点：sync 可信度、CLI、大图性能），**无稳定安装版时间表**；Reddit 有用户抱怨已等约两年；官方 docs/db-version-changes.md 记录破坏性变更（logbook 时间戳 → 状态变更历史、TODO 由关键字改命令创建）。重写动机（官方论坛）：文件版每次建块**重写整个 Markdown 文件**、重命名会级联改写所有引用文件，大库性能瓶颈。
- **Standard Notes 现状**：2024-11 Proton 收购（官方博客承诺保持开源、免费、受支持）；2025 官方更新确认**保持独立产品/品牌/订阅（约 $39/年）**，不并入 Proton 订阅；社区存在"项目死了吗/发布放缓"讨论（GitHub forum issue #3906）；创始人新项目 Shape 遭 Proton 法务援引非竞争条款阻拦，隐私社区争议。

# 其他值得注意

- **AppFlowy-Cloud 2026-09-01 归档**，归档公告提及转向"our current SaaS and self-hosted solutions"的新结构（替代后端形态未查明）；r/SelfHosting 有高热度批评帖 "Tried self-hosting AppFlowy — turns out it's not really open"。主仓 AppFlowy（76.3k⭐）仍 AGPL 活跃——但其自托管/二次开发评估需跟进新后端。
- **SiYuan README 明示的二次开发通道**：kernel API 文档（docs/API.md，本地 HTTP 服务）、petal（Plugin API 仓）、bazaar（社区集市仓：插件/主题/模板/图标）、Docker 自托管（含 OIDC 认证配置项）、AI 经 OpenAI API；"Some features are only available to paid members"（订阅制功能并存）。
- Dendron（Apache-2.0）最后推送 2025-11-13，视为事实停摆。
- TriliumNext/Trilium 的 37.7k⭐ 含原 zadam/trilium 历史星数继承（仓库转移非新仓）。

# 搜索过程发现（未深挖，登记为线索）

- **AS Notes**：较新的 VS Code wikilink + 纯 Markdown 扩展（Reddit r/logseq 讨论中被列为 Foam 替代），支持重命名跨文件更新引用——star/协议未实测。
- Foam 生态 fork 已实现"标准 Markdown 链接替代 WikiLinks"（foambubble/foam issue #1353）。
- AppFlowy 替代后端（接替 AppFlowy-Cloud 的自托管方案）形态待查。
- Anytype 的 any-sync 协议层（P2P 同步）与客户端协议不同仓，协议条款未逐一核实——若评估 Anytype 系二次开发需单独查。
