---
type: concept
created: 2026-09-06
updated: 2026-09-06
sources: []
---

# Tauri vs Electron

**定义**：桌面应用「Web UI + 本地后端 + IPC」骨架的两大范式之争——[[wiki/concepts/tauri|Tauri]] 把浏览器**借**自系统，[[wiki/concepts/electron|Electron]] 把浏览器**带**在身上。骨架上两者同构（本地后端 + IPC + 安全桥），**本质区别只有一处：浏览器带不带**，其余差异都是这一处的下游。

## 对比矩阵（2026-09 实测与检索）

| | Electron | Tauri |
|---|---|---|
| UI 引擎 | 自带 Chromium（每 app 一份） | 系统 WebView（借用 OS） |
| 后端 | Node.js（JS） | Rust |
| 前后端通信 | ipcMain/ipcRenderer（JS↔JS） | invoke 命令（TS↔Rust） |
| 安全桥 | preload + contextBridge | capabilities 权限文件 |
| 安装包体积 | 100-200MB | 5-20MB（基准：小 ~96%） |
| 闲时内存 | 200-300MB | 30-40MB（基准：低 ~75%） |
| 生态 | npm 全家桶、前后端同语言 | crates.io 核心件够用 + 前端照用 npm |
| 渲染一致性 | 绝对一致（自带 Chromium） | 依赖宿主 WebView 版本 |
| 移动端 | 无 | v2 官方支持 iOS/Android |
| 代表应用 | VS Code、Slack、Discord、Obsidian | cc-switch、Cap、Spacedrive、BiliNote 桌面端 |
| GitHub ⭐（实测） | 122,902（2013 生） | 110,843（2019 生） |

## 论证一：为什么 Tauri 后端被"锁死"成 Rust/C++（不是偏爱，是体积约束）

```
后端语言能编译出零依赖原生可执行文件吗？
├─ 能（Rust/C++/Go/Zig/C# AOT）→ 借系统 WebView，5-20MB（Tauri / Wails / Photino）
└─ 不能（Node/Python 等解释型）→ 运行时必须随包携带（Node 60-80MB）
   → 已经半个 Electron → 干脆连 Chromium 一起带，换渲染一致 + 生态全
   → 重新发明完全体 Electron
```

「不带运行时」这个目标几乎唯一地锁死了可编译原生二进制的语言；选 Rust 而非 C++ 叠加三项加分：内存安全（IPC/不可信输入场景，Chromium 自己也在引入 Rust）、无 GC 并发（tokio，适合本地代理/SSE 长流）、类型系统契合 capabilities 权限模型。代价：学习曲线陡、编译慢。

## 论证二：中间形态为什么没成为主流

「Node 后端 + 系统 WebView」混合形态存在（pywebview、Node WebView 绑定），体积 ~80-100MB：比 Electron 小一半、仍是 Tauri 的 5-10 倍——两头不占，始终 niche〔AI 推断，待来源验证〕。反向证明是 Neutralino（极小 C++ 核暴露本地 WebSocket API、业务逻辑全在 WebView JS）：「JS 写后端逻辑」可行，但重底座终究要原生语言写——殊途同归。另一个细节：Node 借 WebView 本身就需要 N-API 原生扩展去调 C/C++ API，原生胶水层无论如何躲不掉。

## 论证三：2026 年的共识分界

**Electron 赢在成熟度与渲染一致性，Tauri 赢在体积、内存与权限控制。**工具型/AI CLI 配套应用（管理本地配置 + 轻 UI + 本地服务）几乎清一色 Tauri；复杂前端、大团队、跨宿主渲染零差异需求的仍选 Electron。Tauri 最大软肋：Linux WebKitGTK 兼容性、移动端成熟度；Electron 最大软肋：资源占用。

## 与其他概念的关联

- [[wiki/concepts/self-contained-binaries|自包含二进制分发]]——「借 vs 带」在打包层的延伸（依赖消灭 vs 依赖搬家）
- [[wiki/concepts/plugin-mechanism-comparison|插件机制三方对比]]——Electron 宿主（Obsidian/VSCode）的插件架构对比
- [[wiki/entities/cc-switch|cc-switch]]——Tauri 2 完整实读样本

## 已知争议或注意

- 体积/内存基准数字（96%/75%）来自第三方 2026 对比文，测量口径不一：量级可信、精确值存疑〔待剪藏〕
- Reddit「迁移 18 个月被 WebKit 折磨」反面案例与 Tauri 整体好评并存——Linux 依赖重的项目需自评〔待剪藏〕

## 依据来源

- 本地实读 [[wiki/entities/cc-switch|cc-switch]]（Tauri 2 样本）与 [[wiki/concepts/bilinote-architecture|BiliNote]]（Tauri 桌面端）
- GitHub star：API 实测 2026-09-06
- 〔待剪藏〕tech-insider.org 2026 对比文、buildmvpfast.com Tauri v2 vs Electron、PkgPulse 2026 框架综述、Reddit r/javascript WebKit 案例帖
