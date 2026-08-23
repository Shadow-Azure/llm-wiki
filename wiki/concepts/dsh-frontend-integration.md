---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-dsh-docs-settings-card.md"
  - "raw/2026-08-23-dsh-docs-conversation-node.md"
  - "raw/2026-08-23-dsh-docs-subsystem-client-modules.md"
  - "raw/2026-08-23-dsh-docs-subsystem-web-server.md"
---

# dsh 插件前端接入

**定义**：[[wiki/entities/deepseek-harness|dsh]] 插件接入 Web 界面的官方方式——插件包带一个"浏览器半"（`src/client/` + `dsh.client` 声明），通过**插槽（slots）系统**把 React 组件插进 Web GUI 的预留孔位；UI 组装有且仅有这一条通道。直觉模型：插件 = 后厨（宿主半，Node，干活）+ 前厅（浏览器半，React，显示）。

## 两半架构与自动发现

- 一个包内：`src/`（宿主半）+ `src/client/`（浏览器半），`exports["./client"]` 导出，`package.json` 声明 `"dsh": { "client": { "platform": "web", "inject": […] } }`。
- **挂载即被发现**：客户端模块系统扫描配置树中声明 `dsh.client` 的包，bundle 经 `/plugins/<id>/client.js?rev=<内容哈希>` 提供给页面（lazy 按需拉取，`immediately` 行启动期预取），**无需重建 Web 应用**（settings-card 食谱原文）。
- 开发期：`pnpm dsh web --patch ./overlay.yml` 挂载 + 单独跑 `pnpm run dev:web` watcher（HMR 接收器常驻，bundle 重建经 SSE 广播到浏览器）。
- 参考包：`packages/client/ui-theme`（两半打包现成例子）。

## 五个接入点

| 接入点 | 机制 | 适合 |
|---|---|---|
| ① 聊天流业务节点 | `ConversationNodeDefinition` + `ctx.slots.inject('conversation.chat.node', …)` keyed React renderer | 在对话里渲染自己的行（播放器/图表）；数据走持久 session 事件族，回放/分页/实时由引擎保证 |
| ② 设置卡片 | 宿主半 `installSettingsSection(ctx, 命名空间, …)` + 浏览器半注册进 `settings.plugin.item`，**按命名空间自动配对**（host 服务了哪个命名空间，对应卡片才渲染） | 插件配置界面；`role('secret')` 字段值不回显，`applies: 'restart'` 标注需重启生效 |
| ③ 其他具名插槽 | 同一 slots 系统的其他键（见下） | 输入区/头部/详情等 |
| ④ 工具结果卡片 | `presentCall/presentResult` | 封闭枚举（generic/terminal/diff/search/web），纯函数约束——**不是自由 UI** |
| ⑤ 自建页面 | 宿主半 `ctx.webServer.register({ kind: 'prefix', … })` 服务任意 HTML | 逃逸舱：不进 Web App 的 React 树，整页自治 |

## slots 系统纪律（`packages/client/AGENTS.md`，库外代码仓实读，待剪藏）

1. **唯一组装 API**：`ctx.slots.register({ name, children?, store?, inject? }, Component)`；shell 独占渲染 `'root'`。
2. **children = 声明 + 授权**：组件渲染的子插槽必须写在注册的 `children` 里；渲染未声明的、或声明别人已占的，**加载即失败**（冲突是设计在说话）。插槽命名 `<domain>.<entry>.<hole>`。
3. **组件永远见不到 `ctx`**：只收四份额派生 props（runtime/slots/store/inject）；活数据只经框架造的 hooks（`useSession`/`useStore`/`renderSlot` 等）。
4. **跨插件禁止值导入**：协作只走 slots 与 cordis services；bundle 纯净度门禁强制。
5. **数据访问阶梯**：框架 hooks → 声明的 store（读 `props.useStore` 写 `props.actions.*`）→ inject 回调；业务组件零订阅机制、零 React context。

## 具名插槽清单（源码 grep 枚举，2026-08-23；非文档承诺，preview 期会变）

`conversation.chat.node`（聊天流节点，用例最多）· `conversation.composer` / `conversation.composer.bar`（输入区）· `conversation.input.overlay` / `input.attachments` / `input.model` / `input.plan` / `input.dock` · `conversation.view` · `conversation.session.header`（+ `.actions` / `.lineage`）· `conversation.details.tool`（工具详情）· `conversation.hero.workspace` / `hero.brand.mark` · `conversation.message.images` · `settings.section` / `settings.plugin.item` / `settings.onboarding`

## 打包与开发的坑

- **bundle 格式**：必须是 loader 的 **lazy-CJS factory** 产物；官方 tsdown 预设（`clientBundle`）**尚未发布**——仓库外作者需自行复刻输出格式（settings-card 食谱明言），初学者照抄社区现成项目配置最稳。
- **基线隐式外部化**：React / Cordis / runtime / ui-primitives / ui-slots 是共享基线，不写进 manifest；`dsh.client.external` 只用于额外非基线共享请求（同步 require，不可等待，拒绝环）。
- **样式**：共享 `--dsh-*` 设计令牌 + CSS Modules，无组件库、无 Tailwind（`docs/web-styling.md` 为准，待剪藏）。
- **依赖声明**：Cordis 保持 peer + dev 匹配；动态包不把 `@deepseek-ai/dsh-*` 放 dependencies；发布载荷闭合（`files` 覆盖全部运行时导入与产物）。

## 示例接线（视频插件最小形态）

```
宿主半：ctx.webServer.register 前缀路由喂 mp4 + 发 'video/start…' 事件族进 session 日志
浏览器半：ctx.conversationEvents.register(videoDefinition)
         + ctx.slots.inject('conversation.chat.node', …, VideoNodeView)  // createElement('video', …)
```

展开见 [[wiki/concepts/dsh-video-plugin-feasibility]]。

## 与其他概念的关联

- 上游：[[wiki/concepts/dsh-plugin-development]] / [[wiki/concepts/dsh-plugin-distribution]]（浏览器半的发现/加载机制见 [[wiki/concepts/dsh-plugin-networking]]）
- 应用：[[wiki/concepts/dsh-video-plugin-feasibility]]；对照 [[wiki/concepts/obsidian-plugin-development]]（Obsidian 的 ItemView/DOM 全权 vs dsh 的受限插槽——两类"同进程全权"宿主在 UI 层的不同选择）
- 宿主 → [[wiki/entities/deepseek-harness]]

## 已知争议或矛盾

- 无库内矛盾。注意：插槽清单来自源码枚举而非文档承诺，developer preview 期可能增删。

## 依据来源

- [[wiki/sources/dsh-official-docs|dsh 官方文档]]：settings-card / conversation-node / client-modules / web-server 四页（raw 支撑）
- `packages/client/AGENTS.md`（slots 纪律、依赖声明、分层红线）与 `docs/web-styling.zh.md` 为库外代码仓实读，**待剪藏**（已登记 [[wiki/sources/dsh-official-docs]] 后续线索）；插槽清单为源码 grep 结果（同上标注）
