---
title: GitHub Copilot 的用户数据利用（Copilot data utilization / Copilot Memory / Work IQ）
layer: model
source_urls:
  - https://github.blog/changelog/2026-03-25-updates-to-our-privacy-statement-and-terms-of-service-how-we-use-your-data/
  - https://github.com/orgs/community/discussions/188488
  - https://docs.github.com/copilot/how-tos/manage-your-account/managing-github-copilot-policies-as-an-individual-subscriber
  - https://github.blog/changelog/2026-05-15-copilot-memory-supports-user-preferences-for-pro-pro-users/
  - https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/work-iq/cli
  - https://github.com/microsoft/work-iq
accessed: 2026-08-26
local_pdf:
---

# GitHub Copilot 的用户数据利用

> 一句话：GitHub 自 2026-04-24 起默认用个人版（Free/Pro/Pro+）用户的 Copilot 交互数据（提示词、接受的/修改过的建议、代码片段与上下文）训练自家 AI 模型（可 opt-out）；"记忆用户偏好"的功能实名为 Copilot Memory，而 Microsoft "Work IQ" 是另一个产品（M365 数据访问层），两者常被混为一谈。

## 是什么 / 背景

- **政策变更**：GitHub 于 2026-03-25 在官方 changelog 宣布更新 Privacy Statement 与 Terms of Service："from April 24 onward we will begin using interaction data—specifically inputs, outputs, code snippets, and associated context—from Copilot Free, Pro, and Pro+ users to train and improve our AI models unless you opt out."（https://github.blog/changelog/2026-03-25-updates-to-our-privacy-statement-and-terms-of-service-how-we-use-your-data/）
- **官方 FAQ**：GitHub Community Admin 早于 2026-03-02 发布置顶 FAQ 讨论帖，明确生效日与细节："The change goes into effect on April 24. Users received 30 days advance notice and can opt out at any time."（https://github.com/orgs/community/discussions/188488）
- **适用范围划界**（务必分清）：
  - 个人版 Free / Pro / Pro+：默认用于训练，可随时 opt-out；
  - Copilot Business / Copilot Enterprise：**不受影响**——"Our agreements with Business and Enterprise customers prohibit using their Copilot interaction data for model training"；
  - 学生版 / 免费教师版 Copilot Pro：不受影响。
- 注意：此次变更是个人版政策从"默认不训练"转向"默认训练 + opt-out"（对旧设置"Enabling or disabling prompt and suggestion collection"的关闭偏好会被继承）。

## 机制：用户数据怎么流动

信号来源 → 处理/提炼 → 更新什么 → 验证与上线闭环：

1. **信号来源**（官方 FAQ 列明的收集范围，仅当设置开启时）：
   - 用户接受或修改过的输出（"Outputs accepted or modified by the user"）——用户对建议的修正本身就是训练信号；
   - 发送给 Copilot 的输入，包括展示给模型的代码片段（"Inputs sent to GitHub Copilot, including code snippets shown to the model"）；
   - 光标位置周边的代码上下文；
   - 用户写的注释与文档；
   - 文件名、仓库结构、导航模式；
   - 与 Chat / 内联建议等功能的交互。（https://github.com/orgs/community/discussions/188488）
2. **处理/提炼**：自动过滤 API key、密码、token 与 PII；去标识化/聚合；按仓库访问控制限制采集面。（changelog："filters designed to detect and remove sensitive data, and de-identification techniques"）
3. **更新什么**：GitHub 及其关联公司（主要是 Microsoft）的 AI 模型（Terms 新增 Section J：未 opt-out 时授予 GitHub 及 affiliates 收集使用 inputs/outputs 训练 AI 模型的许可）。第三方模型提供商**不会**拿到该数据做自己的训练；数据不出售。GitHub/Microsoft 从事模型研发的人员可访问，访问有日志审计。
4. **验证与上线**：官方理由是"real-world data will help our models cover the increasing number of scenarios they're now being used for"；opt-out 即时生效（"Once you opt out, we stop collecting from that point forward"）；私有仓库静态内容不用于训练（交互数据可能产生于私有仓库内的使用过程，但仓库内容 at rest 不碰）。
5. **用户控制入口**：Settings → Privacy / Copilot 设置中的训练开关；此前关闭过 prompt/suggestion 收集的用户偏好自动延续。docs.github.com 的 "Managing GitHub Copilot policies as an individual subscriber" 页面描述了个人版该开关（注：docs.github.com 对本机抓取器持续不可达，此句依据搜索快照转述，页面 URL 见 frontmatter）。

