---
type: concept
created: 2026-08-23
updated: 2026-09-06
sources:
  - "raw/2026-08-23-obsidian-docs-anatomy-of-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-build-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-development-workflow.md"
  - "raw/2026-08-23-obsidian-docs-manifest.md"
  - "raw/2026-08-23-obsidian-docs-versions.md"
  - "raw/2026-08-23-obsidian-docs-settings.md"
  - "raw/2026-08-23-obsidian-docs-plugin-guidelines.md"
  - "raw/2026-08-23-obsidian-docs-community-directory-faq.md"
---

# Obsidian 插件开发

**定义**：Obsidian 插件是 vault `.obsidian/plugins/<id>/` 文件夹内的三件套（manifest.json + main.js + 可选 styles.css），代码以 `Plugin` 子类的 `onload()`/`onunload()` 生命周期挂进宿主，**与宿主同进程运行于 [[wiki/concepts/electron|Electron]] renderer**（桌面端有完整 Node 能力）。

## 插件解剖：该看什么文件

起步模板：官方 [obsidian-sample-plugin](https://github.com/obsidianmd/obsidian-sample-plugin)（GitHub template repo，可直接派生自己的仓库），clone 到 `.obsidian/plugins/` 下。

| 文件 | 作用 | 要点 |
|---|---|---|
| `manifest.json` | 元数据清单 | 必填 `author`/`minAppVersion`/`name`/`version` + 插件专属 `description`/`id`/`isDesktopOnly`；id 仅小写字母与连字符、不能以 `plugin` 结尾、不能含 `obsidian`；**本地开发时文件夹名必须等于 id**（否则 `onExternalSettingsChange` 等不生效） |
| `main.ts` → `main.js` | 源码与构建产物 | esbuild bundle 成单文件；插件类继承 `Plugin` |
| `styles.css` | 可选样式 | 官方指南要求用 CSS 变量、禁止硬编码内联样式 |
| `versions.json` | 版本回退表 | key=插件版本，value=对应 `minAppVersion`；用户 Obsidian 版本过低时据此选可安装的旧版；仅 `minAppVersion` 变化时才需要更新 |
| `README.md` / `LICENSE` | 提交市场必备 | README 摘要会展示在目录页（相对链接自动改写） |

## 生命周期与开发工作流

- `onload()`：用户开始使用插件时调用，注册全部能力（命令、ribbon、视图、设置页、事件）；`onunload()`：禁用时调用，必须释放资源（事件监听等）。优先用 `registerEvent()`/`addCommand()` 等自动清理方法。
- **永远在专用 vault 开发**（官方警告：一个 bug 就可能毁掉主 vault）。
- 迭代：`npm run dev`（esbuild watch）→ 重新加载插件的三种方式：设置里关/开插件、命令面板 "Reload app without saving"、社区 [hot-reload](https://github.com/pjeby/hot-reload) 插件（文件变化自动重载）。
- `manifest.json` 改动必须重启 Obsidian 才生效。
- 移动端约束：Node.js / Electron API（`fs`、`crypto`、`os` 等）仅桌面可用，用了就必须 `isDesktopOnly: true`；官方建议用 Web API 替代（`SubtleCrypto`、`navigator.clipboard`）。

## UI 接入（前端新手向）

**心智模型**：Obsidian 的整个界面就是一个网页（Chromium 渲染），插件是跑在这个网页里的 JS。"接入界面"不是从零写前端，而是**在 `onload()` 里调用官方注册函数，把界面元素放进预定义插槽**——往装修好的房间里加家具。

**六个官方插槽**：

| 注册函数（写在 onload 里） | 东西出现在哪 | 难度 |
|---|---|---|
| `this.addRibbonIcon('bot', '打开', 回调)` | 左侧边栏图标按钮 | ⭐ 一行 |
| `this.addCommand({...})` | 命令面板（Cmd+P），可绑快捷键 | ⭐ |
| `new Notice('你好')` | 右上角气泡提示 | ⭐ |
| `this.addSettingTab(...)` | 设置窗口里多一个标签页 | ⭐⭐ |
| `this.addStatusBarItem()` | 底部状态栏一格 | ⭐⭐ |
| **`this.registerView(类型, 工厂)`** | **一整个独立面板**（可停靠、可开多个，最"像应用"的入口） | ⭐⭐⭐ |

**最少前端知识（4 个概念，够用到相当复杂）**：

1. **DOM = 积木树**：网页是嵌套方块树，写界面 = 造方块塞进父方块。
2. **`createEl` / `createDiv`**：Obsidian 给容器加的造方块帮手（官方推荐；勿用 `innerHTML` 拼字符串——安全红线见 plugin-guidelines）。
3. **CSS 类**：样式写进 `styles.css`，代码只挂类名，用 `var(--background-primary)` 等 CSS 变量自动适配主题。
4. **事件回调**：`button.addEventListener('click', () => {...})`。

React/Svelte 是界面变复杂后的可选项（官方有指南），起步完全不需要；Claudian 整个聊天界面即纯 DOM + CSS 撑起（库外实读）。

**最小可跑示例**（视频面板，即 [[wiki/concepts/video-player-plugin-feasibility|播放器构想]] 的 L1 骨架）：

```ts
const VIEW_TYPE = 'my-video-player';

class VideoView extends ItemView {
  getViewType() { return VIEW_TYPE; }        // 面板"身份证"
  getDisplayText() { return '我的播放器'; }
  onOpen() {                                  // contentEl 是专属画布
    const video = this.contentEl.createEl('video',
      { attr: { controls: true, width: '100%' } });
    const file = this.app.vault.getFileByPath('path/to/video.mp4');
    if (file) video.src = this.app.vault.getResourcePath(file);
  }
  onClose() { this.contentEl.empty(); }       // 收摊：清空画布
}

export default class MyPlugin extends Plugin {
  async onload() {
    this.registerView(VIEW_TYPE, leaf => new VideoView(leaf));  // ① 注册类型
    this.addCommand({                                          // ② 给入口
      id: 'open', name: '打开视频播放器',
      callback: () => {
        const leaf = this.app.workspace.getRightLeaf(false);
        leaf!.setViewState({ type: VIEW_TYPE, active: true });
      },
    });
  }
}
```

循环：`npm run dev` → 关/开插件重载 → Cmd+P 运行命令。**Cmd+Option+I 开发者工具**可审查 Obsidian 自身 UI（学前端最快的路，官方 anatomy 文档所教）。

**dsh 对照**：同为"插槽"思路，Obsidian 的插槽在 `onload()` 代码里命令式注册；dsh 的 slots 在 client 模块中声明式挂载——见 [[wiki/concepts/dsh-frontend-integration]]。

## 构建与审核的关系

- 构建产物就三个文件：`main.js`、`manifest.json`、`styles.css`——这也是要上传到 GitHub Release 的全部资产。
- 社区目录扫描器会**直接跑仓库里的构建脚本**（顺序 `build` > `build:plugin` > `compile`，取第一个），并做"Build verification"（构建产物与源码一致性校验），所以该脚本必须是生产构建。
- 版本号遵循 semver `x.y.z`，Release tag 必须与 manifest `version` 一致。

## 设置存储

- `Plugin.loadData()` / `saveData()` 读写插件目录 `data.json`——插件配置的官方持久化通道。
- Obsidian 1.13.0+ 提供声明式设置 API（`getSettingDefinitions()`），旧命令式 `display()` 仍受支持（文档给出双支持迁移模式）。

## 与其他概念的关联

- 开发完成后走社区目录分发 → [[wiki/concepts/obsidian-plugin-distribution]]
- 调外部服务、动态下载资源的通道与边界 → [[wiki/concepts/obsidian-plugin-networking]]
- 插件在 Obsidian 架构中的位置（同进程全权模型）→ [[wiki/entities/obsidian]]
- 与 deepseek-harness、VSCode 的机制横向对比 → [[wiki/concepts/plugin-mechanism-comparison]]
- 具体插件实例：[[wiki/entities/claudian]]（agent 工作台）、[[wiki/concepts/video-player-plugin-feasibility|本地视频播放器]]（构想）

## 依据来源

- 本页核心论断依据 [[wiki/sources/obsidian-developer-docs|Obsidian 官方开发者文档]]（anatomy / build-a-plugin / development-workflow / manifest / versions / settings / plugin-guidelines / FAQ 各页）
- **UI 接入一节的溯源**：createEl 替代 innerHTML（plugin-guidelines raw ✓）、DevTools 技巧（anatomy raw ✓）、设置页（settings raw ✓）；**插槽表与 ItemView 示例**来自官方 User interface 系列文档（Commands/Views/Ribbon/Modals/Status bar 等，**未剪藏**——本节按通用知识讲解，待剪藏后复核）
