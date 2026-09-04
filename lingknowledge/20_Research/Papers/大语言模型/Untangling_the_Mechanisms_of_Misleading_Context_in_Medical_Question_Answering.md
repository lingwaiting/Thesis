---
date: "2026-09-04"
paper_id: "arXiv:2609.02754"
title: "Untangling the Mechanisms of Misleading Context in Medical Question Answering"
authors: "Robin Linzmayer, Noémie Elhadad"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 医疗问答
  - 可解释性
  - 机制分析
  - 上下文污染
  - 推理追踪
quality_score: "8.0/10"
created: "2026-09-04"
updated: "2026-09-04"
status: analyzed
---

# Untangling the Mechanisms of Misleading Context in Medical Question Answering

## 核心信息
- **论文ID**：arXiv:2609.02754
- **作者**：Robin Linzmayer, Noémie Elhadad
- **机构**：--
- **发布时间**：2026-09-02
- **会议/期刊**：--（arXiv 预印本，cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](http://arxiv.org/abs/2609.02754v1) | [PDF](https://arxiv.org/pdf/2609.02754v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models now answer medical questions with expert-level performance. However, the context these systems act on can be misleading, and misleading context can corrupt a model's medical judgment. To understand how misleading context corrupts this judgment, we examine the model's susceptibility to the context, disclosure of it, mechanism of corrupted reasoning, and monitorability of the decision. On the medical reasoning subset of MedMisBench, a clinician-reviewed question-answering benchmark of 8,627 questions, we inject two types of misleading context cues, fabricated evidence and a bare assertion. We test three reasoning models, two that expose their full reasoning trace and one frontier model that exposes only its response. All three are more susceptible to the assertion than to the fabricated evidence, adopting the asserted answer 10 to 27 points more often. The misleading cues are disclosed in 81 to 98% of traces but only 7 to 90% of responses, and the assertion is disclosed less often than evidence based cues. Resampling from reasoning traces without disclosure shows the two cues corrupt reasoning differently, evidence entering early and accumulating while the assertion redirects the conclusion near its end. An LLM monitor catches 78% of corrupted decisions at 5% false positives when reading an open model's trace with guidance, against at most 32% from any response. The misleading context that models are most susceptible to is disclosed least, and was caught reliably only from an open reasoning trace, which frontier providers withhold.

### 中文翻译
大语言模型如今在回答医学问题时已达到专家级表现。然而，这些系统所依赖的上下文可能是误导性的，而误导性上下文会侵蚀模型的医学判断。为理解误导性上下文如何破坏这一判断，作者考察了模型对上下文的易感性、其对上下文的披露、被侵蚀推理的机制，以及决策的可监控性。在 MedMisBench 的医学推理子集（一个由临床医生审校、含 8,627 道题的问答基准）上，作者注入了两类误导性上下文线索：编造证据与赤裸断言。测试了三个推理模型，其中两个暴露完整推理轨迹、一个前沿模型只暴露其回复。三个模型都对断言比对编造证据更易感，采纳断言答案的频率高出 10 到 27 个百分点。误导性线索在 81% 到 98% 的推理轨迹中被披露，但仅在 7% 到 90% 的回复中被披露，且断言比基于证据的线索更少被披露。对不含披露的推理轨迹进行重采样显示，两类线索以不同方式侵蚀推理：证据在早期进入并逐步累积，而断言在接近结束时才扭转结论。当读取开放模型的轨迹并给予引导时，一个 LLM 监控器能在 5% 假阳性率下抓住 78% 的被侵蚀决策，而任何回复最多只能抓到 32%。模型最易感的误导性上下文恰恰披露得最少，且只能通过开放推理轨迹被可靠捕获——而前沿供应商恰恰不提供这一点。

### 核心要点提炼
- **研究背景**：LLM 已能专家级回答医学问题，但检索/外部上下文可能被污染。
- **研究动机**：弄清误导性上下文如何侵蚀 LLM 的医学判断，而非仅仅测量"是否被侵蚀"。
- **核心方法**：在 MedMisBench 上注入两类误导线索（编造证据 vs 赤裸断言），从易感性、披露、机制、可监控性四维度解剖。
- **主要结果**：断言比证据更危险（采纳率 +10~27pp）；线索多在轨迹中披露却在回复中沉默；LLM 监控器读开放轨迹可抓 78% 被侵蚀决策。
- **研究意义**：揭示"最易感的误导最不被披露"这一机制悖论，凸显开放推理轨迹对医疗安全的必要性。

## 研究背景与动机

### 领域现状
LLM 在医学问答上逼近专家水平，临床部署常借助检索增强（RAG）或外部知识库注入上下文。但真实医疗场景中，上下文可能来自不可信来源或含误导信息。

### 现有方法的局限性
1. **只看结果不看机制**：现有工作多测量"上下文是否改变答案"，缺乏对侵蚀过程的机制性理解。
2. **忽视披露差异**：推理模型"想了什么"与"说了什么"之间的披露落差未被系统研究。
3. **监控能力不清**：能否从轨迹/回复中监控出被侵蚀的决策，缺乏量化基准。

### 研究动机
作者希望从机制层面解开"误导性上下文如何破坏医学判断"，从而为医疗 AI 的可信部署与监控提供依据。

## 研究问题

### 核心研究问题
误导性上下文究竟在推理过程的哪个阶段、以何种方式侵蚀模型的医学判断？模型是否披露了这种侵蚀？能否被外部监控器可靠捕获？

## 方法概述

### 核心思想
把"误导性上下文"细分为两种线索（编造证据、赤裸断言），在 MedMisBench 上注入，并从四个正交维度（易感性、披露、侵蚀机制、可监控性）对三个推理模型进行对照解剖。

### 方法框架

#### 整体架构
![[composite_uptake_page1.png|600]]

> 图1：误导性线索的采纳率（uptake）对比——三个模型对断言与编造证据的易感性差异。

#### 各模块详细说明

**模块1：线索注入与易感性测量**
- 在 MedMisBench 医学推理子集（8,627 题）注入两类线索：编造证据（fabricated evidence）与赤裸断言（bare assertion）。
- 对比三模型：两个暴露完整推理轨迹的开放模型 + 一个仅暴露回复的前沿模型。

**模块2：披露分析**
- 统计误导线索在"推理轨迹"与"最终回复"中的披露比例。
- 发现线索在轨迹中披露 81-98%，在回复中仅 7-90%，且断言披露更少。

**模块3：侵蚀机制（重采样）**
- 从"不含披露"的推理轨迹重采样，追踪证据 vs 断言如何影响推理链。
- 证据早期进入并逐步累积；断言在推理末尾扭转结论。

**模块4：可监控性**
- 用 LLM 监控器分别读取开放轨迹（带引导）与最终回复。
- 开放轨迹可抓 78% 被侵蚀决策（5% FPR），回复最多 32%。

## 实验结果

### 数据集
MedMisBench 医学推理子集（临床医生审校，8,627 题）。

### 主要结果
- **易感性**：三模型对断言比编造证据更易感，采纳断言答案频率高出 **10~27pp**。
- **披露落差**：线索在轨迹中披露 **81-98%**，在回复中仅 **7-90%**；断言比证据披露更少。
- **机制差异**：证据在早期进入并累积，断言在接近结束时扭转结论。
- **可监控性**：LLM 监控器读开放轨迹抓 **78%** 被侵蚀决策（5% FPR），回复最多 **32%**。

![[disclosure_composite_page1.png|600]]

> 图2：误导线索的披露情况——轨迹 vs 回复、断言 vs 证据的披露对比。

![[monitor_auroc_recall_page1.png|600]]

> 图3：LLM 监控器在不同信号源（开放轨迹 vs 回复）下的检测性能。

## 深度分析

### 研究价值评估
- **理论贡献**：首次系统区分"编造证据"与"赤裸断言"两种误导机制，揭示它们以不同时序方式侵蚀推理链。
- **实际应用**：为医疗 RAG 系统的监控、开放推理轨迹的必要性提供直接证据。

### 方法优势
1. **机制导向**：不止测量"被侵蚀率"，而是解剖侵蚀发生的阶段与方式。
2. **多维对照**：易感性、披露、机制、可监控性四维度正交，结论扎实。
3. **现实意义强**：直指前沿供应商"隐藏推理轨迹"带来的医疗安全风险。

### 局限性
- 仅覆盖医学问答单域，跨领域泛化未验证。
- 三类线索（仅证据 vs 断言）之外的误导形式（如微妙的指代误导）未覆盖。
- 监控器依赖"带引导"的开放轨迹，无引导下的性能未充分报告。

## 技术路线定位
本文属于 **LLM 可信推理 × 医疗安全** 路线，与"上下文污染 / 提示注入 / 推理可解释性"一脉相承，特色在于把"误导上下文"从攻击向量升级为可机制解剖的研究对象。

## 未来工作建议
1. 扩展到更多领域（法律、金融）与更多误导形式（含微妙指代、部分真相）。
2. 研究无引导、无完整轨迹条件下的监控方法。
3. 探索在推理过程中主动检测与阻断误导线索的防御机制。

## 我的综合评价

### 价值评分
**8.0/10** — 机制分析视角新颖、结论对医疗 AI 部署有现实警示意义，但实验聚焦单域、监控依赖开放轨迹。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 把误导上下文机制化、类型化分析，视角新颖 |
| 技术质量 | 8/10 | 四维度对照 + 重采样实验，方法严谨 |
| 实验充分性 | 7/10 | 单域、三类线索，跨域泛化未验证 |
| 写作质量 | 8/10 | 清晰 |
| 实用性 | 8/10 | 医疗 RAG 安全与监控价值高 |

> [!tip] 关键启示
> 模型最易被侵蚀的误导（赤裸断言）恰恰披露得最少、只能靠开放推理轨迹抓取——这对隐藏 CoT 的前沿模型构成医疗场景下的真实安全风险。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 值得精读——机制性拆解"误导上下文"，为医疗 LLM 的可信部署与监控提供了难得的实证视角。
