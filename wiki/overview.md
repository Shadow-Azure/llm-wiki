---
type: overview
created: 2026-08-23
updated: 2026-09-26
sources:
  - "raw/2026-04-04-karpathy-llm-wiki.md"
  - "raw/2026-08-23-obsidian-docs-build-a-plugin.md"
  - "raw/2026-08-23-obsidian-docs-submit-your-plugin.md"
  - "raw/2026-08-23-obsidian-docs-developer-policies.md"
  - "raw/2026-08-23-dsh-docs-plugin-publish.md"
  - "raw/2026-08-23-dsh-docs-subsystem-extensions.md"
  - "raw/2026-09-05-hkuds-videorag-vimo.md"
  - "raw/2026-09-05-omagent-video-understanding.md"
  - "raw/2026-09-05-hkuds-videoagent.md"
  - "raw/2026-09-05-player-embedded-ai-snapshot.md"
  - "raw/2026-09-05-video-understanding-api-products.md"
  - "raw/2026-09-05-video-agent-academic-landscape.md"
  - "raw/2026-09-05-media-extended-v4-no-ai.md"
  - "raw/2026-09-06-oss-video-extension-landscape.md"
  - "raw/2026-09-06-open-notebook-architecture.md"
  - "raw/2026-09-06-bilinote-architecture.md"
  - "raw/2026-09-06-content-core-formats.md"
  - "raw/2026-09-06-obsidian-oss-alternatives-landscape.md"
  - "raw/2026-09-26-ai-ran-architecture-overview.md"
  - "raw/2026-09-26-oran-sc-architecture.md"
  - "raw/2026-09-26-ai-ran-wg1-whitepaper.md"
  - "raw/2026-09-26-ai-ran-wg3-whitepaper.md"
  - "raw/2026-09-26-ai-ran-platform-infrastructure-orchestrator.md"
  - "raw/2026-09-26-nvidia-ai-ran-solutions.md"
  - "raw/2026-09-26-nvidia-ai-aerial-developer-portal.md"
  - "raw/2026-09-26-nvidia-telecom-6g-ai-native-platforms.md"
  - "raw/2026-09-26-nvidia-t-mobile-physical-ai-ai-ran.md"
  - "raw/2026-09-26-ocudu-realtime-ai-ran-platform.md"
  - "raw/2026-09-26-ai-ran-alliance-mwc-2026-progress.md"
  - "raw/2026-09-26-nvidia-ai-native-ran-spectral-efficiency.md"
  - "raw/2026-09-26-softbank-ai-for-ran-verification.md"
  - "raw/2026-09-26-softbank-aitras-roi-and-architecture.md"
  - "raw/2026-09-26-avidthink-ai-in-mobile-ran.md"
  - "raw/2026-09-26-mwc-shanghai-china-ai-native-network-context.md"
---

# 综述

本库是一个**通用个人知识库**，按 [[wiki/concepts/llm-wiki|LLM Wiki]] 模式运行：人收集来源和提问，LLM 编译并维护全部 wiki 页面。

## 当前图景（2026-09-26）

- **规模**：84 个来源（raw，另有 `self-opt/` 用户自管检索库未计入），65 个知识页面（sources 29 / concepts 31 / entities 5）。〔2026-09-26 研究核定〕
- **已确立的核心认识**：知识管理的胜负手不在检索而在**维护**——RAG 每次查询重新推导、没有积累；把知识编译进互相链接的持久页面，维护成本由 LLM 压到趋近于零，知识才能复利。见 [[wiki/concepts/wiki-vs-rag|编译式知识库 vs RAG]]。
- **本库即方法的自证**：仓库自身就是该模式的实例，方法论源头是 [[wiki/sources/karpathy-llm-wiki|Karpathy 的原文]]。

## 正在形成的主题

