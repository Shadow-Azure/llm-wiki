---
type: concept
created: 2026-09-06
updated: 2026-09-06
sources: []
---

# 自包含二进制分发

**定义**：把程序的全部依赖（语言运行时、标准库、第三方库）收进单一可执行文件的分发方式。核心洞察：**「零依赖」有两种实现——把依赖消灭（静态编译）和把依赖搬家（随身携带）**；原生语言与 Node 之争的本质是搬运量差一个数量级。

## 「glibc 地狱」的正确归因

在 Linux 分发二进制的常见报错，全部源于**动态链接的默认行为**，不是语言能力天花板：

| 症状 | 根源 |
|---|---|
| `GLIBC_2.xx not found` | 动态链接 glibc（新系统编译、旧系统运行） |
| `GLIBCXX_3.4.x not found` | 动态链接的 libstdc++ 版本过老 |
| Windows 缺 `vcruntime140.dll` | MSVC 默认动态链接 CRT |

## Rust 静态打包实践清单

```toml
# .cargo/config.toml — Windows 静态链接 CRT
[target.x86_64-pc-windows-msvc]
rustflags = ["-C", "target-feature=+crt-static"]
```

```bash
# Linux 换 musl 目标全静态
rustup target add x86_64-unknown-linux-musl
cargo build --target x86_64-unknown-linux-musl --release
```

- **TLS 用 rustls 替代 openssl**：openssl 是"打包不干净"的头号元凶（C 库/动态链接/版本混乱）；[[wiki/entities/cc-switch|cc-switch]] 全栈 rustls 正是此道
- 先例：rustup、ripgrep、fd 的官方发行版即静态单文件
- 残余坑：musl 的 DNS 解析历史问题、含 C 依赖的 crate 需 vendored 特性〔细节待来源验证〕

## 对照：Node 的「打包一切」

| | 静态编译 Rust | 打包一切的 Node（pkg / bun compile） |
|---|---|---|
| 依赖去哪了 | 编译进二进制 | 搬进安装包 |
| 随身携带量 | 只有自己的代码（MB 级） | 整个运行时 V8+libuv+node_modules（40-90MB） |
| 用户环境要求 | 无 | 无 |
| 本质 | 零依赖 | 零**外部**依赖、全**内部**携带 |

两个常见推理错误：

1. **不对称比较**：拿 Node 的终极形态（全量打包）对比 Rust 的默认配置（动态链接）——前者也是工具链加工后的产物，后者两条配置即静态化
2. **忽略 Node 自己的原生坑**：`.node` 扩展绑定 Node ABI（`NODE_MODULE_VERSION` 不匹配）、Electron 里需 electron-rebuild 重编译、sharp/node-canvas 等 C 库包把 glibc 问题原样带回——Node 只有"纯 JS、零原生模块"时才真正免坑

悖论式注脚：Node 单文件打包工具本身全是原生静态编译产物（Bun=Zig、Deno compile=Rust、pkg 背后 V8=C++）——原生路线可行性最有力的证人。

## 与其他概念的关联

- [[wiki/concepts/tauri-vs-electron]]——「借 vs 带」在分发层的具体化；Tauri 安装包里那个几 MB 二进制除系统 WebView 外零环境依赖
- [[wiki/entities/cc-switch|cc-switch]]——rustls 全栈选择的实证样本
- （延伸，AI 推断待验证）Docker 镜像与自包含二进制是同一问题的两种答案：[[wiki/concepts/open-notebook-architecture|open-notebook]] 靠容器携带多运行时（compose 成最小分发单元），[[wiki/concepts/bilinote-architecture|BiliNote]] 单进程+SQLite 免 Docker——依赖搬运策略决定分发形态

## 依据来源

- 本地实读 [[wiki/entities/cc-switch|cc-switch]] Cargo.toml（rustls 技术栈，2026-09-06）
- 静态链接做法（musl / crt-static / rustls）为 Rust 社区通行实践，本页由 AI 综合〔待来源验证，建议剪藏权威实践页〕
- crates.io ~20 万包 vs npm ~220 万包：2026-09 检索快照〔待剪藏〕
