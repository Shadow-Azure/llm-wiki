---
type: concept
created: 2026-08-23
updated: 2026-08-25
sources:
  - "raw/2026-08-23-obsidian-docs-anatomy-of-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-build-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-manifest.md"
  - "raw/2026-08-23-obsidian-docs-developer-policies.md"
  - "raw/2026-08-23-obsidian-docs-requesturl.md"
---

# 插件机制三方对比（Obsidian / deepseek-harness / VSCode）

**定义**：三种宿主（笔记应用 / agent 运行框架 / IDE）的插件机制代表三种扩展性设计——**同进程全权**（Obsidian）、**无特权内核**（deepseek-harness）、**进程隔离 + 受限 API**（VSCode）。

## 对比表

| 维度 | Obsidian | deepseek-harness | VSCode |
|---|---|---|---|
| 宿主定位 | Electron 笔记应用 | Node agent 运行框架 | Electron IDE |
| 插件形态 | 文件夹：manifest + main.js (+css) | ES module：name/inject/Config/apply | npm 包：package.json + main/browser 入口 |
| 清单声明内容 | 纯元数据，**无能力声明** | 服务依赖 + 配置 schema | **能力合同**：contribution points + activation events |
| 发现/组合 | 目录安装 / 社区目录分发 | 声明式 YAML 配置树（patch 层叠） | Marketplace / VSIX |
| 加载时机 | 启用即全量加载 | 服务可用性驱动（依赖注入） | 激活事件惰性加载 |
| 运行位置 | 与宿主**同 renderer 进程**（Node 集成） | 与宿主**同 Node 进程** | **独立 Extension Host 进程**（local Node / web WebWorker / remote），`extensionKind` 路由 |
| API 模式 | 继承 Plugin + 命令式注册；文档化 API 外有内部 API 逃逸舱 | `ctx.*` 服务容器 + 类型化事件四模式 | 严格 `vscode` 命名空间，**无 DOM 访问** |
| 能否改 UI | 一等能力（同 DOM） | web profile 有正式扩展点：conversation node / slots / settings card / `dsh.client` 浏览器半；核心无 UI 概念 | 不能，只能声明贡献点由工作台渲染 |
| 能否换核心 | 事实上能（内部 API），无官方支持 | **设计目标**：任何插件行（含 agent loop）可被 patch 替换† | 不能 |
| 故障隔离 | 无：插件崩→宿主崩 | 无进程隔离；靠可逆 effect + HMR | 进程级：官方明言防扩展拖慢启动、阻塞 UI、修改 UI |
| 卸载语义 | `onunload` 手动清理（约定） | effect 自动逆序回滚（机制保证） | `deactivate` + dispose（约定） |
| 安全/信任 | 信任 + 目录审核 | 信任；审批/沙箱本身是插件 | 进程隔离是**稳定性**边界而非安全边界——本地扩展在 host 内仍有全 Node 权限 |

> † **精化（2026-08-25，代码仓实读）**：patch 整行替换确是文档承诺的机制，但 30 个 core 角色服务（会话、工具注册表、提示收集器…）无并列实现，agentLoop 是唯一具体循环——"可 patch"≠"有得换、换了能用"。形态统一、脊柱固定。详见 [[wiki/concepts/dsh-plugin-capability-boundaries]]。

## 五条洞察（AI 综合；各列事实来源见下方溯源说明）

1. **声明式程度谱系：VSCode > dsh > Obsidian**。VSCode 把能力写进静态可扫描清单（因此能惰性激活、多 runtime 路由）；dsh 用 YAML 配置树声明组合（组合本身是数据）；Obsidian 的 manifest 只是名片。代价对偶：Obsidian 宿主无法静态分析，永远无法惰性加载。
2. **扩展的"对象"不同：空间 vs 流程 vs 插槽**。Obsidian 扩展宿主的**空间**（UI+数据，任意深）；dsh 扩展**流程**（waterfall 短路权即决策权，agent loop 可替换）；VSCode 扩展宿主预定义的**插槽**（插槽之外无路）。dsh 的"无特权内核"是最激进的去中心化（2026-08-25 精化：指挂载形态无特权；能力角色上仍有 30 个 core 固定脊柱，见 [[wiki/concepts/dsh-plugin-capability-boundaries]]）。
3. **隔离换自由**。VSCode 用独立进程+受限 API 买启动性能、UI 流畅、UI 不可破坏，牺牲深度改造宿主的能力；Obsidian/dsh 选同进程全权——Claudian 正靠这种全权才能 patch Electron、spawn 子进程、在笔记软件里再养一层插件生态（"生态桥"玩法）。注意 VSCode 进程隔离常被误解为安全边界：本地扩展 `require` 不受限，买到的是**稳定性**而非**安全性**——三者对本地代码最终都是"装了即信任"。
4. **生命周期哲学各不相同**：VSCode 惰性激活为**性能**；dsh 服务依赖驱动为**组合正确性**（boot 顺序概念消失）；Obsidian 启用/禁用为**简单性**。
5. **审批机制的位置暴露系统本性**：Claudian 的审批长在**聊天气泡**（UI 层接管 `canUseTool`）；dsh 长在 **`tools/pre-execute` waterfall**（插件化闸门）；VSCode 没有"工具审批"概念——它扩展的是编辑器不是会执行危险操作的 agent。**agent 时代的 harness 需要管线中的否决权，这是传统 IDE 插件模型未考虑的扩展点。**

## 溯源说明

- **Obsidian 列**：raw 支撑（上方 sources；生命周期/manifest/分发政策/同进程模型均见 [[wiki/concepts/obsidian-plugin-development]] 等三页）
- **deepseek-harness 列**：官方文档 32 页已剪藏落库（[[wiki/sources/dsh-official-docs]]）；展开见 [[wiki/concepts/dsh-plugin-development]] / [[wiki/concepts/dsh-plugin-distribution]] / [[wiki/concepts/dsh-plugin-networking]] / [[wiki/concepts/dsh-frontend-integration]] / [[wiki/concepts/dsh-plugin-capability-boundaries]]（† 精化的依据）
- **Claudian 实例**：库外代码仓实读 `/Users/zn-ice/2026/claudian`，详见 [[wiki/entities/claudian]]
- **VSCode 列**：官方文档——[Extension Host](https://code.visualstudio.com/api/advanced-topics/extension-host)、[Activation Events](https://code.visualstudio.com/api/references/activation-events)、[Contribution Points](https://code.visualstudio.com/api/references/contribution-points)，**待剪藏**

## 后续线索

- 剪藏 VSCode 三页官方文档为 raw（对比表 VSCode 列即可全溯源）
- ~~剪藏 dsh 的 architecture.md / cordis-primer.md~~（已完成，2026-08-23 research）
- ~~Cordis 框架源自 Koishi 生态〔AI 推断，待来源验证〕~~（**已验证 2026-08-25**：上游 cordis 作者 Shigma 即 Koishi 作者，上游 MIT——见 [[wiki/concepts/cordis-vendoring]]）

## 关联

- 实体：[[wiki/entities/obsidian]] · [[wiki/entities/claudian]] · [[wiki/entities/deepseek-harness]]
- Obsidian 侧展开：[[wiki/concepts/obsidian-plugin-development]] · [[wiki/concepts/obsidian-plugin-distribution]] · [[wiki/concepts/obsidian-plugin-networking]]
- 背景：[[wiki/concepts/note-tool-landscape]]
