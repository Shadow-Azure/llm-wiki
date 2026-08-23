---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-obsidian-docs-requesturl.md"
  - "raw/2026-08-23-obsidian-docs-developer-policies.md"
  - "raw/2026-08-23-obsidian-docs-plugin-guidelines.md"
  - "raw/2026-08-23-obsidian-docs-community-directory-faq.md"
  - "raw/2026-08-23-obsidian-docs-manage-entry.md"
---

# Obsidian 插件网络访问与动态资源

**定义**：技术上插件可自由访问网络（两条通道，其一为官方免 CORS API）；政策上社区目录要求**披露**网络使用、**禁止自更新**——数据类资源可以动态下载，可执行代码的动态加载处于政策灰色地带。

## 两条网络通道

| 通道 | CORS | 说明 |
|---|---|---|
| 标准 `fetch()` / XHR | 受限 | 插件运行在 Electron renderer（`app://obsidian.md` origin），只适合本身开了 CORS 的服务 |
| **`requestUrl()`**（官方 API） | **免 CORS** | 原文："Similar to `fetch()`, request a URL using HTTP/HTTPS, without any CORS restrictions." —— 调用外部服务 API 的标准姿势 |

官方指南同时示范了 `requestUrl` 的 async/await 用法。API key 等凭据的存放无官方专门方案，通行做法是存在插件设置（`data.json`）。

## 政策边界（针对社区目录上架插件）

- **网络使用必须在 README 披露**：说明用了哪些远程服务、为什么需要。
- **禁止自安装 / 自更新 / 自装依赖**——更新必须走 GitHub Release → Obsidian 客户端的官方通道。
- **主题禁止从网络加载资源**；插件未被禁止（数据资源动态加载合法，但仍落入"网络使用须披露"）。
- 这些政策只约束官方目录上架的插件；目录外安装（手动/BRAT）不受其管辖，官方也注明"仍是好实践"。

## 动态下载与加载资源

**数据 / 资产资源（模板、词典、模型、图片…）——可行**：

- 技术：`requestUrl`/`fetch` 下载，桌面端还可用 Node `fs` 落盘缓存；移动端走 Web API。
- 政策：属"网络使用"，README 披露即可。
- 〔AI 推断，待来源验证〕缓存落盘位置的通行约定是插件自身目录（`.obsidian/plugins/<id>/`）或 vault 内约定路径；官方未规定，注意别破坏 vault 数据。

**可执行代码——灰色地带**：

- 官方政策**没有逐字禁止**"运行时下载并执行代码"，明文禁的是**自更新/自装依赖**与**混淆代码**。
- 但三条规则合力构成事实约束：① 自更新禁令；② 审核的 **Build verification** 校验 release 三资产与源码构建一致（`main.js` 之外再藏一份代码会过不了校验逻辑的初衷）；③ 禁混淆（隐藏目的）。〔AI 推断，待来源验证〕远程代码加载等于绕过审核，实际难以过审；社区目录的信任模型建立在"审过的 main.js = 全部可执行代码"之上。
- 若确需动态逻辑，合规思路是**声明式驱动**：下载配置/数据（JSON、模板），由审过的代码解释执行，而不是下载代码本身。〔AI 推断，待来源验证〕

## 与其他概念的关联

- 披露义务与提交流程 → [[wiki/concepts/obsidian-plugin-distribution]]
- 开发基础（renderer 进程模型、Node 能力）→ [[wiki/concepts/obsidian-plugin-development]]
- 宿主平台 → [[wiki/entities/obsidian]]

## 依据来源

- [[wiki/sources/obsidian-developer-docs|Obsidian 官方开发者文档]]（requestUrl / developer-policies / plugin-guidelines / FAQ / manage-entry 各页）；"灰色地带"两段为本页 AI 推断，已标注
