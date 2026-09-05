---
type: entity
created: 2026-08-23
updated: 2026-08-30
sources:
  - "raw/2026-08-23-dsh-docs-repo-readme.md"
  - "raw/2026-08-23-dsh-docs-architecture.md"
  - "raw/2026-08-23-dsh-docs-cordis-primer.md"
  - "raw/2026-08-23-dsh-docs-glossary.md"
  - "raw/2026-08-23-dsh-docs-first-plugin.md"
  - "raw/2026-08-23-dsh-docs-plugin-publish.md"
  - "raw/2026-08-23-dsh-docs-cli-reference.md"
  - "raw/2026-08-23-dsh-docs-subsystem-extensions.md"
  - "raw/2026-08-23-dsh-docs-subsystem-client-modules.md"
  - "raw/2026-08-30-dsh-src-agent-lifecycle.md"
  - "raw/2026-08-30-dsh-src-subsystem-core.md"
  - "raw/2026-08-30-dsh-src-subsystem-session.md"
  - "raw/2026-08-30-dsh-src-subsystem-tools.md"
  - "raw/2026-08-30-dsh-src-subsystem-skills.md"
---

# deepseek-harness（dsh）

**一句话身份**：DeepSeek 开源的通用 agent harness（TypeScript / pnpm monorepo，`npx @deepseek-ai/dsh`），设计哲学 **"everything is a plugin"**——模型适配器、工具注册表、会话日志、连 agent loop 本身都是插件，没有可打补丁的特权内核。2026-08 开源即爆火（三天近 13 万 star）；目前 developer preview，官方明言将有破坏性变更。

> **精化（2026-08-25，代码仓实读）**："一切皆插件"指**挂载形态统一**（连 core 服务也是配置行）；可替换性上分 30 core / 28 seam / 1 bundle 三种角色——core 服务无并列实现、agentLoop 是唯一具体循环，"可 patch 替换"≠"有得换、换了能用"。详见 [[wiki/concepts/dsh-plugin-capability-boundaries]]。

## 运行时架构要点（2026-08-30 源码实读，v0.1.2-alpha.1 @ cd5ef814）

- 本地 clone `/Users/zn-ice/2026/deepseek-harness`：~50 packages、4700+ TS 文件；仓库自带 `docs/`（架构）+ `docs/subsystems/`（45 份子系统文档，多为生成物）+ 生成目录（tool-catalog 2279 行等）。
- 四大运行时支柱，已各立概念页：[[wiki/concepts/dsh-agent-loop|agent loop（turn/step 状态机）]] · [[wiki/concepts/dsh-session-log|会话日志（单一事实源 + compaction）]] · [[wiki/concepts/dsh-tool-pipeline|工具管线（三道 waterfall + fail-closed 审批 + PTC）]] · [[wiki/concepts/dsh-coordination-subsystems|协调子系统（skill/subagent/goal/Ralph + 自治限幅）]]。
- 一句话结构观：**循环写日志、日志投影历史、工具走管线、策略全是监听者**——"everything is a plugin" 在运行时的形态是"策略 = 管线上的 waterfall 监听者"。
- 协调子系统全部带自治限幅（goal round ≤256、jobs maxConsecutiveWakes=3、goal resume 必须人工 re-arm）；skill 写入发现根同会话即生效——自进化插件的机制地基（连 [[raw/self-opt/INDEX.md|self-opt 检索库]]）。
- 来源页：[[wiki/sources/dsh-source-runtime|dsh 源码仓运行时架构（v0.1.2-alpha.1）]]。

## 插件机制要点

