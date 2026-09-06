---
type: source
created: 2026-09-06
updated: 2026-09-06
sources:
  - "raw/2026-09-06-obsidian-oss-alternatives-landscape.md"
---

# 类 Obsidian 开源项目格局（star/协议/活跃度/二次开发通道实测 2026-09-06）

**一句话**：22 仓实测揭晓"类 Obsidian"开源格局——本地优先 PKM 三强 **SiYuan 46.2k / Logseq 44.8k / Trilium 37.7k** 全员 AGPL；star 最高的一档（AppFlowy 76.3k / AFFiNE 72.2k / Memos 62.8k）都是 Notion/速记形态而非 Obsidian 形态；且**高 star ≠ 开源友好**——Outline 已转 BUSL、Anytype 客户端实为 source-available、AFFiNE 后端是 EE。

## 核心要点

- **三条旧线索裁决**（[[wiki/concepts/note-tool-landscape|笔记工具格局]]页 2026-08-23 登记）：① Trilium 由社区 fork TriliumNext 接管并拿回原名（原仓库转移，AGPL 治理开放活标本）；② Anytype 桌面客户端 LICENSE 实为 **Any Source Available License 1.0**（非商用或 Allowed Networks 才可商用）——修正库内旧表述"客户端开源"；③ Joplin 确认 **AGPL-3.0-or-later**（目录级例外 + JOPLIN SAS 商标），"Joplin 是 MIT"是过时印象。
- **Outline 已转 BUSL-1.1**（Change Date 2030-09-01 → Apache-2.0）："源码可见"与"开源"在笔记工具域已明确分家——本页与格局页的协议商用含义表互为印证。
- **二次开发通道最全的是 SiYuan**：kernel 本地 HTTP API（语言无关）+ petal Plugin API + bazaar 集市 + Docker 自托管（含 OIDC）；但部分功能付费会员、原生 `.sy` 格式非纯 Markdown。
- **Logseq 处于重写动荡期**：DB 版至 2026-05 无稳定安装版时间表（社区已等约两年），文件版有整文件重写/重命名级联的性能瓶颈——贡献与深度二次开发需择时。
- **AppFlowy 风险信号**：自托管后端 AppFlowy-Cloud 于 2026-09-01 归档（替代结构未明），社区有"self-host 并不真开放"的批评；主仓 76.3k⭐ AGPL 仍活跃。
- **纯宽松协议 + 类 Obsidian 形态**的组合存在但 star 中等：Foam 17.4k（MIT，VS Code 内）、TiddlyWiki 8.6k（BSD，单文件可改性天花板）、SilverBullet 6k（MIT，自托管）。

## 值得注意的细节

- AGPL 是本地优先 PKM 的统治性协议（SiYuan/Logseq/Trilium/Joplin/Docmost/Standard Notes）；MIT/BSD 阵营（Foam/TiddlyWiki/SilverBullet/Memos/BookStack）star 反而普遍低一档——宽松协议与商业可持续性的取舍在笔记域肉眼可见。
- Standard Notes：Proton 2024-11 收购后保持独立产品与 AGPL，但社区有开发放缓担忧；创始人新项目遭非竞争条款阻拦。
- Dendron（Apache-2.0，7.5k⭐）最后推送 2025-11-13，事实停摆——"协议最友好"与"项目活着"是两个维度。
- BookStack 主仓已迁 Codeberg；star 数应以迁移后为准。

## 后续线索

- AppFlowy 接替 AppFlowy-Cloud 的自托管后端形态（影响其二次开发评估）。
- AS Notes（VS Code wikilink+纯 Markdown 新扩展，Foam 潜在后继）star/协议未实测。
- Anytype 的 any-sync 协议层与客户端协议不同仓，若评估需单独核验。
- Logseq DB 版稳定安装版发布节点。
