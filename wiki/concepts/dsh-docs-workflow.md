---
type: concept
created: 2026-09-06
updated: 2026-09-06
sources: []
---

# dsh 文档工程工作流（docs-as-code 十步流水线）

**一句话定义**：deepseek-harness 把文档当作与代码同级的工件管理——每条"应保持一致"的软性要求背后都站着一个会红的门禁脚本，人只做判断（设计取舍、翻译取舍、归档取舍），一致性与保鲜全部机械化。链路覆盖变更全生命周期十步：**规则注入 → 决策落档 → 契约注释 → 类型进文档 → 目录生成 → 双语配对 → 本地验证 → CI 裁决 → 站点投影 → 归档复盘**。

> 本页与 [[wiki/concepts/dsh-agent-loop]] 等运行时四支柱相对，是 dsh 的**文档侧**解剖（2026-09-06 本地实读 @ cd5ef814，与 [[wiki/sources/dsh-source-runtime|运行时实读]] 同一存证 commit）。文内标注"AI 推断"者为综合判断，其余机制均可溯源到仓库文件（见文末清单）。

## 十步总览

| 步 | 环节 | 核心机制 | 门禁/脚本 |
|---|---|---|---|
| 0 | 会话启动 | 分层 AGENTS.md：每条规则一两行 + 链接到唯一 home（one home per fact）；`CLAUDE.md` 是 `AGENTS.md` 的 symlink | `verify-doc-budgets`（常驻文档词数预算） |
| 1 | 决策落档 | Agent Note：`{proposed\|implemented\|rejected}/{class}/yyyy-mm-dd-主题.md`，六类封闭集合；implemented note 是活文档 | `verify-agent-note-{classification,format}`、`verify-archived-agent-notes` |
| 2 | 写代码 | JSDoc 即契约（导出必须有、函数须 `@param`/`@returns`、事件须 `@mode`）；包 README 钉死 Model Experience 与 Known Limitations 段 | `verify-export-jsdoc`、`verify-package-readme-{model-experience,limitations}` |
| 3 | 类型进文档 | ```` ```ts type-equiv ```` 围栏 = 与源码逐字等价，manifest 三元组 `{doc, symbol, source}` 登记；普通 ts 围栏真实编译 | `verify-type-equiv`、`doc-typecheck` |
| 4 | API 目录 | `<!-- BEGIN GENERATED -->` 区块是机器地盘；verify = gen `--check` 重生成后与磁盘 diff；双语 locale 投影 | `gen-*`/`verify-*` 成对（cordis/tool/config/persistence/module-graph/…） |
| 5 | 双语配对 | 三件套 `foo.md` + `foo.zh.md` + `foo.i18n.yaml`（存两侧 git blob hash）；改一侧须最小修补另一侧重录 | `verify-translation-pairing` + git merge driver |
| 6 | 本地验证 | 按改动面选门禁组合：`test:docs`（quick 叶子）vs `doc-sync`（全量约 30 叶）；git hooks 刻意窄 | `run-gates.ts`（doc-quick / doc-sync 模式） |
| 7 | PR 与 CI | 文档门禁内嵌静态检查 lane；文档标准本身也有 vitest 测试 | `ci.yml`（check:ci:static）、`docs-pages.yml` |
| 8 | 发布投影 | VitePress 站是 docs/ 的构建期**投影**，仓库无第二份内容；豁免显式登记 | `project-doc-site.spec.ts` 等 |
| 9 | 归档复盘 | note 事实被代码漂移追着就地更新；低价值 note 语义判断后冻结归档；事故进 postmortem | `dsh-archive-agent-notes` skill、sha256 + append-only manifest |

## 关键机制

### 分层规则与字数预算（第 0 步）

根 AGENTS.md 开头即声明必读链：`Read docs/architecture.md before changing packages/`。规则按层级分工，`docs/AGENTS.md` 的 tier 表同时回答"放什么"与"**不许**放什么（应去哪）"：

| Tier | Job | Does NOT belong there |
|---|---|---|
| Root `AGENTS.md` | Standing orders: one to three lines each, linking its home | Stories, worked examples, anything restated from a linked home |
| Subtree `AGENTS.md` | Orders specific to that subtree | Repo-wide rules the root file already carries |
| Package README | Per-package contract: config, semantics, limitations, Model Experience | JSDoc restatement, generated-catalog restatement |
| postmortem/ | Incident stories — the only tier where narrative belongs | — |

`scripts/doc-budgets.manifest.json` 给 8 篇常驻文档设词数上限（如 `AGENTS.md: 1950`、`docs/architecture.md: 2400`），超限即红。门禁红的处置顺序：**Relocate（搬到正确层级）→ Condense（压缩）→ Raise（提上限须在 PR 里说明理由）**。本质是上下文预算管理：AGENTS.md 每个会话都整体进入模型上下文，写长即烧 token、稀释注意力（AI 推断，待来源验证——仓库未明文说"token"，只说 standing orders）。

### Agent Note：设计决策的固定归宿（第 1 步）

"为什么选 A、放弃了什么"装不进代码与 commit message，固定归宿是 `.agents/notes/`。硬性规则（README:46）：

> Every non-trivial change MUST add or update at least one Agent Note in the same PR.

- **路径即状态**：文件夹就是状态机（proposed/implemented/rejected），文件名 `yyyy-mm-dd-主题`；六类 class 是封闭集合（feature/bug-fix/simplification/architecture/process/testing，`scripts/agent-note-tree.ts`），加类须同时改脚本与 README。
- **固定结构**：Problem → Decision → **Alternatives considered**（被否方案及原因——本页存在的核心理由）→ Consequences。
- **三门禁**：classification（路径合法，并封死旧目录 `docs/rfc/`）；format（第三行 `Status:` 与所在文件夹互检，如 rejected 须写 `Status: rejected — 理由`）；archived（归档件 sha256 冻结 + append-only 清单，改一字节即红）。
- **活文档**：代码改名/挪文件/改默认值，同一变更就地重写 note 中的事实（"Rewrite stale facts in place; do not append change history"）。

### JSDoc 即契约、README 即文档（第 2 步）

注释不是装饰，是文档**源料**——生成器原样抽走（见第 4 步）。示例（`packages/settings/settings/src/types.ts`，事件 `settings/updated`）：

```ts
/**
 * Committed change to one registered namespace's resolved value. Emitted
 * after the provider persisted ... never emitted when the resolved value
 * is deep-equal. Listener failures are contained and logged ...
 * @param ns - the namespace whose resolved value changed.
 * ...
 * @mode emit
 */
