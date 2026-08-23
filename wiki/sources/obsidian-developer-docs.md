---
type: source
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-obsidian-docs-anatomy-of-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-build-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-development-workflow.md"
  - "raw/2026-08-23-obsidian-docs-settings.md"
  - "raw/2026-08-23-obsidian-docs-manifest.md"
  - "raw/2026-08-23-obsidian-docs-versions.md"
  - "raw/2026-08-23-obsidian-docs-requesturl.md"
  - "raw/2026-08-23-obsidian-docs-submit-your-plugin.md"
  - "raw/2026-08-23-obsidian-docs-plugin-guidelines.md"
  - "raw/2026-08-23-obsidian-docs-release-with-github-actions.md"
  - "raw/2026-08-23-obsidian-docs-beta-testing-plugins.md"
  - "raw/2026-08-23-obsidian-docs-developer-policies.md"
  - "raw/2026-08-23-obsidian-docs-submission-requirements.md"
  - "raw/2026-08-23-obsidian-docs-community-directory-faq.md"
  - "raw/2026-08-23-obsidian-docs-manage-entry.md"
---

# Obsidian 官方开发者文档（插件开发与分发）

**来源信息**：Obsidian 官方开发者文档 [docs.obsidian.md](https://docs.obsidian.md)，markdown 原文取自开源仓库 [obsidianmd/obsidian-developer-docs](https://github.com/obsidianmd/obsidian-developer-docs)（main 分支），2026-08-23 快照剪藏 15 页（每页 frontmatter 记录原文路径）。

**一句话概括**：插件从"三件套文件夹"到社区目录自动审核发布的官方全流程规范，含网络访问与动态资源的政策边界。

## 核心要点

- **插件解剖与生命周期**：三件套（manifest.json / main.js / styles.css）、`Plugin` 子类 onload/onunload → [[wiki/concepts/obsidian-plugin-development]]
- **构建与版本兼容**：esbuild 产物、审核扫描器直接跑仓库 build 脚本、versions.json 版本回退 → [[wiki/concepts/obsidian-plugin-development]]
- **提交通程已迁移到社区目录网站**（community.obsidian.md 提交 + 自动审核，非旧博客常写的 obsidian-releases PR 流程）→ [[wiki/concepts/obsidian-plugin-distribution]]
- **政策红线**：禁混淆 / 禁自更新 / 禁客户端遥测；网络使用等事项须 README 披露 → [[wiki/concepts/obsidian-plugin-distribution]]
- **网络通道**：`requestUrl()` 官方 API 免 CORS；动态资源的技术可行性与政策边界 → [[wiki/concepts/obsidian-plugin-networking]]

## 值得注意的细节 / 引文

- **自更新禁令**（原文）："Plugins and themes must not: … Install or update themselves or their dependencies."（developer-policies）——更新必须走 GitHub Release 官方通道
- **requestUrl**（原文）："Similar to `fetch()`, request a URL using HTTP/HTTPS, without any CORS restrictions."（requestUrl 参考）
- **审核含构建校验**：自动审核分 Manifest / Releases / Source code / **Build verification** 四节，校验 release 产物与源码构建一致（manage-entry / FAQ）
- **扫描器构建脚本顺序**：`build` > `build:plugin` > `compile`，取到的第一个（FAQ）——须确保是生产构建
- **manifest id 规则**：仅小写字母与连字符、不能以 `plugin` 结尾、不能含 `obsidian`；本地开发时 id 须与文件夹名一致，否则 `onExternalSettingsChange` 等不生效（manifest）
- **fork 政策**：需原作者公开可验证的书面许可，或原作者失联 ≥6 个月且经 30 天等待（developer-policies）
- **更新免重提**："users can download new releases from GitHub directly from within Obsidian"（submit-your-plugin）——首次提交后，发布仅靠新 release
- **私有源码仓库合法**：公开仓库放构建产物、私有仓库放源码，装官方 GitHub App 授权扫描并做构建一致性校验（manage-entry）

## 后续线索

- 未剪藏的相关官方页：Mobile development、Events、Vault、Editor 扩展系列、User interface 其余页（Commands/Modals/Views/Ribbon…）、Reference/TypeScript API 全量、CSS variables——按需补
- [obsidian-sample-plugin](https://github.com/obsidianmd/obsidian-sample-plugin) 与 [obsidianmd/eslint-plugin](https://github.com/obsidianmd/eslint-plugin)（本地预检）两个仓库值得按需剪藏
- BRAT（社区 beta 分发工具）官方文档仅一页提及，其自身文档未入库
- 旧提交流程（obsidianmd/obsidian-releases 仓库 PR）的演变时间线可作考古线索（待来源验证）
