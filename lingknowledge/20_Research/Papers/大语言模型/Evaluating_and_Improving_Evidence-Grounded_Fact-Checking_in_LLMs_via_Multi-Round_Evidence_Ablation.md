---
date: "2026-09-10"
paper_id: "arXiv:2609.08943"
title: "Evaluating and Improving Evidence-Grounded Fact-Checking in LLMs via Multi-Round Evidence Ablation"
authors: "Xingyu Deng, Mingzi Cao, Nikolaos Aletras, Xi Wang, Mark Stevenson"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Fact-Checking
  - RAG
  - Evidence-Grounding
  - Verification
quality_score: "8.0/10"
related_papers: []
created: "2026-09-10"
updated: "2026-09-10"
status: analyzed
---

# Evaluating and Improving Evidence-Grounded Fact-Checking in LLMs via Multi-Round Evidence Ablation

## 核心信息
- **论文ID**：arXiv:2609.08943
- **作者**：Xingyu Deng, Mingzi Cao, Nikolaos Aletras, Xi Wang, Mark Stevenson
- **机构**：--
- **发布时间**：2026-09-08
- **会议/期刊**：--
- **链接**：[arXiv](https://arxiv.org/abs/2609.08943) | [PDF](https://arxiv.org/pdf/2609.08943)
- **分类**：cs.CL, cs.AI, cs.IR

## 摘要翻译

### 英文摘要
Automatic fact-checking systems assess the veracity of claims given evidence from relevant documents. Large Language Models (LLMs) have demonstrated strong performance in fact-checking due to their general reasoning capabilities. However, it remains unclear whether they faithfully make use of the evidence provided to reach veracity judgments or rely on parametric knowledge. To investigate this, we introduce Fact-Ablated Evaluation (FAE), a new evaluation framework that iteratively ablates the cited evidence to assess whether LLMs revise their predictions accordingly. Our empirical results show that current off-the-shelf LLMs as fact-checking systems rely more on their parametric knowledge than on the evidence provided. To bridge this gap between prediction accuracy and evidence grounding, we propose REAL (Rigorous Evidence Ablation Learning), a training framework that promotes evidence-dependent verification through counterfactual evidence supervision for the LLM-as-verifier models. Experiments on four fact-checking datasets across different domains demonstrate that models trained with REAL obtain superior evidence-dependent capabilities compared to standard fine-tuned models. Our findings highlight that strong fact-checking performance can still coexist with weak evidence dependency, while REAL encourages veracity predictions to remain more closely tied to the availability of supporting evidence.

### 中文翻译
自动事实核查系统依据相关文档中的证据来判定声明（claim）的真伪。大语言模型（LLM）凭借通用推理能力在事实核查上表现出色，但它们究竟是「忠实使用所提供的证据」还是「依赖参数化记忆」尚不清楚。为此，本文提出 Fact-Ablated Evaluation（FAE）评估框架，通过**迭代消融被引用的证据**来检验模型是否会相应地修改其预测。实证结果表明，现成的 LLM 作为事实核查系统，更多依赖其参数化知识而非所提供的证据。为弥合「预测准确率」与「证据依赖」之间的鸿沟，作者进一步提出 REAL（Rigorous Evidence Ablation Learning）训练框架，通过反事实证据监督（counterfactual evidence supervision）来促进「LLM 作为验证器」的证据依赖式核查。在四个跨领域事实核查数据集上的实验表明，用 REAL 训练的模型相比标准微调模型具备更强的证据依赖能力。这一发现强调：**强的事实核查性能可以与弱的证据依赖共存**，而 REAL 使真实性预测更紧密地绑定于支持证据的可用性。

### 核心要点提炼
- **研究背景**：LLM 事实核查虽准确率高，但可能只是「背答案」而非「看证据」。
- **研究动机**：需要一个能区分「证据依赖」与「参数记忆」的评估手段，以及能增强前者的训练方法。
- **核心方法**：评估侧 FAE（迭代证据消融）+ 训练侧 REAL（反事实证据监督）。
- **主要结果**：现成 LLM 证据依赖弱；REAL 训练后证据依赖能力显著优于标准微调。
- **研究意义**：揭示「准确率 ≠ 证据依赖」这一关键问题，并给出可操作的训练解法。

## 研究背景与动机

### 领域现状
事实核查（fact-checking）是检索增强生成（RAG）与可信 AI 的关键环节。LLM 在给定证据文档的条件下，能对声明给出高准确率的真伪判断，已成为事实核查的主流方案。

### 现有方法的局限性
但「准确率高」并不等于「真的用了证据」。模型可能只是把训练时记住的参数化知识当作答案来源，一旦证据缺失或被替换，其预测未必随之改变——这正是**幻觉（hallucination）与过度自信**的温床。现有评估几乎只测准确率，无法衡量「证据依赖」程度。

### 研究动机
作者提出一个关键问题：**如果消融掉模型引用的证据，模型的判断会不会改变？** 若基本不变，说明模型其实在靠参数记忆作答。基于此，需要新的评估框架与训练方法来强化证据依赖。

## 研究问题

### 核心研究问题
1. 如何量化 LLM 事实核查中的「证据依赖」程度？
2. 现成 LLM 的证据依赖究竟有多弱？
3. 如何通过训练让模型真正「看证据」做判断？

## 方法概述

### 核心思想
用「消融实验」的思想反过来评估与训练事实核查模型：评估时逐轮消融被引用的证据，观察预测是否随之修正；训练时用反事实证据监督，强制模型把真伪预测绑定到证据的可用性上。

### 方法框架

#### 整体架构
![[fae_example_eng_page1.png|800]]

> 图1：FAE（Fact-Ablated Evaluation）框架示意——迭代消融被引用的证据，检验模型是否相应修正其真伪预测。

#### 各模块详细说明

**模块1：FAE（Fact-Ablated Evaluation）评估框架**
- **功能**：评估模型是否忠实依赖证据。
- **输入**：声明 + 相关证据文档。
- **处理流程**：
  1. 让模型基于证据给出真伪判断并引用证据；
  2. 迭代消融被引用的证据（逐条移除）；
  3. 观察模型预测是否随证据消失而改变。
- **输出**：证据依赖度指标。

**模块2：REAL（Rigorous Evidence Ablation Learning）训练框架**
- **功能**：通过反事实证据监督，训练「证据依赖式」验证器。
- **关键技术**：反事实证据（counterfactual evidence）监督——构造与事实相悖或移除证据的情形，让模型学会「证据变了，结论就该变」。

### 关键创新
1. **FAE 评估框架**——首次把「证据消融」系统化为衡量证据依赖的评估方法。
2. **REAL 训练框架**——用反事实证据监督直接优化证据依赖，而非仅优化准确率。
3. **发现「准确率与证据依赖可脱钩」**——重要的实证结论。

## 实验结果

### 实验设置
- **数据集**：4 个跨领域事实核查数据集
- **基线**：现成 LLM、标准微调模型
- **方法**：REAL 训练模型

### 主要结果
1. 现成 LLM 做事实核查时，预测更多依赖参数化知识而非证据（FAE 消融后预测基本不变）。
2. REAL 训练的模型在 4 个数据集上均获得优于标准微调的证据依赖能力。
3. 结论：强性能可与弱证据依赖共存；REAL 让真伪预测更紧贴证据可用性。

## 深度分析

### 研究价值
- **理论贡献**：提出「证据依赖」这一独立于准确率的评估维度，并给出量化方法。
- **实际应用**：直接服务于可信 AI、RAG、幻觉治理等场景。
- **领域影响**：为事实核查从「追求准确率」转向「追求可验证性」提供了方法论支撑。

### 优势
- 问题定位精准（准确率与证据依赖脱钩）
- 评估（FAE）与训练（REAL）形成闭环
- 反事实监督的思路可迁移到其他「LLM 作为判断器」任务

### 局限性
- 证据消融的粒度与方式可能影响评估稳定性
- 4 个数据集的领域覆盖仍有限
- 未深入探讨多跳、相互矛盾的证据情形

### 适用场景
- 事实核查 / 验证器系统的可靠性评估与训练
- RAG 系统中「回答是否真正基于检索证据」的审计

## 我的综合评价

### 价值评分
- **总体评分**：**8.0/10** — 问题重要、方法闭环的可靠事实核查工作

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 把证据消融系统化为评估+训练框架，思路清晰 |
| 技术质量 | 8/10 | 反事实监督设计合理 |
| 实验充分性 | 8/10 | 4 数据集跨领域验证，对比充分 |
| 写作质量 | 8/10 | 论证链条完整 |
| 实用性 | 9/10 | 直接面向幻觉治理与可信 AI，落地价值高 |

### 突出亮点
- 用「消融证据看预测是否改变」这一朴素思想，精准暴露 LLM 的「伪证据依赖」
- REAL 提供了一条可操作的、增强证据依赖的训练路径

## 相关论文
- [[Noēsis_Deterministic-First_Retrieval_with_Two-Tier_Context_Hydration_for_Factuality-Critical_Queries_on_Small_Local_Models|Noēsis]] - 同属事实性/证据依赖方向的检索工作

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.08943)
- [PDF](https://arxiv.org/pdf/2609.08943)

> [!tip] 关键启示
> 事实核查模型「答对了」≠「看了证据」——评估与训练都应把「证据依赖」当作独立于准确率的核心指标。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐关注可信 AI、RAG 幻觉治理与事实核查的研究者阅读。