'settings/updated'(ns: SettingsNamespace, next: unknown, prev: unknown, source: SettingsUpdateSource): void
```

写的是契约语义（何时触发/何时不触发/监听器失败如何处置），不是代码复述。包 README 两段被门禁钉死：

- **Model Experience**（固定三层：What the model sees / Token effect / KV Cache effect）——因为 dsh 是 agent harness，文档第一读者是 LLM，此段回答"本包会把什么塞进模型上下文、是否打爆前缀缓存"。允许诚实负结果（"Indirectly, through consumer plugins..."）。
- **Known Limitations and Deferred Work**——精确写出失效条件与欠账（如 `redactSecrets is not a proven wire boundary`），无此段须在 allowlist 登记豁免。

### type-equiv：文档代码块的防伪锁（第 3 步）

文档里贴的类型声明最容易腐烂。解法：```` ```ts type-equiv ```` 围栏声明"与源码逐字等价"，`scripts/verify-type-equiv.ts` 用 TypeScript compiler API 从源文件解析真实声明做结构归一化对比——忽略空白与普通注释，但**保留声明结构与每一条 JSDoc**；围栏与 manifest 条目 1:1（多贴未登记的块也红）；中文 `.zh.md` 侧围栏须与英文侧字节相同。对照实例：

源码 `packages/util/brand/src/index.ts`：
```ts
/** A string carrying a compile-time-only brand `B`. */
export type Branded<B extends string> = string & { readonly [BRAND]: B }
```

