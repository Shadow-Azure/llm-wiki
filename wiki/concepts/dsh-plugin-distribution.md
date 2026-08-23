---
type: concept
created: 2026-08-23
updated: 2026-08-23
sources:
  - "raw/2026-08-23-dsh-docs-plugin-publish.md"
  - "raw/2026-08-23-dsh-docs-cli-reference.md"
  - "raw/2026-08-23-dsh-docs-repo-readme.md"
  - "raw/2026-08-23-dsh-docs-adding-a-package.md"
  - "raw/2026-08-23-dsh-docs-first-plugin.md"
---

# deepseek-harness 插件分发

**定义**：dsh 插件的交付与安装机制——分发的单位是**组合包（bundle）**：一个 npm 包 + `dsh.bundle` manifest（指向一份 `cordis.patch.yml` 配置层）；用户用 `dsh plugin --profile <name> add <spec>` 安装进 profile。**官方没有插件市场**：发布即"发 npm 包或开 git 仓库 + 打 `dsh-plugin` topic"，市场是社区围绕该 topic 自建的一层。

## 打包：两个概念，两种 manifest

| 概念 | 是什么 | manifest |
|---|---|---|
| **组合包 bundle** | 作者编写并分发的 npm 包：构建产物 + 一份配置层 | `package.json` 里 `"dsh": { "bundle": { "patch": "./cordis.patch.yml" } }` |
| **profile** | 用户侧一份可启动组合（`$DSH_HOME/profiles/<name>`） | `dsh.profile` + 有序 `bundles` 列表，**由 `dsh plugin` 命令维护，从不手写** |

最小组合包结构（publish 教程原文）：

```
hello-plugin/
├── package.json       # 声明 dsh.bundle；main 指向构建产物
├── cordis.patch.yml   # 该包贡献的配置层（插件行按包名引用）
└── index.js           # 构建好的插件模块
```

- 构建：dsh 不规定构建工具（仓库自身用 tsdown；git 安装示例 [turtle-ui](https://github.com/deepseek-harness/turtle-ui) 的 `prepare` 跑一份自包含 tsdown 配置，"直接转译 src/，不做类型检查"）。产物要求**自包含**——不能假设旁边有 monorepo checkout。
- 没有 `dsh.bundle` 声明的包也可安装，但只作为普通依赖（供其他插件 import），不激活任何层。

## 安装：`dsh plugin --profile <name> <args…>`（转发给 pnpm）

四种来源，两种不需要任何构建授权：

| 来源 | 命令 | 要点 |
|---|---|---|
| **npm 注册表** | `dsh plugin --profile demo add your-package` | 作者 `pnpm publish` 时已构建好 `lib/`，装的是预构建代码，最省事 |
| **tarball** | `dsh plugin add ./hello-plugin-0.1.0.tgz` | `pnpm pack` 产物，同样预构建 |
| **git 托管** | `dsh plugin add github:you/hello-plugin` | 拉的是**源码**；作者须提供自包含 `prepare` 脚本；用户须过 pnpm≥10 `allowBuilds` 授权 |
| **本地路径** | `dsh plugin add ./hello-plugin` | 开发期 link checkout |

git 安装的安全警告（publish 教程原文）：允许 `prepare` = "允许该包的代码在安装时于你的机器上执行，且不在 agent 运行的任何沙箱之内"；官方建议只对可信源码授权并**锁定 commit**（`github:you/pkg#<sha>`）。

### 安装后的生效语义

- bundle 成员变化（add/remove/update）**需重启 profile**——运行中的 profile 保留启动时的 bundle 集合。
- profile / home 的 `cordis.patch.yml` 编辑则**热重载**（带 HMR）。
- 配置层叠顺序（后应用者按行胜出，整行替换 config 而非深合并）：① `dsh.profile.bundles` 列出的各组合包层（按列表序，`@deepseek-ai/dsh-base` 打底）→ ② profile 自己的 `cordis.patch.yml` → ③ home 级 `$DSH_HOME/cordis.patch.yml` → ④ 各 `--patch` overlay。`dsh --profile X --dump-config` 可审查组合结果（带注释标明每行来自哪层）。

## 发布与"市场"现状

- **官方立场**（README 原文）："Add the [`dsh-plugin`](https://github.com/topics/dsh-plugin) topic to your plugin repository for discoverability." —— 官方 docs 全文**没有 "marketplace" 一词**；没有审核、没有目录、没有提交通道。
- **社区市场**（第三方，围绕 GitHub `topic:dsh-plugin` 聚合；待剪藏的社区来源）：
  - [DSH 插件商店](https://dsh.deepseek404.com/) — 收录 GitHub 上全部 `dsh-plugin` 开源项目，分类浏览
  - [dshplugin.dev](https://dshplugin.dev/) — Web GUI 内浏览 + 一键安装的插件目录
  - [dsh-plugin-marketplace](https://github.com/AwesomeHou/dsh-plugin-marketplace) / [dsh-plugin-market](https://github.com/TheYoungChen/dsh-plugin-market) — **以插件形态实现的应用内市场**（"把 GitHub topic 变成插件市场"：设置页新增市场入口，部分还提供模型工具让 agent 自主搜索安装插件）
  - [awesome-deepseek-harness-plugins](https://github.com/imsai-sh/awesome-deepseek-harness-plugins) 精选列表；媒体报道市场已收录 300+ 插件（[电子工程专辑](https://www.eet-china.com/mp/a517835.html)）
- 与 Obsidian 分发闭环对照：Obsidian 是**官方目录 + 自动审核**（见 [[wiki/concepts/obsidian-plugin-distribution]]）；dsh 把发现层完全交给生态（npm/GitHub 既有机制 + topic 约定），代价是无官方质量/安全把关，收益是零提交通道、发版即 npm publish。

## 与其他概念的关联

- 开发 → [[wiki/concepts/dsh-plugin-development]]；网络与动态加载 → [[wiki/concepts/dsh-plugin-networking]]
- 三方分发机制对比 → [[wiki/concepts/plugin-mechanism-comparison]]
- 宿主 → [[wiki/entities/deepseek-harness]]

## 已知争议或矛盾

- 无库内矛盾。注意快照时效：developer preview 期机制可能变动。

## 依据来源

- [[wiki/sources/dsh-official-docs|dsh 官方文档]]：publish / CLI reference / README / adding-a-package
- 社区市场部分为 web 检索所得社区来源（已附链接），尚未剪藏为 raw——lint 候选
