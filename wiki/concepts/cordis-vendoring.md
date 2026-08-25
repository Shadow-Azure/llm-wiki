---
type: concept
created: 2026-08-25
updated: 2026-08-25
sources:
  - "raw/2026-08-23-dsh-docs-repo-readme.md"
  - "raw/2026-08-23-dsh-docs-cordis-primer.md"
---

# Cordis vendoring：dsh 对框架层的源码收编

**定义**：[[wiki/entities/deepseek-harness|dsh]] 不以 npm 依赖消费 Cordis 框架，而是把**源码 vendor 进仓库 `vendor/` 目录**、全部重命名到 `@deepseek-ai` scope、随 harness 一起发布。官方理由（`vendor/README.md` 原文）：完全掌控框架层——**可审计、可打补丁、可钉死**（auditable, patchable, pinned）。

## vendor 清单（9 包，目录名与上游版本号刻意不变）

| 目录 | rescope 后 npm 名 | 上游名 | 上游源 | 版本 / pinned commit |
|---|---|---|---|---|
| `cordis/` | `@deepseek-ai/cordis` | `cordis` | cordiverse/cordis（packages/core） | 4.0.0-rc.7 @ `56b3d4f` |
| `loader/` | `@deepseek-ai/cordis-plugin-loader` | `@cordisjs/plugin-loader` | cordiverse/cordis（packages/loader） | 1.0.0-rc.5 @ `56b3d4f` |
| `include/` | `@deepseek-ai/cordis-plugin-include` | `@cordisjs/plugin-include` | **deepseek-harness/cordis**（fork） | 1.0.4 @ `abb0a30` |
| `group/` | `…plugin-group` | `@cordisjs/plugin-group` | deepseek-harness/cordis | 1.0.0 @ `abb0a30` |
| `timer/` | `…plugin-timer` | `@cordisjs/plugin-timer` | deepseek-harness/cordis | 1.1.2 @ `abb0a30` |
| `hmr/` | `…plugin-hmr` | `@cordisjs/plugin-hmr` | deepseek-harness/cordis | 1.0.15 @ `abb0a30` |
| `logger-console/` | `…plugin-logger-console` | `@cordisjs/plugin-logger-console` | deepseek-harness/cordis | 1.0.0 @ `abb0a30` |
| `cosmokit/` | `@deepseek-ai/cosmokit` | `cosmokit` | deepseek-harness/cosmokit | 1.8.1 @ `16f6fc0` |
| `schemastery/` | `@deepseek-ai/schemastery` | `schemastery` | deepseek-harness/schemastery | 3.18.0 @ `e67cee0` |

注意两点（AI 推断，待来源验证）：① core/loader 取自 cordiverse 上游，其余 7 包取自 **DeepSeek 自己的 fork**——vendor 的不是纯上游快照；② vendored `cordis/package.json` 版本号为 4.0.1，与 manifest 表的 4.0.0-rc.7 有出入（疑为 rescope 时本地 bump）。

## 为什么要 vendor + rescope

- 所有 workspace 包把 cordis 声明为 **peer dependency** → 发布 harness 必须连带发布框架层；若用上游名发布 = **squat 注册表**（cordiverse 的名字会被 DeepSeek 占据）→ 故 rescope 到 `@deepseek-ai`。
- `verify-vendored-links` CI 门禁：每个 vendored 名在 `pnpm-lock.yaml` 必须解析为 workspace `link:`，且不允许 registry 副本并存——钉死是机器强制的，不是约定。
- 第三方依赖仍走 npm（`@standard-schema/spec`、`js-yaml`、`chokidar` 等）。

## 18 处本地修改（完整清单见 `vendor/README.md`，待剪藏）

修改日志详尽到每条附测试文件名，代表例：

1. **`cordis/src/fiber.ts` 生命周期加固**：修复三个可重入 dispose 缺口（effect owner-list 先注册、UNLOADING 期拒绝新建 effect、子 fiber 在 internal/plugin 发布前注册 disposer 等）——框架级正确性补丁。
2. **事务化 Loader/Include 配置调和**：先导入后处置、失败回滚、Include 子树变更串行化。
3. **懒配置解析**：反向移植上游 [cordiverse/cordis#41](https://github.com/cordiverse/cordis/pull/41)（保留 raw fiber config，注入就绪后才解析）。
4. **include patch 语义修复**：`insert` 进来的行可被同列表后续 patch 命中（上游建索引过早导致新行不可 patch）。
5. 其余：HMR 初始扫描抑制（修退出码 13 死锁）、Windows 短名别名、durable debounced writes、JSDoc 补全（供官网 API 生成器）等。

结论：这是一份**带工程责任的深度 fork**，不是镜像拷贝。

## 协议：MIT（上游 cordiverse/cordis，作者 Shigma）

- 证据三重：① 仓库内 `vendor/cordis/LICENSE` 保留上游原文——*MIT License, Copyright (c) 2021-present Shigma*；② `THIRD_PARTY_NOTICES.md` 声明 vendored 全部 MIT 且各目录保留上游 LICENSE；③ 2026-08-25 web 核实上游 `github.com/cordiverse/cordis` 主分支 LICENSE 同为 MIT。
- **Shigma 即 Koishi 机器人框架作者**——证实 [[wiki/concepts/plugin-mechanism-comparison]] 此前的〔AI 推断〕（2026-08-25 已验证）。
- MIT 允许 vendor + 改名 + 修改 + 再发布，唯一义务是**保留版权与许可声明**；该仓库通过各目录 LICENSE + THIRD_PARTY_NOTICES + `verify-dsh-package-licenses` CI 门禁履行。
- dsh 自身也是 MIT（根 LICENSE）→ 基于 dsh 或 cordis 二开均无传染性义务，保留声明即可。

## 与其他概念的关联

- [[wiki/concepts/dsh-plugin-capability-boundaries]]：vendored 机制层 = 不可替换脊柱的第一层（"插座系统"本体）
- [[wiki/concepts/dsh-plugin-development]]：cordis-primer 是插件作者第一课（本页讲它的"进货渠道"）
- [[wiki/entities/deepseek-harness]]

## 已知争议或矛盾

- 无协议争议。一个注意项：7/9 包取自 deepseek-harness fork 而非 cordiverse 上游，fork 的协议按上游 MIT 继承（`vendor/README.md` 亦按 MIT 记录）——若上游未来变协议，fork 谱系需重新核对。

## 依据来源

- raw：[[wiki/sources/dsh-official-docs|dsh 官方文档]] 之 repo-readme（"powered by Cordis"声明）、cordis-primer（框架概念）
- 库外代码仓实读 `/Users/zn-ice/2026/deepseek-harness`（2026-08-25）：`vendor/README.md`（manifest + 18 处修改日志）、`vendor/cordis/LICENSE`、`vendor/cordis/package.json`、`THIRD_PARTY_NOTICES.md`——均**待剪藏**（已登记来源页后续线索）
- 上游 `github.com/cordiverse/cordis` LICENSE 页 web 核实（2026-08-25，检索快照）
