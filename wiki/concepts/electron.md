---
type: concept
created: 2026-09-06
updated: 2026-09-06
sources: []
---

# Electron

**定义**：把 Chromium + Node.js + C++ 胶水层打包成应用运行时的桌面框架；开发者用 JS/TS 写应用层，站在数千万行 C++ 之上。「Web UI + 本地后端 + IPC」骨架的最早工业化实现，与 [[wiki/concepts/tauri|Tauri]] 同构不同料。

## 组成：越往底层越不是 JS

```
你写的应用代码：JS/TS（main.js + 页面）     ← 只有这层是 JS
Electron 框架本体：C++（把下面两个缝起来）
Chromium（C++，渲染引擎）+ Node.js（C++，V8/libuv）
```

所以「Electron 应用是 JS 写的」只对应用层成立——托底重活全是 C++（Tauri 的镜像：托底重活是 Rust）。

## 双进程模型

- **主进程**：全局唯一，入口 main.js，跑在 Node 上，垄断全部系统能力（BrowserWindow、文件、菜单、托盘、通知）
- **渲染进程**：每窗口一个，纯 Chromium 页面，**默认零系统权限**（安全设计）
- **preload 脚本 + contextBridge**：官方安全桥——preload 在页面加载前运行，用 contextBridge 暴露白名单函数；对应 Tauri 的 invoke 命令层
- **IPC**：`ipcRenderer.invoke('read-file')` ↔ `ipcMain.handle()`，两端都是 JS

## 打包与体积

electron-builder / electron-forge 把应用代码 + 整套 Chromium/Node 组装成安装包。**每个应用自带一份运行时**：装 10 个 Electron 应用 = 硬盘里 10 份 Chromium——150MB 级体积的根源，也是 Tauri「借系统 WebView」路线的对照面（见 [[wiki/concepts/tauri-vs-electron]]）。

## 2026 现状

- 生态最成熟：npm 全家桶、前后端同语言、渲染绝对一致（自带 Chromium）；存量霸主，⭐122,902（GitHub API 实测 2026-09-06，2013 年诞生）
- 代价：体积 100-200MB、闲时内存 200-300MB（对比基准〔待剪藏〕）

## 与本库主题的关联（Electron 无处不在）

- **[[wiki/entities/obsidian|Obsidian]] 本体是 Electron 应用**：插件与宿主同进程跑在 Electron renderer、桌面端有完整 Node 能力——本库 [[wiki/concepts/obsidian-plugin-development|Obsidian 插件开发]]、[[wiki/concepts/obsidian-plugin-networking|网络访问]] 页结论的底层前提
- **VSCode** 是 Electron IDE：[[wiki/concepts/plugin-mechanism-comparison|插件机制三方对比]]中"进程隔离 + 受限 API"的样本
- [[wiki/sources/hkuds-videorag-vimo|HKUDS VideoRAG]] 桌面应用也走 Electron（Beta 未发布）
- [[wiki/entities/claudian|Claudian]] 在 Obsidian 里 patch Electron、spawn 子进程——同进程全权玩法的直接受益者

## 已知争议或注意

- 内存/体积被 Tauri 阵营持续攻击，但"渲染一致 + 生态全 + 上手零门槛"使其仍是大团队默认选择（2026 共识分界见 [[wiki/concepts/tauri-vs-electron]]）
- 渲染进程默认零权限 + contextBridge 是现代安全范式；老代码 `nodeIntegration: true` 直通模式是历史包袱

## 依据来源

- 〔待剪藏〕Electron 官方文档（进程模型 / contextBridge / IPC 各页）
- 〔待剪藏〕2026 Tauri vs Electron 基准文章（体积/内存数据）
- 本库佐证：Obsidian 插件系列页（renderer 同进程全权）、dsh 页（Electron 客户端 file:// + IPC 桥）
- star 数据：GitHub API 实测 2026-09-06
