---
type: source
created: 2026-09-05
updated: 2026-09-05
sources:
  - "raw/2026-09-05-video-understanding-api-products.md"
---

# 视频理解 API 层与垂直产品多源快照（TwelveLabs / VideoDB / 通义听悟 / 飞书妙记 / tl;dv / Knowt / BibiGPT）

**一句话**：商业侧"预分析视频→提问/干活"分两层——**API/基础设施层**（TwelveLabs、VideoDB、通义听悟）已把索引+时间戳+agent 接口完全商品化；**垂直应用层**（会议 tl;dv、学习 Knowt、中文全能 BibiGPT）用 ASR+LLM 文本管线解决场景问题；两层都不占"本地播放器内嵌 AI"形态。

## 核心要点

- **TwelveLabs**：Marengo（多模态 embedding 索引）+ Pegasus（video LM，2 小时时序推理）双模型；agent 产品 Jockey；检索/切分/高光/粗剪/洞察全齐；开发者交付（API+SDK+MCP+Playground，SOC 2，可私有化），无终端播放器。
- **VideoDB**：自我定位"面向 AI agent 的实时视频基础设施"——摄取/时间切分/多模态时间戳索引（语音/场景/人物/物体/动作/事件）/检索返回**可播放片段**；SDK+REST+**MCP server** 三通道；配套开源 agent 框架 Director（search/edit/compile/generate）；支持 RTSP 直播流。
- **通义听悟**：ASR+翻译+说话人分离→LLM 摘要/章节速览/待办；AutoChapters 输出毫秒级 Start/End+标题+摘要 JSON（粒度可配 4~15 章/小时）；PPT 视觉抽取（帧提取+ASR 时间戳对齐）；API"问答"是问答回顾（抽取已有 QA 对）**非**交互式提问。
- **会议/学习垂直**：tl;dv 跨录像自然语言问答+聚合洞察（LLM 外采 Anthropic，文本管线）；Knowt chat-with-video+闪卡测验（ASR 路线，timestamp 零出现）；飞书妙记转录+AI 纪要+章节+待办（未见自由问答）。
- **BibiGPT**：中文区"链接→时间戳总结+对话"独立应用，**agent 接口化**是亮点——MCP server / OpenAPI / NLWeb /ask SSE / Anthropic Skill 四个入口，把视频理解变成第三方 agent 可调用的工具。

## 值得注意的细节

- "AI 输出带时间戳"在 API 层是标准能力（TwelveLabs segment、听悟 AutoChapters、VideoDB 时间戳索引）——下游播放器集成的技术障碍已不存在。
- 厂商自报数字需保留态度：tl;dv 200 万用户、BibiGPT 100 万用户、TwelveLabs"Video-MME 第一"。

## 后续线索

- VideoDB MCP server 的工具集清单（agent 可用动作的真实边界）值得剪藏。
- 听悟消费端网页是否已有提问式交互，需实测。