- 知识管理 / LLM 与个人工具：[[wiki/concepts/note-tool-landscape|笔记工具格局]]已两次深化（2026-08-23 营垒划分 + 协议商用含义；2026-09-06 22 仓 star/协议/活跃度实测 + 二次开发分级），核心发现："高 star""开源协议""可二次开发"三轴不重合（star 最高的是 Notion 形态、PKM 三强全 AGPL、Anytype/Outline 实为源码可见非开源），而 Obsidian"纯 Markdown + 双链"的生态位在开源世界确实无人完整占据——为"AI 维护型知识库"选择 Obsidian 的论证再获一侧证
- **平台扩展性 / 插件机制**：双锚点展开——Obsidian（[[wiki/concepts/obsidian-plugin-development|开发]] / [[wiki/concepts/obsidian-plugin-distribution|分发]] / [[wiki/concepts/obsidian-plugin-networking|网络访问]]，官方目录 + 审核的闭环）与 [[wiki/entities/deepseek-harness|deepseek-harness]]（[[wiki/concepts/dsh-plugin-development|开发]] / [[wiki/concepts/dsh-plugin-distribution|分发]] / [[wiki/concepts/dsh-plugin-networking|网络与动态加载]]，无特权内核 + 无官方市场）；[[wiki/concepts/plugin-mechanism-comparison|三方对比]]（含 VSCode、[[wiki/entities/claudian|Claudian]] 实例）是两条主线的汇合点
- **视频播放插件构想双平台化**：[[wiki/concepts/video-player-plugin-feasibility|Obsidian 版]]（demo 已验证）与 [[wiki/concepts/dsh-video-plugin-feasibility|dsh 版]]（Web UI 有官方扩展点，且多出"agent 动态生成插件"路线）——同一功能在两种插件哲学下的实现对照，本身正在成为理解两类平台的试金石
- **AI 视频助手格局（2026-09-05 research 起）**：[[wiki/concepts/ai-video-assistant-landscape|五层格局]]确立——平台原生与浏览器扩展层播放器 AI 已拥挤（且全为 transcript-based）、API 层时间戳索引商品化 + MCP 标准化、开源 agent 框架走向"理解+剪辑+重制"一体；"本地视频库 + 个人知识库 + 播放器内嵌 AI"与"视觉理解进个人笔记"被裁决为真实空位，直接改变两个视频插件构想页的差异化定位；此主题与 [[wiki/concepts/wiki-vs-rag]] 汇合——视频域的"预索引 vs 长上下文直塞"是编译式知识库论点的模态重现。**2026-09-06 深化**：[[wiki/concepts/open-notebook-architecture|open-notebook 实读]]（个人 KM 品类的 transcript-first 教科书实例，视频入库即失时间戳）与 [[wiki/concepts/bilinote-architecture|BiliNote 实读]]（同题异构对照：B站三级取数 + 字幕优先编排）合读即"个人视频工具架构课"；[[wiki/concepts/yt-dlp|yt-dlp]] 概念页确立视频-AI 工具链最上游取料工的地位，并记下取数策略谱系（纯字幕 API ↔ 字幕+下载兜底）
- **dsh 双文档面（2026-08-30 起）**：插件生态面（[[wiki/sources/dsh-official-docs|官方文档 32 页]] → 开发/分发/网络/前端/能力边界五页）+ 运行时架构面（[[wiki/sources/dsh-source-runtime|源码仓实读 v0.1.2-alpha.1]] → [[wiki/concepts/dsh-agent-loop|agent loop]] / [[wiki/concepts/dsh-session-log|session log]] / [[wiki/concepts/dsh-tool-pipeline|tool pipeline]] / [[wiki/concepts/dsh-coordination-subsystems|协调子系统]] 四页）。运行时面的核心发现：dsh 把"策略"统一为管线上的 waterfall 监听者，把自治全部限幅（goal 256 轮、jobs 唤醒预算 3、resume 必须人工 re-arm）
- **AI 原生通信与 AI-RAN（2026-09-26 research 起）**：[[wiki/concepts/ai-ran-foundations-learning-path|AI-RAN 基础学习路径]]把主题拆成 RAN 基础、vRAN/O-RAN、通信线、服务线、厂商方案五站；[[wiki/concepts/ai-ran|AI-RAN]] 整理官方三分法与架构；[[wiki/concepts/ai-ran-training-inference-infrastructure|训练/模型/推理基建]]落到数据、训练、模型、推理和编排五层。当前可确认的认识：AI-RAN 不是单一协议，官方概念区分 AI-for-RAN、AI-on-RAN 与 AI-and-RAN；O-RAN 提供开放 RAN 与 RIC/SMO 基础但不自动等于 AI-RAN。2026 年 MWC 已出现 33 个 demo，重点从可行性转向 GPU partitioning、workload orchestrator、D4AI 数据质量、模型生命周期、数字孪生和边缘推理。代表性数据包括 NVIDIA ML beamforming 1.62x、SoftBank AI-for-RAN UL +20%/SRS +13%/MAC +8%、SynaXG 单 GH200 上 5G+LLM 推理+AI 训练 36 Gbps/sub-10 ms、China Mobile/ZTE RAN-native AI >15%。这些数字均需按厂商/试验口径复查
- **AI-RAN 数据资产谱系（2026-09-26 research 展开）**：[[wiki/concepts/ai-ran-training-inference-infrastructure|训练/模型/推理基建]]把“脱敏现场、合成信道、数字孪生回放、联邦/梯度级”四类资产拆成生成机制、数据粒度、部署阶段和风险。合成信道解决罕见样本与隐私采集；数字孪生解决不可在生产网乱试的反事实策略；联邦/梯度解决原始数据不能集中但模型需跨站点泛化。三者都要经 HIL/OTA/shadow/灰度闭环，最终服务可回滚、可审计的 AI-RAN 模型。Sionna/AODT 和 O-RAN FL 论文提供了方法侧证据，但不能替代独立商用验证
- **数字孪生与联邦学习专页（2026-09-26 research 细化）**：[[wiki/concepts/ai-ran-digital-twin-replay-data|数字孪生回放数据]]澄清“配置生成数据”只是起点，完整资产还包括标定、时间轴状态机、baseline/counterfactual 分支、训练/验证场景拆分和可复现数据包；AODT 区分 EM mode 与 RAN mode，输出 Parquet/Iceberg 与 H5 IQ。[[wiki/concepts/ai-ran-federated-gradient-data|联邦/梯度级数据]]解释本地模型确实存在，但从同一全局模型聚合更新可解决本地样本不足和非 IID；FedAvg/FedProx/Secure Aggregation/DP 与 NVIDIA FLARE/Flower/OpenFL/TFF 构成工程底座
- **AI-RAN 的协议栈映射（2026-09-26 query-filed）**：[[wiki/concepts/ai-ran-training-model-ran-function-mapping|训练模型与传统 RAN 功能映射]]明确当前 AI 不等于“替代整个 L1/L2”：信道估计/SRS 预测对应 PHY 测量，DRL link adaptation 对应外环 MCS 决策，ML beamforming/MU-MIMO 对应空间信道处理，neural receiver 对应均衡/解调/失真补偿，AI MAC scheduling 对应资源分配，LLM/AIOps 主要落在秒级策略和运维。RLC/PDCP、HARQ 状态机、编解码和 RF 仍受协议与确定性约束，通常不是当前模型直接重写的对象。
- **与 self-opt 检索库的汇流**：[[raw/self-opt/INDEX.md|自进化四层图谱]]（harness/skill 层的 17 个方法核实）与 dsh 源码研究在此相交——dsh 的 skill 写入即生效 + goal/continuable subagent + feedback 留白，是"用户数据驱动自进化"唯一的**开源可自托管**完整实现样本；对照页待做。〔2026-09-06 lint 裁定（用户确认）：self-opt 维持"用户自管检索库"例外定位，wiki 直链 `raw/self-opt/*` 路径可接受，不建 source 页〕

