---
type: entity
created: 2026-08-23
updated: 2026-08-23
sources: []
---

# Claudian

**一句话身份**：把 Claude Agent SDK 嵌入 Obsidian 的桌面插件（manifest id `realclaudian`，`isDesktopOnly: true`）——vault 内的 agent 工作台；**本库的维护会话即运行于其中**（LLM Wiki 的 agent 侧）。

## 背景与实现要点

※ 本节基于库外代码仓实读 `/Users/zn-ice/2026/claudian`（2026-08-23），README 待剪藏。

- **构建**：esbuild bundle 成单 `main.js`；`obsidian`/`electron`/Node 内置模块 external；自定义 esbuild 插件把 SDK 的 `createRequire(import.meta.url)` 改写为 `__filename`——"Electron renderer 里跑 CJS 化 Node 代码"的痕迹。
- **运行模型**：在 renderer 进程内 import Claude Agent SDK（bundle 进 main.js），`query()` 经 `child_process.spawn` 拉起 Claude CLI 子进程；常驻温进程池；`settingSources` 载入 Claude Code 配置（user/project/local）。
- **注册面**：`registerView` ×2、`addRibbonIcon`、`addCommand` ×9、`addSettingTab`、vault 事件监听——标准 Obsidian 命令式注册（见 [[wiki/concepts/obsidian-plugin-development]]）。
- **权限层**：给 SDK 传 `allowDangerouslySkipPermissions: true`，改由自身 `canUseTool` + 规则引擎（精确/通配模式匹配）+ **聊天气泡内联批准卡**接管审批；`normal / plan / yolo` 三种权限模式，批准可转持久权限更新。
- **"插件里养插件"**：skills（`.claude/skills/`、自有 `.agents/skills/`）、subagents（`.claude/agents/`）、Claude Code plugins（`installed_plugins.json`）、MCP（经 SDK settingSources）——把 Claude Code 生态以文件系统约定的形式整体桥接进 Obsidian。这是 Obsidian 同进程全权模型才可能的"生态桥"玩法（见 [[wiki/concepts/plugin-mechanism-comparison]] 洞察 3）。

## 与本库主题的关联

- 本库 = Claudian（agent 侧）+ Obsidian（人类侧）+ 本地文件系统（wiki 层）的实例化，对应 CLAUDE.md 的心智模型"Obsidian 是 IDE，LLM 是程序员，wiki 是代码库"。
- 其聊天气泡审批是"agent 时代审批长在 UI 层"的代表实例（三方对比洞察 5）。

## 相关页面

- [[wiki/entities/obsidian]] · [[wiki/entities/deepseek-harness]]（另一种"agent × 宿主"组合：dsh 自建宿主，Claudian 寄生 Obsidian）
- [[wiki/concepts/plugin-mechanism-comparison]] · [[wiki/concepts/obsidian-plugin-development]]
- [[wiki/sources/karpathy-llm-wiki]]（工作画面原型的出处）

## 依据来源

- 库外代码仓实读（上述路径）；库内暂无 raw 来源，lint 时优先补充（README / 官网待剪藏）
