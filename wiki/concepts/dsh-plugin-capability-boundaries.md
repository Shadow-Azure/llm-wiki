---
type: concept
created: 2026-08-25
updated: 2026-08-25
sources:
  - "raw/2026-08-23-dsh-docs-capability-roles.md"
  - "raw/2026-08-23-dsh-docs-subsystem-web-server.md"
---

# dsh 插件化能力边界

**定义**：[[wiki/entities/deepseek-harness|dsh]] "everything is a plugin" 的精确边界——**形态上**一切能力都经同一套插件机制挂载（连核心服务也是 `cordis.patch.yml` 里的普通配置行，无特权内置）；**可替换性上**分三种角色：seam（实现可整体替换）、core（注册表/载体，只能往里注册、不能换）、bundle（唯一具体实现）。一句话总结："一切皆插件"指挂载形态统一；"实现都可替换"只对 seam 角色成立。

## 角色普查（30 core / 28 seam / 1 bundle）

依据 `docs/capability-seams.zh.md` 末尾总表（库外代码仓实读 2026-08-25，**待剪藏**）：

| 角色 | 数量 | 含义 | 可替换性 |
|---|---|---|---|
| seam | 28 | 接口包 + 并列实现包（即 [[wiki/concepts/dsh-plugin-development\|三角色]] 分包模式） | 换配置行即换实现 |
| core | 30 | 注册表 / 载体 / 协议本身 | 不可换，只能注册扩展 |
| bundle | 1 | agentLoop：唯一的具体循环插件，扩展包依赖其事件与服务而非包本身 | 事实固定脊柱 |

### seam（可替换）代表

| ctx 键 | 官方并列实现 |
|---|---|
| `ctx.llm` | llm-deepseek / llm-pi-ai（多供应商）/ llm-replay |
| `ctx.shell` | bash-local / bash-sandbox / pwsh-local |
| `ctx.fs` | fs-local / fs-sandbox / **fs-e2b**（云沙箱） |
| `ctx.subagents` | spawn/fork-in-process / **subagent-codex / subagent-claude-code** / acp / dsh-sdk |
| `ctx.web` | web-search-exa / perplexity / deepseek + web-fetch-http |
| `ctx.storage` / `ctx.sessionPersistence` | json / sqlite 后端互换 |
| `ctx.sessionTelemetry` | session-telemetry-otel（见下文"打点辨析"） |
| 其余 | skills、settings、credentials、jobs、compaction、approval、sandbox、terminals、spillStore、userQuestions、directoryPicker、codeRuntime、lsp、sessionTitle、sessionQuery、attachments、fileReferences、authorization… |

### core（不可替换）清单要点

`sessions`（会话事件流）· `tools`（工具注册表与执行管线本身）· `systemPrompt`（提示词收集器）· `webServer` · `apiGateway` / `typertGateway` · `typert` · `agents` / `agentDefaultModel` / `agentPresets` · `clientModules`（浏览器启动图）· `sandboxPolicy` / `permissionPresets` · `planMode` / `goals` / `commands` · `sessionProjections` / `sessionProjectionCache` · `tokenMeter` / `toolResultPruner` · `storageDomain` / `workspaceRegistry` / `messageFeedback` / `sessionReferenceResolver` · `shellEnv` / `agentTeams` / `e2b` · `dynamicCordisRunner` / `cordisInspect` · `invariants`。

LSP seam 注：`ctx.lsp` 虽是 seam 但**无协议逃生口**（标准化为 4 种操作，后端必须转换）；workflowEngine 无具名 provider 注册表（每上下文一个引擎）。

## core 的两种动机：公告栏 vs 大门

两个典型 core 剖析（2026-08-25 实读包 README，**待剪藏**），示范脊柱的两类成因：

### ① 系统提示收集器（`ctx.systemPrompt`）——多方共用的公告栏

系统提示不是写死的字符串，而是**每次模型调用前现场拼装**。插件往公告栏"贴卡片"，主循环每步誊写一次：

- **四方贴卡**：收集器自身（固定开头，order -100）→ 部署 persona（order 0，Web 版即 "You are a coding agent…"）→ 各工具插件守则（order 100–199，`tool:bash` 贴 bash 用法、plan-mode 贴计划模式规则…）→ 主循环变量（`{{model}}`、`{{cwd}}`）。
- **工具 schema 同装**："模型被告知能做什么"与提示词同一次拼装（`toolOrder` 也在此配置）。
- **作用域遮蔽**：agent 作用域贴的卡片可遮蔽同名全局卡。
- **严格 fail-loud**：`renderPrompt` 对未知变量引用、畸形 `{{…}}` 直接抛错——宁可失败不发坏提示词。
- **开放注册**：一行 `ctx.systemPrompt.section({ name, order, text })` 即注入；拔掉插件该段自动消失。