- **框架**：vendored **Cordis**（服务容器 + 类型化事件 + 可逆 effect）——源码收编进 `vendor/`（9 包，rescope 为 `@deepseek-ai/*`，18 处本地修改），上游 cordiverse/cordis 为 **MIT**（作者 Shigma），详见 [[wiki/concepts/cordis-vendoring]]。
- **声明**：插件为导出固定字段的 ES module——`name`、`inject`（声明依赖的**服务**而非具体实现，激活顺序由服务可用性驱动）、`Config`（运行时 schema）、`apply(ctx, config)` 或 `Service` 子类。
- **组合**：profile（如 `web`/`headless`）→ bundle → `cordis.patch.yml` 声明式配置树；patch 按 `id` 定位并**整行替换**任意插件行（含核心行）——"替换核心"的官方途径，层叠 last-write-wins。
- **生命周期**：所有注册都是可逆 effect（disposer 逆序自动回滚），base bundle 自带 HMR。
- **API 表面**：TypeScript declaration merging 向 `Context` 声明服务键（`ctx.llm` / `ctx.tools` / `ctx.sessions` / `ctx.agentLoop` / `ctx.sandbox`…）；事件四种调度模式（`emit` 观察 / `waterfall` around-中间件可短路 / `parallel` / `serial`）。
- **隔离**：同进程、无沙箱、全信任（同 Obsidian）；但审批闸门（`tools/pre-execute` 的 allow/deny/ask）、沙箱执行、文件系统 provider 本身也是插件——安全能力是可组合的。
- **双端插件**：包可声明 `dsh.client` 携带浏览器半（bundle 经 `/plugins/<id>/client.js` lazy 提供）；Web UI 扩展点（conversation node / slots / settings card）挂在 web profile 上。
- **动态插件**：`ctx.dynamicCordisRunner` 让 agent 在运行时 define/run 带源码的版本化插件（含审批闸门）；`dsh-tool-cordis` 使模型可自写自挂插件。
- **代表插件**：`dsh-llm-deepseek`（提供方即插件）、`dsh-tool-todo`（工具即插件）、`dsh-llm-retry`（监听 waterfall 做重试——事件即扩展点）。

## 分发与生态要点

- 分发单位是组合包（npm 包 + `dsh.bundle` manifest），安装 `dsh plugin --profile <name> add`（npm/tarball/git/本地四来源）；**官方无插件市场**，仅约定 GitHub `dsh-plugin` topic；社区已自建多个市场与应用内市场插件。详见 [[wiki/concepts/dsh-plugin-distribution]]。

## 与本库主题的关联

- 三方机制对比中"agent harness 扩展点 = 事件管线"路线的代表（见 [[wiki/concepts/plugin-mechanism-comparison]]）。
- 与 [[wiki/entities/claudian]] 对照：同样是"agent + 插件化"，dsh 自建宿主把一切做成插件，Claudian 寄生 Obsidian 并桥接 Claude Code 生态——两条相反的组装路径。
- 插件开发/分发/网络/视频可行性四页概念展开：[[wiki/concepts/dsh-plugin-development]] · [[wiki/concepts/dsh-plugin-distribution]] · [[wiki/concepts/dsh-plugin-networking]] · [[wiki/concepts/dsh-video-plugin-feasibility]]

## 相关页面

- [[wiki/concepts/plugin-mechanism-comparison]] · [[wiki/entities/obsidian]] · [[wiki/entities/claudian]]
- 能力边界与角色普查 → [[wiki/concepts/dsh-plugin-capability-boundaries]]；框架层收编 → [[wiki/concepts/cordis-vendoring]]；官方 Web 装配流程 → [[wiki/concepts/dsh-frontend-integration]]（"官方 Web 的四层装配"节）

## 依据来源

- [[wiki/sources/dsh-official-docs|dsh 官方文档（32 页快照，2026-08-23）]]——本页要点均已可溯源到 raw（此前仅库外代码仓实读 `/Users/zn-ice/2026/deepseek-harness`，现已剪藏其 docs/）
- 2026-08-25 增补（角色普查、vendoring 细节）来自库外代码仓实读（capability-seams 总表、vendor/README 等），**待剪藏**（登记于来源页后续线索）
