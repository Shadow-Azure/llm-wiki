---
type: entity
created: 2026-09-06
updated: 2026-09-06
sources: []
---

# cc-switch

**身份一句话**：Claude Code / Codex / Gemini CLI / OpenCode / OpenClaw / Hermes Agent 六款 AI CLI 的全方位管理工具（供应商切换、MCP、prompt、用量统计、本地代理），[[wiki/concepts/tauri|Tauri]] 2 桌面应用，GitHub `farion1231/cc-switch`，⭐131,292（GitHub API 实测 2026-09-06，已超过 Tauri 框架本体）。

## 背景

- 2025 年开源，增长极快；分发覆盖 Windows / macOS / Linux（含 arm64）+ 应用内自动更新（tauri-plugin-updater）
- 本地源码：`/Users/zn-ice/2026/cc-switch`（HEAD @ 5315fa28，实读 2026-09-06）

## 架构实读（2026-09-06，本地仓实测）

**语言构成**：Rust ~106,220 行（201 文件，58%）+ TypeScript/React ~77,154 行（345 文件，42%）——核心逻辑几乎全在 Rust，TS 只做 UI。

**分层**：

| 层 | 位置 | 职责 |
|---|---|---|
| 前端 | `src/` | React 18 + Vite + Tailwind + TanStack Query；`lib/api/` 25 个文件全是 `invoke()` 薄封装（前后端唯一通道） |
| IPC 命令 | `src-tauri/src/commands/` | 33 个命令模块，`lib.rs` `generate_handler!` 统一注册 |
| 业务逻辑 | `src-tauri/src/services/` | 供应商 CRUD、用量统计、OAuth、测速、WebDAV 云同步 |
| CLI 配置读写 | `src-tauri/src/*_config.rs` | 对 6 款 AI CLI 真实配置文件（如 `~/.claude/settings.json`）的读写器——**切换供应商 = 改写这些文件** |
| 本地代理 | `src-tauri/src/proxy/` | 最重子系统（28 文件）：axum + hyper 反向代理，故障转移/断路器/模型映射/SSE 流处理/用量统计 |
| 数据库 | `src-tauri/src/database/` | SQLite（rusqlite bundled），DAO + 迁移 + 备份 |

**跨平台**：CI 矩阵 windows-2022 / ubuntu-22.04(+arm64) / macos-14，`targets: "all"`；Windows 用 wix per-user 安装器，Linux 另有 flatpak。平台差异靠 `#[cfg(target_os)]` 条件编译（lib.rs 16 处、auto_launch.rs 6 处、tray.rs 4 处）。

**值得记住的工程细节**：TLS 全栈选 rustls（reqwest `rustls-tls`、tokio-rustls、hyper-rustls）而非 openssl——为静态打包扫清最大障碍，见 [[wiki/concepts/self-contained-binaries|自包含二进制分发]]。

## 与本库主题的关联

- 用户 AI 工具链的组成部件（管理 Claude Code 等供应商配置）
- 本库首个 [[wiki/concepts/tauri|Tauri]] 2 深度实读样本：插件化（tauri-plugin-*）与 capabilities 权限系统的完整实例
- 「配置文件即接口」的代表：不侵入 CLI 本体，靠改写各家配置文件实现管理（与侵入式 wrapper 路线的对照，[[wiki/concepts/yt-dlp|yt-dlp]] 是同思路的取数侧实例）

## 相关页面

- [[wiki/concepts/tauri]] / [[wiki/concepts/tauri-vs-electron]] / [[wiki/concepts/self-contained-binaries]]
- [[wiki/entities/obsidian|Obsidian]]（对照组：Electron 阵营的本地优先工具）

## 依据来源

- 本地源码实读 `/Users/zn-ice/2026/cc-switch` @ 5315fa28（find/wc/grep 实测，2026-09-06）
- GitHub API star 实测（2026-09-06）
- 仓库内 README_ZH.md、`.github/workflows/release.yml`、`src-tauri/tauri.conf.json`
- 〔待剪藏〕GitHub 仓库主页 farion1231/cc-switch
