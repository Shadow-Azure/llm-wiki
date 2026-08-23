---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-obsidian-docs-submit-your-plugin.md"
  - "raw/2026-08-23-obsidian-docs-developer-policies.md"
  - "raw/2026-08-23-obsidian-docs-submission-requirements.md"
  - "raw/2026-08-23-obsidian-docs-community-directory-faq.md"
  - "raw/2026-08-23-obsidian-docs-manage-entry.md"
  - "raw/2026-08-23-obsidian-docs-release-with-github-actions.md"
  - "raw/2026-08-23-obsidian-docs-beta-testing-plugins.md"
---

# Obsidian 插件分发与治理

**定义**：插件通过 "GitHub 仓库 + [community.obsidian.md](https://community.obsidian.md) 社区目录" 分发——首次提交经自动审核后发布；此后**更新免重提**，用户端直接从 GitHub Release 拉取新版本。

## 提交流程（官方现行流程）

1. **前置**：GitHub 账号 + Obsidian 账号（在 community.obsidian.md 登录并关联 GitHub，用于验证仓库所有权）。
2. **仓库必备**：根目录 `README.md`、`LICENSE`、`manifest.json`。
3. **发版**：manifest `version` 按 semver `x.y.z` 递增 → 创建 GitHub Release（**tag 必须等于 version**）→ 上传三资产：`main.js`、`manifest.json`、`styles.css`（可选）。
4. **提交**：在 community.obsidian.md 添加插件。目录读取默认分支 HEAD 的 manifest（`id` 全库唯一、不得含 `obsidian`）。
5. **自动审核**：结果分四节——Manifest / Releases / Source code / **Build verification**；每项评级 Error / Warning / Recommendation / Pass。**Error 未解决则插件不可从 Obsidian 内安装**；Warning 不阻断。
6. **修复**：改仓库 + 发新 release 触发重扫；可手动 "Check for new releases"；**Review branch** 可对任意分支/tag/commit 预扫，不必先发版。

> [!注意] 流程已变
> 多数旧博客写的 "向 obsidianmd/obsidian-releases 仓库提 PR" 是旧流程；现行官方文档为社区目录网站提交 + 自动审核。※ 旧流程的存废时间线待补来源验证。

## 更新

首次提交是唯一一次人工提交流程。之后：开发者发新 GitHub Release → 目录周期性扫描 → 用户在 Obsidian 内直接更新（客户端从 GitHub Release 拉取三资产）。

## 政策红线（Developer policies）

**明令禁止**：

- 混淆代码以隐藏用途
- 互联网加载的动态广告；自己界面之外插入静态广告
- 客户端遥测
- **自安装 / 自更新 / 自装依赖**（更新必须走 GitHub Release 通道）
- 主题从网络加载资源（插件不在此列，但仍须披露网络使用）

**须在 README 披露**（否则不允许）：付费墙、账号要求、**网络使用**（用了哪些远程服务、为什么）、访问 vault 之外文件、界面内静态广告、服务端遥测（附隐私政策链接）、闭源（逐案处理）。

**版权与 fork**：LICENSE 必备并遵守上游协议；fork 需原作者公开可验证的书面许可，或原作者失联 ≥6 个月且经 30 天公告等待，且须署名原作者。

**违规处置**：先联系限期整改，不改则下架；恶意 / 不配合 / 累犯立即下架；长期失维护或严重损坏也可能被移除。

## 插件专属提交要求

- `fundingUrl` 仅用于收款链接（Buy Me A Coffee / GitHub Sponsors 等），不收捐款就删掉
- `minAppVersion` 不确定时取最新稳定版兜底
- 描述 ≤250 字符、动词开头（"Translate selected text into…"式）、句号结尾、无 emoji
- 命令 ID 不含插件 ID（Obsidian 自动加前缀）
- 删除 sample plugin 的全部示例代码与占位类名（`MyPlugin` 等须改名）

## 辅助设施

- **GitHub Actions 官方模板**：打 tag 自动构建 + 建 draft release + 上传三资产，含 artifact attestation（构建溯源签名，官方推荐）
- **BRAT**：官方推荐的 beta 测试分发通道（Obsidian 本身不支持 beta 版）
- **私有源码仓库**：源码放私有仓库、构建产物放公开仓库，装官方 GitHub App 授权目录扫描私有源码并做构建一致性校验——闭源插件合规路径
- **官方 ESLint 插件**（obsidianmd/eslint-plugin）：本地跑同款审核检查
- 扫描器忽略固定模式列表（node_modules、`*.test.*`、`*.mjs`、scripts、docs 等）——测试与工具文件不会计入扫描

## 与其他概念的关联

- 开发与构建 → [[wiki/concepts/obsidian-plugin-development]]
- 网络使用披露与自更新禁令的细节 → [[wiki/concepts/obsidian-plugin-networking]]
- 平台与生态位 → [[wiki/entities/obsidian]]

## 依据来源

- [[wiki/sources/obsidian-developer-docs|Obsidian 官方开发者文档]]（submit-your-plugin / developer-policies / submission-requirements / FAQ / manage-entry / release-with-github-actions / beta-testing 各页）
