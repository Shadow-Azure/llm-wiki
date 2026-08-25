---
type: source
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-dsh-docs-repo-readme.md"
  - "raw/2026-08-23-dsh-docs-architecture.md"
  - "raw/2026-08-23-dsh-docs-cordis-primer.md"
  - "raw/2026-08-23-dsh-docs-glossary.md"
  - "raw/2026-08-23-dsh-docs-first-plugin.md"
  - "raw/2026-08-23-dsh-docs-build-a-tool.md"
  - "raw/2026-08-23-dsh-docs-plugin-config.md"
  - "raw/2026-08-23-dsh-docs-plugin-publish.md"
  - "raw/2026-08-23-dsh-docs-plugin-lifecycle.md"
  - "raw/2026-08-23-dsh-docs-service-dependency.md"
  - "raw/2026-08-23-dsh-docs-plugin-events.md"
  - "raw/2026-08-23-dsh-docs-capability-roles.md"
  - "raw/2026-08-23-dsh-docs-llm-adapter.md"
  - "raw/2026-08-23-dsh-docs-extension-cookbook.md"
  - "raw/2026-08-23-dsh-docs-tool-reference.md"
  - "raw/2026-08-23-dsh-docs-adding-a-package.md"
  - "raw/2026-08-23-dsh-docs-conversation-node.md"
  - "raw/2026-08-23-dsh-docs-settings-card.md"
  - "raw/2026-08-23-dsh-docs-subsystem-extensions.md"
  - "raw/2026-08-23-dsh-docs-subsystem-client-modules.md"
  - "raw/2026-08-23-dsh-docs-subsystem-web-server.md"
  - "raw/2026-08-23-dsh-docs-subsystem-attachment.md"
  - "raw/2026-08-23-dsh-docs-subsystem-credentials.md"
  - "raw/2026-08-23-dsh-docs-cli-reference.md"
  - "raw/2026-08-23-dsh-docs-tutorial-index.md"
  - "raw/2026-08-23-dsh-docs-tutorial-01-first-plugin.md"
  - "raw/2026-08-23-dsh-docs-tutorial-02-lifecycle-effects.md"
  - "raw/2026-08-23-dsh-docs-tutorial-03-services.md"
  - "raw/2026-08-23-dsh-docs-tutorial-04-events.md"
  - "raw/2026-08-23-dsh-docs-tutorial-05-config.md"
  - "raw/2026-08-23-dsh-docs-tutorial-06-composition-hmr.md"
  - "raw/2026-08-23-dsh-docs-tutorial-07-into-the-harness.md"
---

# DeepSeek Harness 官方文档（2026-08-23 快照，32 页）

**来源信息**：deepseek-ai/deepseek-harness 官方仓库 docs/ 中文版（commit `b150a551`，2026-08-21 合入 dsh-0.1.1-rc.2），自本地 clone 剪藏。dsh 处于 developer preview，官方明言"将有不兼容变更"。

**一句话概括**："everything is a plugin" 的 agent harness 全套官方文档：插件开发四部曲（插件→工具→配置→打包安装）+ Cordis 框架教程 + 扩展实操手册 + 五个子系统参考（扩展/客户端模块/Web 服务器/附件/凭据）+ CLI 行为参考。

## 核心要点

1. **插件即 ES module**：导出 `name` / `inject` / `Config` / `apply`（或 Service 子类），经 `cordis.yml` 声明式组合加载，注册皆可逆 effect、自动清理（[[wiki/concepts/dsh-plugin-development]]）。
2. **分发的单位是"组合包"（bundle）**：npm 包 + `dsh.bundle` manifest（指向 `cordis.patch.yml` 配置层）；安装四来源（npm / git / tarball / 本地路径），层叠 last-write-wins；**官方无插件市场**，官方建议仅"给仓库加 `dsh-plugin` topic 以便发现"（[[wiki/concepts/dsh-plugin-distribution]]）。
3. **网络与动态加载均为一等能力**：插件同进程全权调外部服务（凭据走专用 seam）；三层动态机制——HMR、浏览器半 lazy bundle、`ctx.dynamicCordisRunner`（agent 运行时 define/run 带源码的插件，含审批闸门）（[[wiki/concepts/dsh-plugin-networking]]）。
4. **Web UI 有正式扩展点**：`dsh.client` 声明浏览器半 + conversation node（React 组件渲染进聊天流）+ settings card + `ctx.webServer.register()` 自定义路由——视频播放插件的全部通道在此（[[wiki/concepts/dsh-video-plugin-feasibility]]）。
5. **能力三角色模式**：Service Definition / Provider / Consumer 分包，同一 seam 可换提供方（bash 例：dsh-shell / dsh-bash-local / dsh-tool-bash）。
6. **信任模型明示**：git 安装的 `prepare` 脚本授权 = "允许该包的代码在安装时于你的机器上执行，且不在 agent 运行的任何沙箱之内"；MCP 服务器命令同理是"沙箱外的受信任可执行代码"。

## 值得注意的细节

- 工具 UI 卡片是**封闭枚举**（generic / terminal / diff / search / web），无自定义卡片类型——富 UI 必须走 client 插件。
- 附件 seam v1 **只收四种图片格式**（png/jpeg/webp/gif），内容寻址 sha256，"先持久化、后追加事件"。
- CLI 参考明确：bundle 成员变化需重启 profile，`cordis.patch.yml` 编辑则热重载；`--dump-config` 可审查层叠结果。
- 术语表（glossary）为每个概念规定规范术语（seam / scope / fiber / 轮次-步骤-Round 层级），是读源码的解码器。

## 后续线索

- `capability-seams.md`（40KB 能力 seam 总目录）与 `config-catalog.md`、`module-graph.md`、`tool-catalog.md` 三份大目录未剪藏——深挖时再取。**capability-seams 已于 2026-08-25 实读引用**（30/28/1 角色普查，见 [[wiki/concepts/dsh-plugin-capability-boundaries]]），剪藏优先级提高。
- cordis-api/ 目录（Context API 参考）与 postmortem/ 目录未剪藏。
- `packages/client/AGENTS.md`（客户端栈/slots 纪律，前端接入的权威文件）与 `docs/web-styling.zh.md` 未剪藏——2026-08-23 前端接入 query 已实读引用，优先登记。
- 社区插件市场（dsh.deepseek404.com、dshplugin.dev 等）与官方 discussion #961 实战帖为社区来源，待剪藏。
- ~~Cordis 与 Koishi 生态的关系〔AI 推断，待来源验证〕~~（**已验证 2026-08-25**：上游作者 Shigma 即 Koishi 作者，MIT——见 [[wiki/concepts/cordis-vendoring]]）。
- **新增待剪藏（2026-08-25 query 实读引用）**：`vendor/README.md`（9 包 manifest + 18 处修改日志）、`THIRD_PARTY_NOTICES.md`、`packages/core/system-prompt/README.md`、`packages/session/session-telemetry/README.md`、`apps/cli/src/profile-boot.ts` + `packages/bundle/{base,web-app}/cordis.patch.yml`（Web 四层装配的依据）——支撑 [[wiki/concepts/cordis-vendoring]] 与 [[wiki/concepts/dsh-plugin-capability-boundaries]] 两页。
