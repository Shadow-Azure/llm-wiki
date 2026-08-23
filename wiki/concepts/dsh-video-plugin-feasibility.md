---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-dsh-docs-conversation-node.md"
  - "raw/2026-08-23-dsh-docs-subsystem-client-modules.md"
  - "raw/2026-08-23-dsh-docs-subsystem-web-server.md"
  - "raw/2026-08-23-dsh-docs-subsystem-attachment.md"
  - "raw/2026-08-23-dsh-docs-tool-reference.md"
  - "raw/2026-08-23-dsh-docs-subsystem-extensions.md"
---

# dsh 视频播放插件可行性

**定义**：在 [[wiki/entities/deepseek-harness|deepseek-harness]] 内做视频播放插件的可行性结论——**Web UI 内完全可行且有官方扩展点支撑**（client 插件渲染 React `<video>` 进聊天流 + `webServer` 自建路由喂本地文件）；CLI/headless/TUI profile 不可行（无 UI）；且 dsh 独有一条 **agent 动态生成播放器插件**的路线。

## 三条渲染路线的判定

| 路线 | 判定 | 依据 |
|---|---|---|
| 工具 UI 卡片 | ❌ 不可行 | `presentCall/presentResult` 是**封闭枚举**（generic/terminal/diff/search/web），无 video 卡片类型，卡片还要求纯函数（禁止 I/O 与状态） |
| **client 插件 conversation node** | ✅ 主路线 | `ConversationNodeDefinition` + `ctx.slots.inject('conversation.chat.node', …)` 注册 keyed React renderer，示例即 `createElement('p', …)`——任意 React 元素（含 `<video controls>`）合法；事件族 `video/start…` 驱动，回放/分页/实时三路径自动工作 |
| 动态插件（`dynamicCordisRunner`） | ✅ 进阶路线 | 模型经 `dsh-tool-cordis` 现场写播放器插件、define+run（client 半经审批激活）；`examples/web-cordis` 为官方同型自指示例 |

## 视频数据的来源通道

1. **本地文件 → 自建路由**：host 半 `ctx.webServer.register({ kind: 'prefix', path: '/my-video', handler })`，handler 拥有完整响应生命周期（可 hold open，如 SSE）——自己实现文件流 + HTTP Range（206）即可喂给 `<video src="/my-video/xxx.mp4">`。〔Range 细节为 AI 推断：文档只保证 handler 全权拥有响应，未逐字承诺 Range 语义，动手时需验证〕
2. **外部 URL 直连**：`<video src="https://…">` 浏览器原生播放；未发现 CSP 约束文档〔AI 推断，待实验验证〕。host 半也可 `fetch` 任意视频 API。
3. **内置附件 seam ❌**：v1 只收 `image/png|jpeg|webp|gif` 四种格式，视频不能走 `ctx.attachments`（内容寻址、先持久化后事件的模式倒是值得仿照）。

## 关键架构事实（来自官方文档）

- client 插件声明在 package.json 的 `dsh.client`（platform: 'web'），浏览器 bundle 经 `/plugins/<id>/client.js` 提供，按需 lazy 拉取——视频 UI 代码本身也是动态加载的。
- conversation node 引擎约定：renderer 只消费 `node.data` 与受限 Location hook，不扫描事件窗口——播放器状态（进度、章节）应放进 node data / Location data，遵循 keyed 稳定身份。
- Electron 客户端走 `file://` + IPC 桥（不经 webserver），路由方案在 Electron 下的行为待验证〔AI 推断，待实验〕。
- profile 差异：`web`（浏览器 UI）可行；`headless`（无 UI 无端口）、TUI bundle（如 turtle-ui）无视频渲染面。

## 与 Obsidian 视频插件的对照

[[wiki/concepts/video-player-plugin-feasibility|Obsidian 版可行性]]（demo 已验证）是"人写插件、Electron renderer 全权"；dsh 版多出两层玩法：

- **agent 原生**：模型可在会话里动态生成/挂载播放器插件（审批闸门把守），"转写→内容地图→批注"闭环可以直接由 agent 组装，而不必预先装好插件。
- **协议化回放**：视频节点作为持久 session 事件族存在，回放/分页语义由引擎统一保证——Obsidian 插件要自己解决的问题在 dsh 是框架给的。

功能阶梯可平移：L1 基础播放（conversation node + 自建路由）→ L2 字幕/章节（node data）→ L3 AI 转写（host 半 spawn whisper + 工具/事件驱动）→ L4 编解码硬骨头与 Obsidian 相同（HEVC 等，浏览器解码能力一致）。

## 建议路径

先读 `docs/cookbook/adding-a-conversation-node.zh.md`（233 行，含完整代码）→ 用 `--patch` overlay 挂最小 `<video>` node 验证渲染 → 加 `webServer` prefix 路由喂本地 mp4 → 再决定走静态 bundle 分发还是 `dsh-tool-cordis` 动态路线。前端接入全貌（两半架构/slots 纪律/打包坑）见 [[wiki/concepts/dsh-frontend-integration]]。

## 依据来源与后续线索

- [[wiki/sources/dsh-official-docs|dsh 官方文档]]：conversation-node / client-modules / web-server / attachment / extensions / 工具参考
- 待验证项均已在文中标注"AI 推断"：Range 流式细节、CSP/外部源、Electron IPC 下路由行为
- 后续线索：官方 discussion #961（arXiv 插件实战帖）演示了完整开发→发布流程，可作为第二个实例来源剪藏
