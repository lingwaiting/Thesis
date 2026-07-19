---
date: "2026-07-19"
paper_id: "2607.15095"
title: "Digital Pantheon: Simulating and Auditing Coalition Formation with LLM Agents"
authors: "Dylan Van Mulders, Matthias Bogaert, Dirk Van den Poel"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Agent
  - 多智能体
  - 政治科学
  - 计算社会科学
  - RAG
  - DPO
  - 联盟形成
quality_score: "8.2/10"
created: "2026-07-19"
updated: "2026-07-19"
status: analyzed
---

# Digital Pantheon: Simulating and Auditing Coalition Formation with LLM Agents

## 核心信息
- **论文ID**：2607.15095
- **作者**：Dylan Van Mulders, Matthias Bogaert, Dirk Van den Poel
- **机构**：Ghent University（比利时根特大学）
- **发布时间**：2026-07-16
- **会议/期刊**：arXiv 预印本
- **链接**：[arXiv](https://arxiv.org/abs/2607.15095) | [PDF](https://arxiv.org/pdf/2607.15095)
- **引用**：--

## 摘要翻译

### 英文摘要
The formation of political coalitions is a complex negotiation driven by both concrete policy objectives and deep-seated ideological convictions. While Large Language Models (LLMs) open new avenues for computational political science, the neutrality and helpfulness biases instilled by Reinforcement Learning from Human Feedback (RLHF) prevent them from sustaining steadfast partisan behaviour. We present a multi-agent framework that reconciles factual grounding with ideological alignment by combining Supervised Fine-Tuning (SFT), Direct Preference Optimization (DPO), and Retrieval-Augmented Generation (RAG): DPO instils aggressive party-specific personas, while a per-party RAG pipeline keeps each agent bounded to its official manifesto. We operationalize the framework on the 2019 Flemish election, deploying the partisan agents in a hub-and-spoke negotiation arbitrated by a formateur. To make the emergent negotiation interpretable, we introduce a Multi-Layered Information Lineage Topology (MILT) that traces every clause in the final agreement back to its manifesto origin and classifies it into five provenance states, a Coalition Influence Score (CIS) that aggregates these traceable contributions to identify which party shaped the agreement, and a real-world grounding pass that benchmarks each simulated provision against the historically adopted coalition agreement. Across three independent simulations the framework yields a stable winner and ranking (N-VA ahead of CD&V and Open Vld), and manifesto-anchored lineage reliably predicts real-world materialization whereas hallucinated content does not. The result is a transparent, scalable testbed for the ex-ante exploration of party compatibility and formateur-mediated compromise.

### 中文翻译
政治联盟的形成是一个复杂的谈判过程，由具体的政策目标和深层的意识形态信念共同驱动。虽然大语言模型为计算政治科学开辟了新途径，但 RLHF 灌输的中立性和有帮助性偏置阻止了它们维持坚定的党派行为。我们提出一个多智能体框架，通过结合 SFT、DPO 和 RAG 来协调事实基础与意识形态对齐：DPO 赋予激进的党派特定人格，而每个党派的 RAG 管道将每个智能体限定在其官方宣言范围内。我们在 2019 年佛兰德选举上实施该框架，将党派智能体部署在由组阁者（formateur）仲裁的 hub-and-spoke 谈判中。为使涌现的谈判可解释，我们引入了多层信息溯源拓扑（MILT），追踪最终协议中每条条款到其宣言来源并分类为五种溯源状态；联盟影响力评分（CIS），聚合这些可追溯的贡献以识别哪个党派塑造了协议；以及现实世界基准测试，将每个模拟条款与历史上实际通过的联盟协议进行对比。在三次独立模拟中，框架产生稳定的获胜者和排名（N-VA 领先 CD&V 和 Open Vld），宣言锚定的溯源可靠地预测现实世界中的实现，而幻觉内容则不能。结果是一个透明、可扩展的测试平台，用于事前探索党派兼容性和组阁者调解的妥协。

### 核心要点提炼
- **研究背景**：LLM 的中立性偏置阻碍其在政治科学中模拟真实党派行为
- **研究动机**：需要一个既能保持意识形态一致性、又能追踪决策溯源的透明政治模拟框架
- **核心方法**：SFT+DPO+RAG 三阶段构建党派智能体 + Hub-and-spoke 谈判 + MILT/CIS 可解释性审计
- **主要结果**：三次独立模拟产生一致的获胜者排名，宣言溯源预测真实条款
- **研究意义**：为计算政治科学提供可解释、可审计的 LLM Agent 模拟范式

## 研究背景与动机

### 领域现状
政治联盟谈判是人类政治中最复杂的多边博弈之一。传统政治科学依赖博弈论模型和历史案例分析，但难以捕捉真实谈判的动态性和复杂性。LLM 的出现为计算政治科学提供了新的可能，但面临核心挑战：
- **RLHF 中立性偏置**：标准对齐训练使 LLM 倾向于提供中立、平衡的回应，难以维持一致的政治立场
- **幻觉问题**：LLM 可能生成看似合理但无事实依据的谈判条款

### 研究动机
如何在保持 LLM 事实准确性（RAG）的同时，赋予其稳定、可审计的意识形态立场？政治模拟的透明性和可解释性至关重要——需要知道每条协议条款的来源和影响。

## 研究问题

**核心研究问题**：能否构建一个透明的 LLM 多智能体框架，既维持党派意识形态一致性，又可审计谈判结果的溯源和影响力分布？

## 方法概述

### 核心思想
**"Digital Pantheon"** — 将 LLM Agent 塑造为具有特定党派意识形态的数字代理人，通过结构化谈判和可解释性溯源实现政治联盟形成的透明模拟。

### 方法框架

![[ASH_chunking_overview_page1.png|800]]

> 图1：Digital Pantheon 框架概览。包含党派智能体构建（SFT+DPO+RAG）、Hub-and-spoke 谈判架构和 MILT/CIS 审计层。

#### 各模块详细说明

**模块1：党派智能体构建（三阶段）**
- **SFT（监督微调）**：在党派宣言和政治文本上微调，建立基础知识
- **DPO（直接偏好优化）**：注入激进的党派特定人格——偏好符合本党立场的回应，拒绝中立或妥协表述
- **RAG（检索增强生成）**：每个党派智能体配备独立的 RAG 管道，检索其官方宣言，确保所有主张可追溯到真实政策文档
- **关键设计**：RAG 管道是**按党派隔离**的——每个智能体只能访问自己党派的宣言

**模块2：Hub-and-Spoke 谈判架构**
- **Formateur（组阁者/中心节点）**：中立的仲裁者，接收各党派提案，起草协议草案，协调修改
- **Partisan Agents（党派智能体/spoke 节点）**：代表各政党，基于自身意识形态和宣言进行提案、评估和反提案
- **多轮交互**：提案 → 反馈 → 修改 → 投票，模拟真实联盟谈判

**模块3：MILT — 多层信息溯源拓扑**
- 追踪最终协议中每条条款到其原始宣言来源
- 将每条条款分类为五种溯源状态：
  1. **直接溯源**：可直接追溯到某党宣言
  2. **间接溯源**：经协商修改但仍可识别来源
  3. **综合条款**：多个党派提案的融合
  4. **组阁者原创**：由 formateur 提出
  5. **幻觉/无源**：无法追溯到任何宣言
- 这是本文最重要的方法论贡献

**模块4：CIS — 联盟影响力评分**
- 聚合 MILT 的可追溯贡献
- 量化每个党派在最终协议中的实际影响力
- 考虑因素：条款采纳率、修改程度、谈判中的让步模式

## 实验结果

### 实验设置
- **场景**：2019 年佛兰德（比利时）议会选举
- **参与党派**：N-VA、CD&V、Open Vld 等主要政党
- **数据**：各党官方选举宣言、历史联盟协议
- **评估**：三次独立模拟 + 与历史真实联盟协议对比

### 主要结果

**1. 稳定的获胜者排名**
- 三次独立模拟一致产生 N-VA > CD&V > Open Vld 的影响力排名
- 与实际选举结果和政治分析一致

**2. 宣言溯源预测现实**
- MILT 标记为"宣言锚定"的条款在历史上实际实现的概率显著高于随机
- "幻觉/无源"条款几乎从未在真实协议中出现
- **核心结论**：信息溯源质量直接预测模拟的现实相关性

**3. 与历史协议对比**
- 模拟协议与 2019 年佛兰德实际联盟协议在关键条款上高度一致
- Formateur 调解机制有效减少了党派间的僵局

### 关键发现
- **DPO 对维持意识形态一致性至关重要**：仅用 SFT+RAG 的智能体会在谈判中逐渐趋向中立
- **RAG 隔离是关键设计**：允许跨党派 RAG 访问会导致智能体立场混淆
- **MILT 提供了前所未有的透明度**：可以精确定位每条协议条款的政治来源

## 深度分析

### 研究价值评估

#### 理论贡献
- **计算政治科学新范式**：将 LLM Agent 从"生成政治文本"提升到"模拟政治过程"
- **MILT 溯源框架**：通用的信息溯源方法论，可应用于任何多智能体协商场景
- **DPO+RAG 意识形态注入**：解决了 LLM 中立性偏置的实用方法

#### 实际应用价值
- **事前联盟分析**：在选举前模拟可能的联盟组合和谈判结果
- **政策影响评估**：追踪政策条款的来源和影响力
- **谈判培训**：为政治从业者提供模拟谈判训练环境

### 方法优势
1. **透明可审计**：每条款可追溯，影响力可量化
2. **意识形态一致**：DPO+RAG 维持稳定党派立场
3. **可扩展**：框架适用于任意多党制议会民主国家

### 局限性
1. **单案例验证**：仅在 2019 年佛兰德选举上测试
2. **简化假设**：谈判过程比现实简化（如忽略媒体影响、公众舆论等）
3. **宣言中心主义**：假设党派行为完全由宣言驱动，忽略个人领导风格等因素
4. **语言限制**：仅测试了荷兰语/英语场景

### 适用场景
- ✅ **适合**：多党制议会民主国家的联盟形成分析
- ⚠️ **谨慎**：两党制或总统制政治体系
- ❌ **不适合**：非民主政治体系或缺乏公开宣言的政治环境

## 我的综合评价

### 价值评分
**8.2/10** — 跨学科创新突出，MILT 溯源方法优雅，但规模验证有限

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | SFT+DPO+RAG 组合注入意识形态 + MILT 溯源拓扑是全新贡献 |
| 技术质量 | 8/10 | 方法设计严谨，对照组合理 |
| 实验充分性 | 6/10 | 仅单个案例，缺少跨国家/跨选举验证 |
| 写作质量 | 8/10 | 清晰流畅，概念定义明确 |
| 实用性 | 8/10 | 计算政治科学的实用工具，但需要大量本地化适配 |

> [!tip] 关键启示
> MILT 信息溯源拓扑不仅适用于政治模拟，也可推广到任何需要审计 LLM 多智能体协商结果的场景（商业谈判、法律调解、国际合作等）。

> [!warning] 注意事项
> - 模拟结果不等于现实预测，需谨慎解读
> - DPO 注入的意识形态强度需要校准，过强会导致谈判僵局
> - 框架依赖高质量宣言文本，对数据质量要求高

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！跨学科（AI + 政治科学）的典范工作，MILT 溯源方法值得关注。
