# llm-wiki

按 Karpathy 的 [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 模式构建的个人知识库：**LLM 负责编译和维护 wiki，人负责收集来源和提出好问题。**

## 结构

```
CLAUDE.md    # schema：结构与工作流契约（LLM agent 必读）
raw/         # 不可变原始来源（LLM 只读）· assets/ 存放附件
wiki/        # LLM 生成维护的知识层
  index.md   #   内容目录（查询的第一入口）
  log.md     #   追加式时间线（可 grep 解析）
  overview.md
  sources/ · concepts/ · entities/
```

## 用法

- **摄入来源**：把文章（markdown / pdf / 网页剪藏）放进 `raw/`，对 agent 说 `ingest raw/xxx.md`
- **提问**：直接问，agent 会先查 `wiki/index.md` 再综合回答
- **体检**：说 `lint 一下 wiki`，检查矛盾、过时论断、孤儿页、缺口

推荐用 [Obsidian Web Clipper](https://obsidian.md/clipper) 收集网页来源（附件已配置自动存入 `raw/assets/`）。

完整约定见 **[CLAUDE.md](CLAUDE.md)**。
