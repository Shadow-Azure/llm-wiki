---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: []
---

# 笔记工具格局

**一句话定义**：个人知识工具市场沿"本地优先 vs 云端 SaaS"分化——开源产品几乎全部站本地优先阵营，闭源产品全部是云端阵营；[[wiki/entities/obsidian|Obsidian]] 例外地"软件闭源、数据本地开放"。

> 本页由 query 回填（2026-08-23）。产品与协议事实经当日 web 检索核实（协议逐仓库查 GitHub），但**尚未剪藏为 raw/ 来源**，外部链接内联标注于文末；待剪藏后 frontmatter 补 sources。

## 国内：开源三强 vs 云端闭源阵营

| 产品 | 开源 | 协议（2026-08-23 于 GitHub 核实） | 形态 |
|---|---|---|---|
| 思源笔记 | ✅ | **AGPL-3.0**（整仓） | 国人开发，最接近 Obsidian 的对标：本地优先、隐私优先、块级引用 + 双链 + 大纲，端到端加密同步，大部分功能含商用免费。注意原生数据为自有 `.sy` 格式，Markdown 靠导出 |
| AppFlowy | ✅ | **AGPL-3.0**（含 AppFlowy-Cloud 服务端） | Notion 替代，Rust + Flutter，数据库视图强，主打可自托管；华人创始团队、国际化运作 |
| AFFiNE | 部分 | **混合**：核心/前端 **MIT**；后端 server（`packages/backend/`）为**企业版协议（EE，生产环境需付费订阅）**；编辑框架 BlockSuite 单独仓库为 **MPL-2.0** | 上海团队 toeverything；文档 + 白板 + 数据库；自托管版偏早期 |

**AFFiNE 的"开源"是 open core**：前端随便用，后端源码可见但上生产要订阅，且修改版权归 AFFiNE 保留——中文社区常直接称其"开源"，引用时需区分（Grafana / GitLab 同款模式）。

闭源阵营：语雀（阿里，云端知识库；资源向钉钉文档倾斜为 V2EX 社区观察，待可靠来源）、wolai 我来、FlowUs 息流（均为 Notion-like）、flomo（卡片轻记，重"记"不重"理"）、印象笔记、有道云笔记、幕布、Heptabase（台湾团队，视觉卡片）。

## 国外开源

- **Logseq**（AGPL-3.0）：大纲式双链 + 每日日志，直接用 Markdown/org 文件；缩进大纲语法有适应成本
- **Joplin**（AGPL-3.0）：层级笔记 + 强剪藏 + 端到端加密同步；非双链优先
- **Anytype**：客户端开源、本地优先 + E2E 加密的 object 模型，生态尚早
- **Trilium Notes**（AGPL-3.0）：层级式，自托管友好，极客向
- 团队 wiki 向：Outline、BookStack、Docmost（对标 Confluence，非个人知识库场景）
- 闭源对照：Notion、Roam Research、Tana、Reflect、Craft、Bear

> 双向链接与图谱的机制原理（反向索引、backlinks、图谱诊断信号）详见 [[wiki/concepts/bidirectional-links|双向链接与关系图谱]]。

## 开源协议的商用含义

AGPL-3.0（思源 / AppFlowy / Logseq / Joplin 均为，是笔记类开源项目的主流选择）：

| 使用方式 | 义务 |
|---|---|
| 自用 / 公司内部（不分发、不对外提供服务） | 随便改，无开源义务 |
| 分发修改版（哪怕免费） | 衍生作品须整体以 AGPL-3.0 开源 |
| 部署为网络服务（SaaS） | 即使不分发，也须向服务使用者提供完整源码（第 13 条，堵"云厂商白嫖改闭源托管"） |

- **MIT**（AFFiNE 核心）：保留版权声明即可，可闭源、可商用、可转售，最宽松
- **MPL-2.0**（BlockSuite）：文件级 copyleft——改动的文件保持 MPL 开源，可与私有代码组合，不传染整项目
- **EE**（AFFiNE 后端）：源码可见非开源；开发/测试免费，生产需订阅
- 通用坑：① 商标不在协议内，fork 分发须换名换 logo；② 通过 API 调用**未修改**的 AGPL 服务，一般认为不构成衍生作品（有法律灰色地带，非法律意见）；③ 官方云订阅服务与协议无关

## 为什么 Obsidian 最适合接 AI（AI 推断，待来源验证）

1. **文件系统即 API**：coding agent 直接把 vault 当代码库读写，无 Notion 式"必须走 API/块模型不透明/限流"的中间层——本库整个 schema 建立于此。
2. **人机共用结构**：wikilink 与 frontmatter 人能读、agent 能 grep，index/log 可用 shell 导航——与 [[wiki/concepts/wiki-vs-rag|编译式知识库优于 RAG]] 的机制同源。
3. **生态补齐 AI 侧**：Smart Connections（本地 embedding）、Copilot、Smart Composer 等（见 [[wiki/entities/obsidian|Obsidian]]）。
4. **对比**：云端闭源产品（Notion / 语雀 / wolai）数据锁在厂商数据库，agent 无法直接读写全量内容，对"AI 维护型知识库"是结构性劣势；同为本地优先的 Logseq（缩进大纲）、思源（`.sy` 格式）对 LLM 的读写不如纯 Markdown 干净。

**反面**：Obsidian 本体闭源、无实时协作、移动端一般、同步付费——团队协作需求下 Notion / 语雀更合适；但"AI 替你长期维护个人知识库"这一场景下，纯 Markdown 本地方案目前没有真正对手。

## 已知争议/矛盾

- 暂无库内矛盾。需注意：AFFiNE 的开源性质在不同来源中表述不一（"开源" vs "open core"），以 GitHub LICENSE 原文为准（本页已核实）。

## 后续线索

- 待剪藏正式来源（剪藏后本页 sources 补全）：思源官网 / GitHub LICENSE、AFFiNE LICENSE 文件、[Obsidian 官方 roadmap](https://obsidian.md/roadmap/)、[2026 AI 插件盘点](https://www.shadow.do/blog/best-ai-plugins-for-obsidian-2026)、[小众软件思源评测](https://www.appinn.com/siyuan-biji/)
- 语雀现状（阿里资源倾斜）目前仅有社区讨论支撑，值得找更权威来源
- Anytype 的具体协议条款未逐条核实；Trilium 2026 年维护状态未查

## 依据来源

- 本页为 query 综合回填，暂无 raw/ 剪藏。核实过的外部锚点：[siyuan-note/siyuan](https://github.com/siyuan-note/siyuan)（AGPL-3.0）、[AppFlowy-IO/AppFlowy](https://github.com/AppFlowy-IO/AppFlowy)（AGPL-3.0）、[toeverything/AFFiNE](https://github.com/toeverything/AFFiNE)（MIT + EE 混合，经 LICENSE 原文解码核实）、[toeverything/BLOCKSUITE](https://github.com/toeverything/BLOCKSUITE)（MPL-2.0）
- 关联库内来源：[[wiki/sources/karpathy-llm-wiki]]（Obsidian 在 LLM Wiki 模式中的角色）