文档 `docs/subsystems/core.md`：
```` ```ts type-equiv ```` 围栏内为一字不差的同两行（连 JSDoc 都相同）。

普通 ```` ```ts ```` 围栏另由 `doc-typecheck` 抽成虚拟文件**真实编译**（`ts.createProgram`）——文档示例是能编译的活代码。因多个门禁各自构建完整 ts.Program，本地模式并发限 4（run-gates.ts:155）。

### 生成目录：gen/verify 成对（第 4 步）

API 参考目录不手写。`package.json` 里 `gen-foo` 与 `verify-foo` 是**同一脚本**，后者即 `--check`：重新生成后与磁盘 diff，源码改了没再生成即红（报错含第一处差异行号）。文档中生成区块自带告示牌：

```markdown
<!-- BEGIN GENERATED cordis-surface (gen-cordis-catalog.ts) — do not edit between markers -->
```

区块内是源码 JSDoc 的原样搬运（`ts cordis-catalog` 围栏）——**写给编译器看的 `@param` 就是文档站读者看到的 API 参考**，中间零人工转写。生成器双语感知：同一区块写进 `.md` 与 `.zh.md` 两侧，中文侧仅本地化配对文档路径。

### 双语三件套：哈希公证（第 5 步）

全库文档中英双语且"两侧说同一件事"被机器公证。三件套 = 同目录 `foo.md` + `foo.zh.md` + `foo.i18n.yaml`；yaml 存两侧的 **git blob hash**（内容寻址，未提交内容也可算，故本地即时可校验）。实例（仓库根 `README.i18n.yaml` 全文）：

```yaml
# Bilingual-pair consistency record ... after editing either side,
# bring the other along and re-record with:
#   pnpm run verify-translation-pairing --write README.md
README.md: 9f89db3d4502dea4a0d181799164f304d4976740
README.zh.md: aa66ef1d24a5e2165859e9337273d807dff3d070
```

更新循环：改 A 侧 → 对照 A 的 diff **最小修补** B 侧（非重翻）→ `--write` 重录两侧 hash；这个 yaml diff 就是可评审的"确认一致"动作。失配时可从 hash 恢复任一侧上次确认原文。除 hash 外还校验**结构签名**：标题层级与顺序、代码围栏逐字、表格行列数、列表种类/条数、链接 query/fragment。Git 合并有配套 merge driver（两侧各自合法合并成功时自动合成新记录）。此设计把"翻译保持同步"从靠自觉的苦役变成可 diff、可评审、可机械验证的提交动作（AI 评价，机制本身见 docs/i18n/README.md）。

### run-gates：门禁组合与 CI 内嵌（第 6-7 步）

`scripts/run-gates.ts` 是总调度：检查定义为"叶子"，模式是叶子组合。快速档即全量档过滤免构建叶子：

```ts
function docQuickLeafGates(): Gate[] {
  return docSyncLeafGates({ includeDocTypecheck: false }).filter(gate => gate.quick === true)
}
```

`test:docs` = doc-quick（md-links、type-equiv、translation-pairing、budgets、note 三门禁等）；`doc-sync` = 全量约 30 叶（含 doc-typecheck、各 catalog 新鲜度、VitePress 构建）。路由由 skill 规定：改动"Documentation, Agent Notes, catalogs, or doc-linked comments" → `doc-sync`。关键架构决策：`docSyncLeafGates()` 被 doc-sync / doc-quick / check-all / ci-primary / **ci-static** 五条通道复用——文档门禁内嵌静态 lane 而非旁路，CI 不可能"忘了配"。git hooks 刻意窄（pre-commit 只管 staged lint/尾随空白，pre-push 只增量 typecheck），穷尽覆盖归 CI。文档标准本身也有 vitest 测试（`doc-standard.spec.ts` 等连技能文本都被断言）。

### 文档站：投影不是副本（第 8 步）