### ② HTTP 服务器（`ctx.webServer`）——多方共用的总大门

`dsh web` 监听 `127.0.0.1:3080` 的服务器，README 自述"**不认识任何 harness 概念、不服务任何文件**"——只有大门和走廊、没有房间：

- **路由房间全是别人注册的**：`/api` 桥 → connection 插件；`/plugins/<id>/client.js` → modules 插件；HMR 事件流 → hmr 插件；静态页面 → frontend-static（唯一 fallback）。
- **撞名抛错**：路由表是组合层契约，冲突即配置错误，宁可启动失败。
- **匹配顺序固定**：exact → 最长 prefix → fallback；注册顺序无请求语义。
- **克制即设计**：无 TLS/鉴权/源策略；host 只接受 `127.0.0.1` 与 `0.0.0.0`（v1 有意留给反向代理）。

**两类动机对照**：N 个插件要**往同一内容贡献碎片** → 中央收集器（systemPrompt）；N 个插件要**共用同一入口** → 中央载体（webServer）。两者都是"协议本身"——这就是 core 与 seam 的分界。

## "能拔" ≠ "拔了还能用"

配置层面可以 disable 任何行（含 core 行），loader 照办。但依赖该服务的插件声明了 `inject`，服务永不出现 → 这些 fiber **永远停在 pending**，产品残缺。core 的"不可替换"不是被机制禁止，而是**换了就没有消费者能用**。

## 脊柱在哪：两层 + 宿主

不可替换部分分三块，cordis 只占一块（详见 [[wiki/concepts/cordis-vendoring]]）：

| 层 | 位置 | 内容 | 作者 |
|---|---|---|---|
| 机制层 | `vendor/`（9 包） | 插件运行时本身：加载器、fiber 生命周期、DI、事件总线 | Cordis 上游（Shigma），DeepSeek vendor |
| 领域层 | `packages/core`、`packages/host` 等 | 上文 30 个 core 角色服务 | **DeepSeek 自己** |
| 宿主引导 | `apps/cli` 等 | profile 分层启动、CLI 入口 | DeepSeek 自己 |

脊柱的大部分面积是 dsh 自写领域代码；cordis 只提供"插座系统"。

## 打点辨析：上报是 seam（默认关），计量是 core（不出进程）

- **`ctx.sessionTelemetry`（上报类打点）是标准 seam**：接口包（契约+捕获协调器）+ otel 后端；base bundle 默认 `mode: DISABLED`，`DSH_TELEMETRY_DISABLED` 由启动器 patch 成禁用，**整行不装也能跑**（启动代码明言 "custom profiles need not mount telemetry"）。
- **依赖方向反转**：不是核心里埋 `telemetry.report()` 调用，而是核心（`ctx.sessions`）对外发持久会话事件流，打点插件只是**订阅者**（`session/created`、`session/event`…）——核心不知道打点存在。脱敏是 `sessionTelemetry/record` waterfall，接口包**零内置规则**（无规则时数据原样出进程，规则由部署方挂）。
- **`ctx.tokenMeter`（token 计量）是 core**——但属进程内记账（供 UI 显示与上下文压缩），输出不离开进程。分界线即 capability 表原文：sessionTelemetry 的"输出会离开当前进程"。

## 与其他概念的关联

- 三角色分包模式（seam 的设计方法）→ [[wiki/concepts/dsh-plugin-development]]
- 机制层 vendor 细节 → [[wiki/concepts/cordis-vendoring]]
- 官方 Web 如何用这些插件装配出来 → [[wiki/concepts/dsh-frontend-integration]]（"官方 Web 的四层装配"节）
- 机制对比中 dsh 列的精化 → [[wiki/concepts/plugin-mechanism-comparison]]
- 宿主 → [[wiki/entities/deepseek-harness]]

## 已知争议或矛盾

- 与 [[wiki/concepts/plugin-mechanism-comparison]] 的"任何插件行（含 agent loop）可被 patch 替换 = 设计目标"及"无特权内核"表述：**非事实矛盾**（patch 整行替换确是文档承诺的机制），但精度需提升——30 个 core 无并列实现、agentLoop 无第二实现，"可 patch"≠"有得换、换了能用"。已在两处标记（铁律 7：标记不裁决）。

## 依据来源

- raw：[[wiki/sources/dsh-official-docs|dsh 官方文档]] 之 capability-roles（三角色概念）、subsystem-web-server（webServer 契约细节）
- 库外代码仓实读 `/Users/zn-ice/2026/deepseek-harness`（2026-08-25）：`docs/capability-seams.zh.md`（30/28/1 普查总表）、`packages/core/system-prompt/README.md`、`packages/session/session-telemetry/README.md`、`packages/bundle/base/cordis.patch.yml`、`apps/cli/src/profile-boot.ts`——均**待剪藏**（已登记来源页后续线索）