## 开放问题

- Karpathy 原文评论区有大量社区实践（团队规模经验、并发 ingest、人工修正的存活机制），尚未剪藏——值得作为第二个来源。
- Karpathy 提到 "the wiki is a persistent, compounding artifact"，但**复利何时开始递减**（规模、矛盾积累、维护成本回升）原文未展开，暂无库内答案。
- Obsidian 插件"装了即全权"的信任模型（同进程、无沙箱）与市场审核之间的张力：官方治理完全靠入口审核 + 政策约束，无运行时隔离——见 [[wiki/concepts/plugin-mechanism-comparison|三方对比]] 洞察 3。
- dsh 动态插件的隔离边界：`dynamicCordisRunner` / `dsh-tool-cordis` 允许 agent 自写自挂插件（内存态、卸载即消失），官方自认"可能影响同一进程中的其他会话"——审批闸门能守住"谁批准"，守不住"批准后跑飞"，运行时隔离缺位与 Obsidian 同病（见 [[wiki/concepts/dsh-plugin-networking]]）。
- dsh 无官方插件市场：发现层完全外包给 npm/GitHub topic + 社区自建市场（300+ 插件收录），质量与安全把关机制如何演化值得跟踪（社区市场来源待剪藏）。
- **视频域开放问题（2026-09-05 research 登记）**：①"转写 QA 够不够"无第三方裁决——HKUDS VideoAgent 的 Q&A 只需 Whisper vs TwelveLabs 宣称 segment"不是读转录稿"；②学术（纯视觉 captioning）与工程（ASR-first）路线未收敛；③YouTube Gemini Ask 的可用范围（Premium/地区/语言）未核实；④新一代视频 LM（Video-MME 榜首 video-SALMONN 2+，81.6%）会否侵蚀"必须预索引"的前提；⑤Vimo Beta 发布后的真实体验（宣传与现实有时间差：awesome-video-agents 称已发布，README 却 Coming Soon）。
- **笔记工具域开放问题（2026-09-06 research 登记）**：①AppFlowy 接替 AppFlowy-Cloud 的自托管后端形态未明（直接关系其二次开发评估）；②Logseq DB 版稳定安装版无时间表（社区已等约两年）；③AS Notes（VS Code wikilink + 纯 Markdown，Foam 潜在后继）star/协议未实测。
- **AI-RAN 域开放问题（2026-09-26 research 登记）**：①AI-for-RAN 模型跨厂商/频段/站点的泛化与安全回退；②GPU/加速器在硬实时 RAN 的独立 TCO、能耗和故障数据；③NEF/NWDAF、RAIE/R1、CAMARA 如何收敛成 AI-on-RAN API；④AI Grid/AI factory 的真实商业计量与监管口径；⑤OCUDU、3GPP、O-RAN、AI-RAN Alliance、Linux Foundation 的接口边界；⑥China Mobile/ZTE、Huawei/ZTE、AsiaInfo 与国内 5G-A/ISAC/算力网络如何在术语和接口上与 AI-RAN Alliance 对齐。

## 下一步

1. **ingest `raw/2026-08-26 self-improving.md`**——2026-09-06 lint 裁定为全库唯一未 ingest 来源（AI Harness 自进化综述：定义 / DeepSeek·小米·上海 AI Lab·MiniMax·腾讯·EverMind·Google 各家做法 / 学界质疑）。它是 self-opt 汇流主题缺的行业背景篇，且直接触及 [[wiki/entities/deepseek-harness]] 与 [[wiki/concepts/cordis-vendoring]]（Cordis 被媒体称"自进化的后悔药"）；ingest 时顺带对 dsh 页面群做全文矛盾复查
2. 视频主题待跟进线索见 [[wiki/concepts/ai-video-assistant-landscape]] 与开放问题节（YouTube Ask 可用范围核实、Vimo Beta 实测、hoverNotes 机制剪藏）
3. dsh×self-opt 对照专页仍待做（2026-08-30 登记的旧账；做成 concept 页）

> lint 记录（2026-09-06）：69 raw 中 68 已被 14 个来源页覆盖；42 页 frontmatter 全合规；零孤儿页、零真实断链；index/log 同步到位；数据缺口维持两项（Karpathy gist 评论区、dsh 社区插件市场来源）。