`website/`（VitePress）无一篇 markdown 正文，只有发布清单 `docs.ts`：每页登记 `source`（指向 docs/ 源）→ `route` 映射，`pairedPages()` 自动把 `foo.md`/`foo.zh.md` 镜像到两 locale 路由；翻译缺失时两侧故意投同一源而非拷贝。有测试断言"仓库不含文档副本"。`docs:build` 兼任死链检查。无中文审校对应物的页面（如 `docs/cordis-api/inherited.md`）在 manifest exclusion 列表显式登记——豁免也要白纸黑字。

### 归档与复盘（第 9 步）

文档老化三条出路：① implemented note 被代码漂移追着**就地更新事实**；② 低价值 note 归档——刻意反机械：`Judge every note semantically; word count and age are discovery aids, never archive criteria`、`Do not archive toward a quota`；归档即三件套整体挪入 `archived/`，每文件 sha256 记入 append-only manifest 封存。③ 事故进 `docs/postmortem/`（如 0004 Landlock 误分类事故：Executive summary / Timeline / Root cause 结构）——全库**唯一允许叙事**的层级，每篇仍是严格三件套，逃不出双语配对门禁。

## 与其他概念的关联

- **运行时侧 ↔ 文档侧成对**：[[wiki/entities/deepseek-harness]] 的四支柱（loop/日志/管线/协调）之外，本页补齐第五个维度；[[wiki/concepts/dsh-coordination-subsystems]] 中 `.agents/notes` 1646 篇的完整生命周期即本页第 1/9 步。
- **与 [[wiki/concepts/llm-wiki|LLM Wiki]] 同构**（AI 推断，待来源验证）：本库是"LLM 编译的个人 wiki"（schema 层 CLAUDE.md + 维护义务），dsh 是"机器编译的工程文档"（AGENTS.md 常驻层 + 门禁义务）——都用"常驻规则层 + 增量维护纪律 + 单一事实源"对抗知识腐化；差异在 dsh 把一致性裁决全交给确定性脚本，LLM Wiki 把综合判断交给模型。
- postmortem 0004（Landlock 部分执行通知误分类）即 [[wiki/concepts/dsh-tool-pipeline]] 沙箱域的真实事故样本。

## 已知争议或矛盾

- 无已知矛盾。一处易混点（2026-09-06 实读核对）：JSDoc 的 `@param`/`@returns` 强制由 `verify-export-jsdoc` 执行；事件 `@mode` 规则**声明**于根 AGENTS.md、由 `gen-scoped-events` 等配套脚本执行——两条规则宿主不同，引用时勿混为同一门禁。

## 依据来源

- 本地实读 `/Users/zn-ice/2026/deepseek-harness` @ `cd5ef814`（2026-09-06，仓未漂移，与 [[wiki/sources/dsh-source-runtime]] 同一存证）。
- 关键文件（均**未剪藏入 raw/**，论断以本地路径溯源）：根 `AGENTS.md`、`docs/AGENTS.md`（tier 表）、`.agents/notes/README.md`、`scripts/{agent-note-tree, verify-agent-note-*, verify-export-jsdoc, verify-type-equiv, doc-typecheck, run-gates, verify-doc-budgets}.ts`、`scripts/{type-equiv, doc-budgets, translation-pairing}.manifest.json`、`docs/i18n/README.md`、`docs/cookbook/adding-a-package.md`、`docs/subsystems/{core, settings}.md`、`packages/util/brand/src/index.ts`、`packages/settings/settings/{README.md, src/types.ts}`、`website/docs.ts`、`.github/workflows/{ci, docs-pages}.yml`、`lefthook.yml`、`.agents/notes/archived/manifest.json`、`docs/postmortem/0004-*`。

## 后续线索

- **待剪藏**：`docs/AGENTS.md`（tier 表 + 写作规则）与 `docs/i18n/README.md`（双语契约）是可直接借鉴的方法论文档，值得剪藏入 raw/ 作为本库 schema 演化的参考源。
- gen-* 生成器的实现载体 `packages/typert` 未深挖（REGION 标记、catalog 渲染管线）。
- 1646 篇 note 体量下归档制度的实际运转（archived/ 实际条目数、归档频率）未统计。
