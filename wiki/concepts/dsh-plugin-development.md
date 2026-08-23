---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-dsh-docs-first-plugin.md"
  - "raw/2026-08-23-dsh-docs-build-a-tool.md"
  - "raw/2026-08-23-dsh-docs-plugin-config.md"
  - "raw/2026-08-23-dsh-docs-plugin-lifecycle.md"
  - "raw/2026-08-23-dsh-docs-service-dependency.md"
  - "raw/2026-08-23-dsh-docs-plugin-events.md"
  - "raw/2026-08-23-dsh-docs-capability-roles.md"
  - "raw/2026-08-23-dsh-docs-llm-adapter.md"
  - "raw/2026-08-23-dsh-docs-tool-reference.md"
  - "raw/2026-08-23-dsh-docs-cordis-primer.md"
  - "raw/2026-08-23-dsh-docs-tutorial-index.md"
  - "raw/2026-08-23-dsh-docs-tutorial-07-into-the-harness.md"
---

# deepseek-harness 插件开发

**定义**：在 [[wiki/entities/deepseek-harness|dsh]] 中开发插件的官方路径——插件是导出固定字段的 ES module，经 `cordis.yml` 声明式组合加载；开发期用 `--patch` overlay 即时挂载，无需打包。与 Obsidian 的"文件夹三件套"相对，dsh 的插件开发单元是**纯代码 + 纯配置**。

## 想开发一个插件，看什么文件（官方阅读路线）

| 顺序 | 文档（repo 路径） | 内容 |
|---|---|---|
| ① | `docs/user/develop/basic/index.md` | 第一个插件：模块形态、`--patch` 挂载、自动清理 |
| ② | `docs/user/develop/basic/tool.md` | 用 `defineTool` 注册模型可调用工具 |
| ③ | `docs/user/develop/basic/config.md` | Schemastery 配置 schema |
| ④ | `docs/user/develop/basic/publish.md` | 打包成组合包并安装（见 [[wiki/concepts/dsh-plugin-distribution|分发]]） |
| ⑤ | `docs/user/develop/framework/` | 生命周期 / 服务与依赖 / 事件 |
| ⑥ | `docs/user/develop/practice/` | 能力三角色、LLM 适配器 |
| 参考 | `docs/cordis-tutorial/`（7 章） | Cordis 框架动手教程，每章可运行、无需 API key |
| 参考 | `docs/cookbook/` | 工具参考、权限门禁、conversation node、包清单 |
| 参考 | `docs/cordis-primer.md` + `docs/glossary.md` | 概念速查 + 术语表 |

## 插件的三种形态

```ts
// 函数形式（最常用）
export const name = 'my-plugin'
export const inject = ['tools']        // 声明依赖的服务，就绪后才加载
export function apply(ctx: Context) { /* 注册能力 */ }

// 对象形式：export default { name, inject, apply }
// 类形式：export default class MyService extends Service
```

- `inject` 声明的是**服务**而非具体包——依赖注入由服务可用性驱动，没有 boot 顺序概念。
- 配置：导出 `Config`（Schemastery schema，满足 Standard Schema 接口），加载时校验、失败响亮；官方约定"凡是不同部署可能需要不同值的参数，都必须定义为配置字段"。

## 生命周期（fiber 状态机）

`PENDING → LOADING → ACTIVE →（FAILED）→ UNLOADING → DISPOSED`。要点：

- 所有 `ctx` 注册都是可逆 effect，卸载时**自动**逆序清理（事件监听、工具、定时器）；手动资源用 `ctx.effect(() => cleanup)`。
- 依赖的服务消失（如提供方被替换）→ 插件自动卸载，服务恢复后重载。
- HMR：配置或源码变更 → 卸载旧实例 → 加载新代码，旧注册零残留。

## 工具开发（`defineTool` DSL）

```ts
ctx.tools.register(defineTool({
  name: 'greet',
  description: '…',                       // 模型所见
  parameters: { name: { type: 'string', required: true } },  // 自动校验+类型推导
  output: {
    schema: { type: 'string' },           // execute 只返回规范 JSON 值
    render: (_args, value) => [{ type: 'text', text: value }], // 模型可见内容
  },
  async execute(args) { return `Hello, ${args.name}!` },
}))
```

- 执行身份受保护：参数物化为分离的无损 JSON 并冻结，配不透明 `exec.token`；必须遵守 `exec.signal` 取消。
- 长任务：producer 配置 `run_in_background` + `ctx.jobs.start()`，返回 `{ kind: 'background', jobId }` 句柄。
- UI 卡片：`presentCall` / `presentResult` 返回**封闭枚举**的卡片意图（generic / terminal / diff / search / web）——必须是纯函数（实时流与日志回放双跑），没有自定义卡片类型，富 UI 要走 client 插件（见 [[wiki/concepts/dsh-frontend-integration]] 与 [[wiki/concepts/dsh-video-plugin-feasibility]]）。
- 策略扩展点：`tools/pre-execute`（allow/deny/ask 审批闸门）、`tools/execute`（超时/重试）、`tools/post-execute`、`tools/result`；Code Mode 下每个工具自动成为 `await tools.<name>(args)`。

## 能力三角色（可替换能力的分包模式）

**Service Definition**（定义 `ctx.<key>` 服务与类型）→ **Service Provider**（实现）→ **Consumer**（如把它注册成工具）。范例：`dsh-shell` / `dsh-bash-local`（或 `dsh-bash-sandbox`）/ `dsh-tool-bash`。更换提供方不动定义与工具。官方提醒：不要预防性拆分，单一用途插件保持一个包。

## 与其他概念的关联

- 打包、安装、发布 → [[wiki/concepts/dsh-plugin-distribution]]
- 前端接入（浏览器半与 slots 系统）→ [[wiki/concepts/dsh-frontend-integration]]
- 外部服务调用与动态加载 → [[wiki/concepts/dsh-plugin-networking]]
- 机制对比（vs Obsidian/VSCode）→ [[wiki/concepts/plugin-mechanism-comparison]]
- 宿主 → [[wiki/entities/deepseek-harness]]

## 已知争议或矛盾

- 无（官方文档自洽；developer preview 期 API 会破坏性变更，本文快照锚定 `b150a551`）。

## 依据来源

- [[wiki/sources/dsh-official-docs|dsh 官方文档（32 页快照）]]：user/develop 全部 + cordis-primer + cordis-tutorial + 工具参考
