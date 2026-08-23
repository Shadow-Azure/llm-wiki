---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-dsh-docs-subsystem-credentials.md"
  - "raw/2026-08-23-dsh-docs-subsystem-extensions.md"
  - "raw/2026-08-23-dsh-docs-subsystem-client-modules.md"
  - "raw/2026-08-23-dsh-docs-subsystem-web-server.md"
  - "raw/2026-08-23-dsh-docs-cli-reference.md"
  - "raw/2026-08-23-dsh-docs-plugin-publish.md"
  - "raw/2026-08-23-dsh-docs-tutorial-06-composition-hmr.md"
---

# deepseek-harness 插件网络访问与动态加载

**定义**：dsh 插件调用外部服务与动态下载/加载资源的能力边界——**技术上几乎无限制**（插件与宿主同 Node 进程、无 CORS 概念、官方无 Obsidian 式披露/禁自更新政策），且"动态加载"是官方一等能力，深度达到 **agent 运行时自带源码定义并启动插件**（带审批闸门）。

## 调用外部服务接口

- 插件（host 半）运行在宿主 Node 进程内：`fetch` / `http` / 任意 npm 依赖直接可用，无 CORS、无 API 白名单。官方文档没有"网络使用须披露"类政策（对照 [[wiki/concepts/obsidian-plugin-networking|Obsidian 的披露义务]]）。
- **凭据走专用 seam**（`ctx` 凭据服务）：配置里只放**引用**（POSIX 风格环境变量名，branded 类型），值绝不进 `cordis.yml`；消费方**每个操作解析一次**（轮换无需重启）。本地提供方四层来源：继承环境 → `$DSH_HOME/.credentials.yaml` → 调用目录 `.env` → `$DSH_HOME/.env`。
- agent 侧的边界与插件侧不同：默认 `workspace-write` 权限预设下"**读取和网络访问不受限制**"，bash/写文件限 workspace；MCP 服务器命令被明确标注为"agent 沙箱之外的受信任可执行代码"（故默认不启用）。

## 动态下载与加载资源：三层官方机制

| 层 | 机制 | 触发者 | 说明 |
|---|---|---|---|
| ① HMR | `cordis-plugin-hmr` + 配置层监视 | 开发者 | 改源文件/`cordis.patch.yml` → 卸载旧实例→载新代码；bundle 磁盘变化经 `rebuilt()` 重哈希入图 |
| ② 浏览器半 lazy bundle | `dsh.client` manifest | 用户（组装时） | host 扫描配置树组合 `window.__DSH_BOOT__` 图，各包按需从 `/plugins/<id>/client.js?rev=<内容哈希>` 拉取执行；`immediately` 行则启动期预取 |
| ③ **DynamicCordisRunner**（`ctx.dynamicCordisRunner`） | 运行时 define/run | **agent / 模型** | 见下 |

**③ 是 dsh 最独特的一层**：`define()` 把**带源码的版本化插件包**登记进 session 所有的注册表（包不可变、可多版本）→ `run()` 激活 host 半与（如声明）浏览器半——未经授权的 client 半会挂起等待审批，可一次授权覆盖后续版本（`approveFutureVersions`）→ `stop()` / `undefine()`。配套的 `dsh-tool-cordis` 工具让**模型自己写插件并在内存中挂载/卸载**（`examples/web-cordis` 为官方自指示例；临时插件卸载或进程退出即消失，且"可能影响同一进程中的其他会话"）。

- 判读：Obsidian 把"运行时下载可执行代码"视为绕过审核的灰色地带；dsh 把同一件事做成了**带审批闸门的官方能力**——安全边界不在"代码从哪来"，而在"谁批准它跑"。

## 浏览器半对外通道（client 插件）

- `ctx.webServer.register({ kind: 'exact'|'prefix', path, handler })`：插件可注册自定义 HTTP 路由，handler "拥有完整响应生命周期（可保持响应打开，如 SSE）"——插件自建数据/媒体通道的官方途径。
- Web 服务器默认只绑 `127.0.0.1`，"没有 TLS、认证或 origin 策略"；`--trusted-host` 管理 `/api` 信任围栏。Electron 客户端不走该服务器（`file://` + IPC 桥）。
- 未发现 CSP 设置相关文档〔AI 推断，待来源验证：浏览器半加载外部资源应无策略阻碍，需实验确认〕。

## 与其他概念的关联

- 上游：[[wiki/concepts/dsh-plugin-development]] / [[wiki/concepts/dsh-plugin-distribution]]
- 前端：[[wiki/concepts/dsh-frontend-integration]]（浏览器半 bundle 的发现/加载即本页②层机制）
- 对照：[[wiki/concepts/obsidian-plugin-networking]]（政策约束 vs 机制自由）；[[wiki/concepts/plugin-mechanism-comparison]] 洞察 3/5
- 应用：[[wiki/concepts/dsh-video-plugin-feasibility]]（动态加载的视频插件玩法）

## 已知争议或矛盾

- 无库内矛盾。`dsh-tool-cordis` 自示例承认内存插件"可能影响同一进程中的其他会话"——动态能力的隔离边界是开放问题（登记于 [[wiki/overview]] 开放问题）。

## 依据来源

- [[wiki/sources/dsh-official-docs|dsh 官方文档]]：extensions / client-modules / web-server / credentials 子系统页 + CLI 参考 + publish + HMR 教程