## 关键事实与数字（附出处）

- 生效日期 2026-04-24，提前 30 天通知 — https://github.com/orgs/community/discussions/188488
- 官方 FAQ 发布于 2026-03-02，changelog 发布于 2026-03-25 — 同上；https://github.blog/changelog/2026-03-25-updates-to-our-privacy-statement-and-terms-of-service-how-we-use-your-data/
- 适用：Copilot Free/Pro/Pro+；Business/Enterprise 协议禁止训练；学生/教师免费版不受影响 — https://github.com/orgs/community/discussions/188488
- Terms 新增 Section J（AI features, training, and your data）：未 opt-out 即授权训练用 license；"We will not share your inputs or outputs with third-party AI model providers for their own independent training" — https://github.blog/changelog/2026-03-25-updates-to-our-privacy-statement-and-terms-of-service-how-we-use-your-data/
- EEA/UK 用户：AI 开发被列为 legitimate interest（合法利益）处理依据 — 同上
- **Copilot Memory**（真正的"记忆用户偏好"功能）：2026-05-15 起 user-level 偏好进入 early access（Pro/Pro+），可记住 commit 风格、PR 结构、沟通语气偏好，可在个人设置中查看/删除；此前仅 repo 级记忆 — https://github.blog/changelog/2026-05-15-copilot-memory-supports-user-preferences-for-pro-pro-users/
- **Microsoft Work IQ**：CLI + MCP server，把 AI 助手（含 VS Code/CLI 中的 GitHub Copilot）连接到 Microsoft 365 Copilot 数据（邮件、会议、文档、Teams 消息、人员信息）；2026-06-16 GA；安全模型明确"Work IQ doesn't store your Microsoft 365 data"（按需检索、不存储）— https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/work-iq/cli ；https://github.com/microsoft/work-iq

## 与原 passage 声明的核对

| 原声明 | 检索到的说法（附 URL） | 判定 |
|---|---|---|
| 2026 年 3 月起允许从用户提示词和代码学习 | 政策内容属实：inputs（含提示词、代码片段）、outputs、代码上下文等用于训练。但**生效日是 2026-04-24**，3 月（03-02 FAQ / 03-25 changelog）是公告期，提前 30 天通知（https://github.com/orgs/community/discussions/188488 ；https://github.blog/changelog/2026-03-25-updates-to-our-privacy-statement-and-terms-of-service-how-we-use-your-data/） | ⚠️ 部分证实——"3 月起"应为"3 月宣布、4 月 24 日生效" |
| 默认开启、可 opt-out | 属实："unless you opt out"；可随时 opt-out，关闭后停止收集；旧 opt-out 偏好延续（https://github.blog/changelog/2026-03-25-updates-to-our-privacy-statement-and-terms-of-service-how-we-use-your-data/） | ✅ 证实（注意仅限个人版 Free/Pro/Pro+；Business/Enterprise 默认且协议禁止训练） |
| "Work IQ" 记忆用户偏好 | 名实不符。GitHub 记忆用户偏好的功能叫 **Copilot Memory**（user-level preferences，early access，2026-05-15，Pro/Pro+）（https://github.blog/changelog/2026-05-15-copilot-memory-supports-user-preferences-for-pro-pro-users/）。**Work IQ** 是 Microsoft 的 CLI/MCP 产品，让 AI 助手查询 M365 数据（邮件/会议/文档），明确不存储数据（https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/work-iq/cli） | ⚠️ 有出入——偏好记忆功能存在但名为 Copilot Memory；Work IQ 是另一用途的产品，不承担"记忆用户偏好"职能 |

## 本地文件与延伸阅读

- 本方法无本地 PDF（官方来源均为网页）。
- 延伸：GitHub Privacy Statement 与 Terms Section D/E/J 全文；社区讨论 #188488 下的大量用户反馈；Copilot Memory 的 docs 页（docs.github.com/copilot/how-tos/use-copilot-agents/copilot-memory，抓取不可达，标题经搜索核实）；Microsoft Work IQ GitHub 仓库（github.com/microsoft/work-iq）。
