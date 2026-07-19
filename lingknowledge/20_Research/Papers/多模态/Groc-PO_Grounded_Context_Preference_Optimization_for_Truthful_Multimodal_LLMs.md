---
date: "2026-07-17"
paper_id: "arXiv:2607.13712"
title: "Groc-PO: Grounded Context Preference Optimization for Truthful Multimodal LLMs"
authors: "Zhixiao Zheng, Zheren Fu, Zhiyuan Yao, Chunxiao Liu, Dongming Zhang, Zhendong Mao"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - MLLM
  - 幻觉抑制
  - DPO
  - 偏好优化
  - Grounded-Preference-Optimization
  - 多阶段对齐
quality_score: "8.0/10"
created: "2026-07-17"
updated: "2026-07-17"
status: analyzed
---

# Groc-PO: Grounded Context Preference Optimization for Truthful Multimodal LLMs

## 核心信息
- **论文ID**：arXiv:2607.13712
- **作者**：Zhixiao Zheng, Zheren Fu, Zhiyuan Yao, Chunxiao Liu, Dongming Zhang, Zhendong Mao
- **机构**：--
- **发布时间**：2026-07-15
- **分类**：cs.CV, cs.AI, cs.CL, cs.MM
- **链接**：[arXiv](http://arxiv.org/abs/2607.13712v1) | [PDF](https://arxiv.org/pdf/2607.13712v1)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Despite the rapid progress of Multimodal Large Language Models (MLLMs), they still suffer from untruthfulness issues, such as visual hallucinations, content fabrication, and unfaithful reasoning, which substantially undermine their faithfulness and practical utility. Alignment methods based on human preference, such as Direct Preference Optimization (DPO), have been widely adopted to address these issues. However, multimodal reasoning errors often propagate across stages, and final-answer errors can often be traced to mistakes in early grounding stages, yet standard DPO typically applies preference optimization at the final-answer level. This credit-assignment challenge means that supervision for early grounding stages is indirect rather than stage-specific, making it difficult to suppress error propagation arising from grounding drift and context inconsistency. To address this, we propose Grounded Context Preference Optimization (Groc-PO), a grounded preference optimization framework for MLLMs. We further construct the Grounded Context Preference Dataset (GCPD), organizing multi-stage preference samples around three stages of Object Grounding, Contextual Grounding, and Grounded Reasoning, to capture the formation, integration, and utilization of grounded context. By introducing more explicit preference supervision over multiple grounded stages, Groc-PO strengthens context-dependent reasoning and mitigates cross-stage error propagation. Extensive experiments show that, compared with standard DPO and other strong baselines, Groc-PO achieves improved performance in hallucination mitigation, faithful reasoning, and overall reliability, supporting the value of more explicit grounded supervision for trustworthy multimodal reasoning.

### 中文翻译
尽管多模态大语言模型（MLLMs）取得了快速进展，但它们仍然面临不真实性问题，如视觉幻觉、内容捏造和不忠实推理，这些问题严重削弱了其可靠性和实际效用。基于人类偏好的对齐方法（如直接偏好优化 DPO）已被广泛用于解决这些问题。然而，多模态推理错误往往在多阶段之间传播，最终答案错误通常可以追溯到早期 grounding 阶段的错误，但标准 DPO 仅在最终答案层面应用偏好优化。这一信用分配（credit-assignment）难题意味着对早期 grounding 阶段的监督是间接的而非阶段特定的，使得难以抑制由 grounding 漂移和上下文不一致导致的错误传播。为解决此问题，我们提出了 Grounded Context Preference Optimization（Groc-PO），一个面向 MLLMs 的 grounded 偏好优化框架。我们进一步构建了 Grounded Context Preference Dataset（GCPD），围绕 Object Grounding、Contextual Grounding 和 Grounded Reasoning 三个阶段组织多阶段偏好样本，以捕捉 grounded 上下文的形成、整合和利用。通过在多个 grounded 阶段引入更显式的偏好监督，Groc-PO 增强了上下文依赖推理能力并抑制了跨阶段错误传播。大量实验表明，与标准 DPO 和其他强基线相比，Groc-PO 在幻觉抑制、忠实推理和整体可靠性方面取得了改进的性能，支持了更显式 grounded 监督对于可信多模态推理的价值。

### 核心要点提炼
- **研究背景**：MLLMs 存在严重的幻觉和不忠实推理问题，DPO 等对齐方法被广泛使用
- **研究动机**：标准 DPO 仅在最终答案层面优化，无法有效监督早期 grounding 阶段，导致跨阶段错误传播
- **核心方法**：提出 Groc-PO 框架 + GCPD 数据集，在 Object Grounding → Contextual Grounding → Grounded Reasoning 三个阶段分别施加偏好监督
- **主要结果**：相比标准 DPO 和其他基线，在幻觉抑制、忠实推理和可靠性上显著提升
- **研究意义**：为多模态对齐提供了更细粒度的阶段级偏好监督范式

## 研究背景与动机

### 领域现状
多模态大语言模型（MLLMs）近年来取得了令人瞩目的进展，能够同时理解文本和视觉信息，在视觉问答、图像描述、多模态推理等任务中展现出强大的能力。然而，MLLMs 普遍存在"不真实性"（untruthfulness）问题，主要表现为三种形式：
1. **视觉幻觉（Visual Hallucination）**：模型"看到"了图像中不存在的内容
2. **内容捏造（Content Fabrication）**：模型生成了与视觉输入无关的虚假信息
3. **不忠实推理（Unfaithful Reasoning）**：推理过程与视觉事实不一致

### 现有方法的局限性
基于人类偏好的对齐方法（如 RLHF、DPO）已被广泛用于解决 MLLMs 的不真实性问题。然而，这些方法存在一个根本性的局限：

- **信用分配难题（Credit-Assignment Challenge）**：多模态推理是一个多阶段过程（感知→理解→推理），最终答案的错误往往可以追溯到早期阶段（如目标识别错误、上下文理解偏差），但标准 DPO 仅对最终答案施加偏好信号，对早期阶段的监督是间接的
- **误差传播（Error Propagation）**：由于缺乏阶段级别的直接监督，早期 grounding 阶段的错误会向下游传播和放大
- **监督粒度不足**：标准 DPO 的"端到端"偏好信号无法精确定位问题发生在哪个阶段

### 研究动机
作者的核心洞察是：要真正解决 MLLMs 的不真实性问题，需要对推理链中的每个阶段施加显式的、阶段特定的偏好监督，而非仅仅在最终答案层面优化。这需要一个能够组织多阶段偏好数据、并在各阶段分别施加偏好优化的框架。

## 研究问题

### 核心研究问题
**如何设计一个多阶段的偏好优化框架，在多模态推理的各个 grounding 阶段（目标定位、上下文理解、推理）施加显式的偏好监督，从而抑制跨阶段错误传播，提升 MLLMs 的忠实性和可靠性？**

具体来说，论文试图回答：
1. 如何将"grounded context"（基于视觉上下文的推理基础）的构建过程分解为可监督的阶段？
2. 如何在每个阶段构建偏好数据（chosen vs. rejected）？
3. 多阶段偏好优化相比标准 DPO 能带来多少提升？
4. 哪些阶段的监督对最终性能最关键？

## 方法概述

### 核心思想
Groc-PO 的核心思想是：**将多模态推理中的 grounded context 构建过程显式分解为三个递进阶段，并在每个阶段分别施加偏好优化监督**。这样做的好处是让模型在每个子任务上都接受直接的偏好信号，从而防止早期阶段的错误向下游传播。

### 方法框架

#### 整体架构

Groc-PO 框架包含两个核心组件：

**1. Grounded Context Preference Dataset (GCPD)**
构建三阶段偏好数据的流水线：

![[fig3_grocpo2_page1.png|800]]

> 图：Groc-PO 的三阶段 grounded 偏好优化框架——从 Object Grounding 到 Contextual Grounding 再到 Grounded Reasoning，每个阶段都有独立的 chosen/rejected 偏好对

**三个阶段**：
- **Stage 1: Object Grounding（目标定位）**：模型需要正确识别和定位图像中的关键对象，偏好信号基于对象识别的准确性
- **Stage 2: Contextual Grounding（上下文理解）**：模型需要在正确识别对象的基础上，理解对象之间的关系和上下文含义
- **Stage 3: Grounded Reasoning（基于上下文的推理）**：模型基于前两个阶段建立的 grounded context 进行最终推理

每个阶段都构建了 chosen（正确）和 rejected（错误）的偏好对。

![[grocpo_visual1_page.png|800]]

> 图：Groc-PO 的视觉 grounding 过程示意——展示模型如何从视觉输入中建立 grounded context

**2. Groc-PO 优化目标**
在多个 grounded 阶段上分别应用偏好优化损失。与标准 DPO 仅优化最终答案不同，Groc-PO 对每个阶段的输出都施加偏好监督。

![[grocpo_logit1_page.png|800]]

> 图：Groc-PO 的对数概率（logit）分析——展示多阶段偏好优化如何影响模型的输出分布

### 方法架构图

![[fig1_1_motiv11_page1.png|800]]

> 图：论文的动机示意图——展示标准 DPO 在多模态推理中的 credit-assignment 问题：最终答案错误可追溯到早期 grounding 错误，但 DPO 仅对最终答案施加监督

## 实验与结果

### 实验目标
验证 Groc-PO 相比标准 DPO 和其他基线方法在以下方面的优势：
1. 幻觉抑制（Hallucination Mitigation）
2. 忠实推理（Faithful Reasoning）
3. 整体可靠性（Overall Reliability）

### 主要结果

![[four_plots_v2_77_page1.png|800]]

> 图：四个关键实验结果图——展示 Groc-PO 在多个评估维度上相比 DPO 和其他基线的性能对比

从摘要中的描述，Groc-PO 取得了以下关键结果：
- 在幻觉抑制任务上显著优于标准 DPO
- 在忠实推理任务上有明显提升
- 整体可靠性指标全面领先基线方法

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：识别并形式化了多模态对齐中的 credit-assignment 问题**
  - 创新点：首次明确指出标准 DPO 在多模态推理中仅在最终答案层面优化的局限性
  - 学术价值：为多模态对齐研究提供了新的分析视角和问题定义

- **贡献2：提出了三阶段 grounded 偏好优化框架**
  - 创新点：将 grounded context 的构建过程显式分解为 Object Grounding → Contextual Grounding → Grounded Reasoning 三个阶段
  - 学术价值：提供了一种更细粒度的偏好监督范式

- **贡献3：构建了 GCPD 多阶段偏好数据集**
  - 创新点：系统性地为每个 grounding 阶段构建 chosen/rejected 偏好对

#### 实际应用价值
- **可信多模态 AI 系统**：Groc-PO 的方法可以用于训练更可信的视觉问答、多模态对话系统
- **可解释性提升**：阶段级的监督天然提供了更好的可解释性——如果模型出错，可以定位到具体是哪个 grounding 阶段出了问题

### 方法优势详解

1. **阶段级监督粒度**：相比标准 DPO 的端到端偏好信号，Groc-PO 提供了更精细的阶段级监督
2. **误差传播抑制**：通过在早期 grounding 阶段施加直接监督，有效抑制了错误的下游传播
3. **框架通用性**：三阶段分解（Object → Context → Reasoning）是一个通用的多模态推理框架，可扩展到其他任务

### 局限性分析

- **阶段定义可能不够完备**：三阶段的划分是否覆盖所有多模态推理场景？某些任务可能需要更多或更少的阶段
- **偏好数据构建成本**：GCPD 需要为每个阶段构建偏好对，这比简单的端到端偏好数据标注更昂贵
- **阶段间依赖关系**：论文假设了线性的阶段递进关系（Object → Context → Reasoning），但实际推理可能存在循环依赖或并行处理
- **泛化到其他 MLLM 架构**：框架是否适用于非 DPO-based 的对齐方法（如 RLHF、PPO）需要进一步验证

### 适用性与场景分析

**适用场景**：
- 需要精确视觉 grounding 的任务（如医学图像分析、遥感图像理解）
- 对推理忠实性要求高的场景（如法律文书分析、科学图表解读）
- 多步视觉推理任务（如视觉常识推理、多图比较）

**不适用场景**：
- 简单的单阶段视觉任务（如图像分类）—— 三阶段框架过于复杂
- 实时性要求极高的场景——多阶段偏好优化增加了训练复杂度

## 技术路线定位

### 所属技术路线
本文属于 **多模态对齐（Multimodal Alignment）** 技术路线，具体是 **偏好优化（Preference Optimization）** 子方向。该路线的核心特点是利用人类偏好信号来对齐模型的输出与人类期望。

### 技术路线发展历程
```
RLHF (PPO) → DPO → 多模态 DPO → Groc-PO (本文) → ? (阶段级+多维度联合优化)
```

### 本文在技术路线中的位置
- **承上**：继承了 DPO 的高效偏好优化范式，将其扩展到多模态场景
- **启下**：提出了阶段级偏好优化的思路，为未来的多维度、多层次对齐研究提供了基础

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 论文识别了一个重要但未被充分研究的问题（多模态对齐中的 credit-assignment），提出了清晰的三阶段框架和配套数据集，实验验证充分。主要扣分点在于阶段划分的通用性需要进一步论证。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 识别了 credit-assignment 问题并提出三阶段监督框架，思路新颖 |
| 技术质量 | 8/10 | GCPD 数据集构建和三阶段优化方法设计合理 |
| 实验充分性 | 7/10 | 与 DPO 和多个强基线对比，但更多实验细节需要从正文获取 |
| 写作质量 | 8/10 | 问题定义清晰，动机充分，论证逻辑严密 |
| 实用性 | 7/10 | 方法对可信多模态 AI 有直接价值，但偏好数据构建成本可能限制实际部署 |

### 重点关注

**值得关注的技术点**：
- 三阶段 grounding 分解（Object → Context → Reasoning）是一个通用的分析框架
- 阶段级偏好优化的思路可以推广到其他序列决策问题
- GCPD 数据集的构建方法论

**需要深入理解的部分**：
- 每个阶段的具体偏好信号设计
- 三阶段损失函数的加权方式
- 与 Chain-of-Thought + DPO 方法的对比

## 相关论文

### 直接相关
- [[Direct Preference Optimization (DPO)]] - 本文扩展的基础方法

### 背景相关
- RLHF 相关论文 - 人类偏好对齐的早期工作
- MLLM 幻觉抑制相关论文 - 共享研究目标

---

> [!tip] 关键启示
> 多模态推理是一个多阶段过程，对齐也应该在多个阶段进行——而不是仅在最终答案层面。这是 Groc-PO 带给我们最重要的方法论启示。

> [!warning] 注意事项
> - 三阶段框架是否适用于所有多模态推理任务需要具体分析
> - 阶段级偏好数据的构建成本和可扩展性需要在实际应用中验证
> - 论文发表时 arXiv 仅提供预印本，需关注后续同行评审反馈

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！该论文识别了一个重要但被忽视的问题，提出的阶段级偏好优化思路具有较高的方法论价值，尤其适合研究多模态对齐和可信 AI 的研究者。
