---
type: clip
source:
  - "https://www.alibabacloud.com/blog/zero-code-transformation-observability-in-practice-with-loongsuite_602515（2025-09-04）"
title: "零代码改造：LoongSuite 可观测实践（Python monkey patch / Go toolexec 原理 + Dify 端到端实战）"
author: Alibaba Cloud Native Community
accessed: 2026-10-31
via: web_fetch 博客全文
clip_note: 三种语言非侵入采集原理讲得最直白的一篇；含 Dify 一键接入的完整实操。注意：Dify 演示部分功能当时为商业版能力，将逐步开源（博客自注）。
---

# 一、AI 原生应用典型架构与可观测需求

- 典型链路：用户（浏览器/小程序/Android/iOS）→ Higress 网关统一入口（安全防护/流量治理）→ 模型应用层（Dify/LangChain/Spring AI Alibaba 等多语言框架）→ 模型服务（托管或自部署 Qwen/DeepSeek，多模型部署按成本/重要性/流量策略切换，通常经 Higress AI 网关代理）。
- 执行链路很长，任一节点出问题业务就失败。三个需求：①单次调用经过了哪些组件并用调用链串起来（端到端诊断）；②全栈可观测数据关联（trace + GPU 利用率等指标关联分析，判断问题在应用层还是模型层）；③模型日志了解每次调用输入输出，做评测验证。
- LLM 应用特殊需求：agent 执行任务 token 消耗大、耗时长，需记录每一步；多轮交互中间过程消耗惊人甚至死循环（"MCP token 黑洞"）；每次修改发布都要"回归测试"（评测），依赖大量可观测数据。
- Gartner 当时预测：到 2025 年超过 70% 的企业将部署至少一个 agent。

# 二、非侵入采集原理（三种语言三种姿势）

## Python Agent：monkey patch（运行时替换）
- Python 允许运行时动态修改函数（含属性与方法）。初始化阶段把原函数的引用替换为包装函数：包装函数在原函数执行前后插入逻辑（类似装饰器模式），原函数照常调用。全程不改用户代码。

## Go Agent：编译期插桩（toolexec + AST）
- 前身：alibaba/opentelemetry-go-auto-instrumentation。
- Go 不像 Java 可运行时字节码增强——Go 编译产物是直接可执行二进制。
- 方案：利用 Go 的 `go toolexec` 能力，在编译器前端与后端之间插入中间层；对前端生成的 AST 做任意修改，基于预定义 tracking rules 注入监控代码（span 创建、指标统计、上下文透传），再交给后端编译。
- 优点：不改代码只改编译命令；走完整 Go 编译流程，天然支持所有 Go 运行场景，避免不可预测错误。

## Java Agent：字节码增强
- （本篇简述，详见开源公告篇）运行时动态修改 bytecode，50+ 组件自动埋点。

覆盖面自评（博客）：微服务（http/db/redis/mq 多插件）+ AI agent 框架（LangChain、MCP、Dify），可看 LLM 调用输入输出、耗时、token 数，用于优化 prompt、提升接入效率、降低 token 消耗。> "与其他开源探针相比，LoongSuite 追踪覆盖更广、功能支持更全、语言支持更广。"

# 三、Dify 可观测实战（端到端）

## Dify 架构（1.0 后插件化）
- 浏览器 → 本地 Nginx 统一入口 → WebUI 前端（控制台渲染）+ Dify API（Flask 应用：应用配置/对话/数据管理）。
- API 依赖：PostgreSQL（应用配置/对话记录/多租户，经 SQLAlchemy）；Redis（缓存/会话 + Celery 消息队列存异步任务如 embedding、可观测数据上传）；OSS（上传文件/知识库文档/证书）。
- 1.0+ 插件架构：dify-plugin-daemon 负责按参数启停管理插件生命周期、经 SSE 协议调用 LLM、有状态、负责所有插件输入输出、支持动态加载与本地/远程插件、可反向调用 Dify 内部服务。Models 与 Tools 迁入插件体系，新增 Agent Strategies/Extensions/Bundles 插件类型 + Dify Marketplace。

## 接入步骤（非侵入）
1. 一键部署 Dify 到 SAE 或 ACK。
2. dify-api（Python/Flask）：安装 ack-onepilot 组件，按文档给 dify-api deployment 加几个 label/开关 → 立即接入。
3. dify-plugin-daemon（Go）：编译期增强——Dockerfile 构建命令把 `go build` 换成 `./aliyun-go-agent-linux-amd64 go build`（其余不变），重新构建镜像替换。
4. 控制台配置一个聊天应用，与后端 LLM 对话。

## 效果
- 同一界面同时看 dify-api（Python）与 dify-plugin-daemon（Go）的监控数据；两者调用链成功串联 → Dify 端到端可观测。

## 与其他 Dify 观测方案对比（博客结论）
- Dify 原生只能看内部孤岛链路、OTel 只能看平台链路 → LoongSuite 补齐端到端全旅程。
- 接入成本更低：一次接入全应用生效；非侵入（无需逐个改 LLM 应用）；不受 gevent 等运行时限制；后续升级维护成本低。
- 弥补原生方案在多维观测、全局分析、端到端旅程追踪上的不足。

# 四、未来展望（博客时点）
- 模型侧推理观测（vLLM、sglang 等推理加速引擎）；GPU profiling；更多 LLM 插件（Google ADK、a2a 等）。

# 后续线索
- ack-onepilot 组件（ACK 一键接入的载体）机制未剪藏。
- 商业版（ARMS）与开源版功能边界未系统盘点（README 中 Go Agent 有"商业版"徽章链到 ARMS 文档）。
