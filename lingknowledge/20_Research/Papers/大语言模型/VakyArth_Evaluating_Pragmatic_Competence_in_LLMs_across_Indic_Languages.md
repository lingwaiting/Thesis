---
date: "2026-09-03"
paper_id: "arXiv:2609.01788"
title: "VakyArth: Evaluating Pragmatic Competence in LLMs across Indic Languages"
authors: "Usneek Singh, Poorvaja Veera Balaji Kumar, Parth Nanda, Anand Madhusoodanan, Geyang Guo, Wei Xu, Junyi Jessy L"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 语用推理
  - Pragmatics
  - 多语言
  - 基准测试
  - Indic-Languages
quality_score: "7.5/10"
created: "2026-09-03"
updated: "2026-09-03"
status: analyzed
---

# VakyArth: Evaluating Pragmatic Competence in LLMs across Indic Languages

## 核心信息
- **论文ID**：arXiv:2609.01788
- **作者**：Usneek Singh, Poorvaja Veera Balaji Kumar, Parth Nanda, Anand Madhusoodanan, Geyang Guo, Wei Xu, Junyi Jessy L
- **机构**：--
- **发布时间**：2026-09-01
- **会议/期刊**：--（arXiv 预印本，cs.CL / cs.AI）
- **链接**：[arXiv](http://arxiv.org/abs/2609.01788v1) | [PDF](https://arxiv.org/pdf/2609.01788v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Real-world communication often requires pragmatic reasoning: interpreting meanings implied through context and cultural convention rather than stated literally. Existing pragmatic evaluation remains largely limited to English and high-resource languages, leaving Indic languages unexplored despite their linguistic and cultural diversity. We introduce VakyArth, the first pragmatic benchmark for Indic languages, designed as a diagnostic evaluation covering Hindi, Punjabi, Tamil, and Malayalam. VakyArth evaluates models across five phenomena: deixis, speech acts, implicature, social pragmatics, and coherence; through multiple-choice questions, natural language inference, and translation, with all items authored by native speakers. Across multilingual large language models (LLMs) of varying families and sizes, we find consistent failures on pragmatic meanings rooted in Indic linguistic and cultural conventions. Our analysis shows systematic differences across languages and tasks: MCQ accuracy exceeds NLI accuracy in all model-language combinations, translation performance does not reliably track pragmatic understanding, and Indo-Aryan languages show a translation advantage over Dravidian languages. We further show that automatic translation metrics can miss fluent but pragmatically unfaithful outputs, especially for implicature and deixis.

### 中文翻译
现实世界的交流往往需要语用推理：解读通过上下文与文化惯例暗示、而非字面陈述的含义。现有的语用评估大多局限于英语等高资源语言，印度语系（Indic）语言尽管在语言与文化上高度多元，却长期未被探索。作者提出 **VakyArth**，首个面向印度语系语言的语用基准，作为覆盖印地语、旁遮普语、泰米尔语和马拉雅拉姆语的诊断性评估。VakyArth 从五个语用现象（指示语、言语行为、含义/蕴涵、社会语用、连贯性）评估模型，任务形式包括多项选择、自然语言推理与翻译，所有条目均由母语者撰写。在不同家族与规模的多语言大语言模型（LLM）上，作者发现模型在植根于印度语言与文化惯例的语用含义上持续失败。分析揭示了跨语言与跨任务之间的系统性差异：在所有模型-语言组合中，MCQ 准确率均高于 NLI 准确率；翻译性能并不能可靠地反映语用理解；印度-雅利安语系相比达罗毗荼语系展现出翻译优势。作者还进一步表明，自动翻译指标可能漏掉"流畅但语用上不忠实"的输出，尤其在含义与指示语上。

### 核心要点提炼
- **研究背景**：语用推理评估几乎只覆盖英语等高资源语言，印度语系被忽略。
- **研究动机**：填补印度语系语言语用能力评估的空白。
- **核心方法**：首个 Indic 语用基准 VakyArth，覆盖 4 种语言、5 个语用现象、3 类任务，母语者撰写。
- **主要结果**：多语言 LLM 在印度语系语用含义上持续失败；MCQ > NLI；翻译性能与语用理解脱钩。
- **研究意义**：为多语言 LLM 的"深层语用能力"评估提供诊断工具。

## 研究背景与动机

### 领域现状
LLM 的语用能力（pragmatic competence）评估是近年的研究热点，但已有基准（如 Implicatures 类任务）几乎只覆盖英语和少数高资源语言。

### 现有方法的局限性
1. **语言覆盖单一**：印度语系语言（超过 10 亿使用者）在语用评估中几乎空白。
2. **文化惯例被忽略**：语用含义高度依赖文化与语境，翻译式评估无法捕捉。
3. **翻译 ≠ 理解**：翻译指标与语用理解之间缺乏对应关系，现有评估存在盲区。

### 研究动机
作者希望为印度语系语言建立首个语用基准，诊断模型对"语境与文化惯例隐含意义"的真实理解能力。

## 研究问题

### 核心研究问题
多语言 LLM 在印度语系语言上的语用能力（deixis、speech acts、implicature、social pragmatics、coherence）表现如何？翻译能力能否作为语用理解的代理指标？

## 方法概述

### 核心思想
构建一个由母语者撰写、覆盖 4 种语言 × 5 种语用现象 × 3 类任务的诊断性基准，系统测量并对比模型在"字面理解"与"语用理解"之间的差距。

### 方法框架

#### 整体架构
![[Benchmark-Design-Page-2.drawio_page1.png|800]]

> 图1：VakyArth 基准设计，展示语言、语用现象与任务类型的多维覆盖。

![[introduction-Page-4.drawio_page1.png|600]]

> 图2：引言示意图，说明语用推理（pragmatic reasoning）与字面理解的差异。

#### 各模块详细说明

**模块1：语言覆盖**
- 印地语、旁遮普语（印度-雅利安语系），泰米尔语、马拉雅拉姆语（达罗毗荼语系）。

**模块2：语用现象（5 类）**
- 指示语（deixis）、言语行为（speech acts）、含义/蕴涵（implicature）、社会语用（social pragmatics）、连贯性（coherence）。

**模块3：任务形式（3 类）**
- 多项选择（MCQ）、自然语言推理（NLI）、翻译。

## 实验结果

### 数据集
VakyArth：4 种语言、5 个语用现象、全部条目由母语者撰写；另含 54 条孟加拉语方言 meme 的补充集（该部分见 MemeCULT-1K）。

### 主要结果
- 多语言 LLM 在印度语系语用含义上**持续失败**。
- **MCQ 准确率 > NLI 准确率**（所有模型-语言组合）。
- **翻译性能与语用理解脱钩**：翻译好 ≠ 语用理解好。
- 印度-雅利安语系相较达罗毗荼语系有**翻译优势**。
- 自动翻译指标会漏掉"流畅但语用不忠实"的输出（尤其 implicature 与 deixis）。

![[nli_confusion_page1.png|600]]

> 图3：NLI 混淆矩阵，展示模型在不同语用现象上的混淆模式。

## 深度分析

### 研究价值评估
- **理论贡献**：首次为 Indic 语言建立语用基准，揭示"翻译能力 ≠ 语用能力"。
- **实际应用**：为多语言 LLM 评测、低资源语言能力诊断提供工具。

### 方法优势
1. **母语者撰写**，保证语用含义的文化真实性。
2. **多维诊断**（语言 × 现象 × 任务），能定位具体失败模式。
3. **翻译脱钩的发现**具有方法论警示意义。

### 局限性
- 覆盖 4 种语言，仍属印度语系子集；其他低资源语系有待扩展。
- 基准规模与区分度需更大规模验证。

## 技术路线定位
本文属于 **多语言 LLM 评估 / 语用推理** 路线，与 Implicit 推理、pragmatic reasoning 基准一脉相承，特色在于把语用评估推进到低资源、文化高度依赖的印度语系语言。

## 未来工作建议
1. 扩展至更多低资源语言与语系。
2. 探索"显式文化知识注入"是否可提升语用能力。
3. 设计更细粒度的语用失败归因指标。

## 我的综合评价

### 价值评分
**7.5/10** — 填补空白、方法严谨，但作为评估基准的创新性与影响力取决于社区采纳度。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 首个 Indic 语用基准，填补空白 |
| 技术质量 | 8/10 | 母语者撰写、多维诊断 |
| 实验充分性 | 7/10 | 覆盖多家族模型，但规模有限 |
| 写作质量 | 8/10 | 清晰 |
| 实用性 | 7/10 | 评估工具价值，落地场景较窄 |

> [!tip] 关键启示
> "翻译得好"不等于"理解得对"——自动翻译指标会漏掉流畅但语用不忠实的输出，多语言能力评测需要专门的语用维度。

> [!success] 推荐指数
> ⭐⭐⭐ 值得关注——多语言 LLM 评测方向，对低资源语言能力研究有参考价值。
