---
type: concept
created: 2026-09-06
updated: 2026-09-06
sources: []
---

# Tauri

**定义**：用「系统自带 WebView 渲染 UI + Rust 原生二进制做后端」的桌面（v2 起含移动端）应用框架；与 [[wiki/concepts/electron|Electron]] 的本质区别只有一处——浏览器是**借**系统的而非**带**在身上，安装包因此从 150MB 级降到 5-20MB 级。

## 架构：三块拼装

```
前端（平台无关）              Rust 后端（每平台一份）      打包器
React/Vue + Vite build   →   cargo 按平台编译原生二进制  →  tauri-bundler
出纯静态 dist（三平台共用）      （零运行时依赖）             组装各平台安装包
        └────────── 系统 WebView 渲染 UI ──────────┘
        Windows: WebView2（系统自带）/ macOS: WKWebView / Linux: WebKitGTK
```

- **IPC**：前端 `invoke("命令名")`（`@tauri-apps/api/core`）→ Rust `#[tauri::command]`，前后端唯一通信通道（对应 Electron 的 ipcMain/ipcRenderer）
- **权限**：capabilities 文件声明每个窗口可访问的命令/资源（v2 的 ACL）

## Tauri 1 → Tauri 2（同一框架的两代，2024-10 发布 2.0 稳定）

| 维度 | 1.x | 2.x |
|---|---|---|
| 平台 | 仅桌面 | + iOS / Android（最大卖点） |
| 核心 API | 大多内置 | **拆成独立插件**（tauri-plugin-dialog/updater/store/deep-link…），可单独升级 |
| 权限 | allowlist 清单 | capabilities 细粒度 ACL |
| 移动原生代码 | — | Swift/Kotlin 写插件暴露给前端 |

有 breaking changes，官方提供迁移指南；Updater 的 `v1Compatible` 模式将在 v3 移除。

## 框架替你做的 vs 项目自己做的

框架原生提供：一份代码出全平台、系统 WebView 抽象、IPC、权限模型、自动更新插件。项目自己做的只有三件：`#[cfg(target_os)]` 条件编译抹平平台差异（如配置路径 `~/Library` vs `%APPDATA%`）、平台专属打包配置（如 Windows 标题栏专属 conf）、CI 构建矩阵。完整实例见 [[wiki/entities/cc-switch|cc-switch]]（4 个 runner 出三平台产物）。

## 同族与生态位（2026-09 实测）

- **同族**（同为"借系统 WebView"路线）：Wails（Go）、Photino（.NET）、Neutralino（极小 C++ 核 + JS 业务逻辑，反 Tauri 设计）
- **热度**：⭐110,843（GitHub API 实测 2026-09-06）≈ Electron（⭐122,902）的 90%，而项目年轻 6 年；新项目尤其开发者/AI 工具圈的首选之一——cc-switch、Cap、Spacedrive、BiliNote 桌面端（[[wiki/concepts/bilinote-architecture|BiliNote 架构分析]]）
- 体积/内存/权限赢，成熟度/渲染一致性输 [[wiki/concepts/electron|Electron]]（全维度见 [[wiki/concepts/tauri-vs-electron]]）

## 已知争议或注意

- **Linux WebKitGTK** 是最弱一环：有团队迁移 18 个月后报告被 WebKit 兼容性反复折磨（Windows WebView2 / macOS WKWebView 则稳）〔待剪藏：Reddit 案例帖〕
- 移动端支持较新，成熟度低于桌面
- 后端 Rust 的学习曲线/编译时间/招聘池是采用成本（命令层可以写薄，见 cc-switch）

## 与其他概念的关联

- [[wiki/concepts/tauri-vs-electron]]——为什么后端语言被"锁死"成 Rust/C++，分叉树推演在该页
- [[wiki/concepts/self-contained-binaries]]——"零运行时"承诺背后的静态打包实践
- [[wiki/concepts/plugin-mechanism-comparison|插件机制三方对比]]中 Obsidian/VSCode 都是 Electron 宿主；Tauri 生态尚无同量级插件宿主案例

## 依据来源

- 本地实读 [[wiki/entities/cc-switch|cc-switch]]（Tauri 2.8.2 完整实例，`/Users/zn-ice/2026/cc-switch` @ 5315fa28）
- 〔待剪藏〕Tauri 2.0 官方博客 v2.tauri.app/blog/tauri-20/ 与迁移指南 v2.tauri.app/start/migrate/from-tauri-1/
- star 数据：GitHub API 实测 2026-09-06
