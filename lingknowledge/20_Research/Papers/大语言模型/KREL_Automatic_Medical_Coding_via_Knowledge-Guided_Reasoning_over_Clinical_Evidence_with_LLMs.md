---
date: "2026-08-24"
paper_id: "arXiv:2608.20887"
title: "KREL: Automatic Medical Coding via Knowledge-Guided Reasoning over Clinical Evidence with LLMs"
authors: "Xubin Chen, Yipeng Zhou, Wen Sun, Chengkai Huang, Xiaoming Fu, Quan Z. Sheng"
domain: "大语言模型"
tags:
  - 论文笔记
  - Medical-Coding
  - ICD
  - LLM-Reasoning
  - Knowledge-Guided
  - RAG
  - Healthcare-NLP
quality_score: "8.0/10"
created: "2026-08-24"
updated: "2026-08-24"
status: analyzed
---

# KREL: Automatic Medical Coding via Knowledge-Guided Reasoning over Clinical Evidence with LLMs

## 核心信息
- **论文ID**：arXiv:2608.20887
- **作者**：Xubin Chen, Yipeng Zhou, Wen Sun, Chengkai Huang, Xiaoming Fu, Quan Z. Sheng
- **机构**：Macquarie University, The University of New South Wales, University of Göttingen
- **发布时间**：2026-08-21
- **会议/期刊**：--
- **链接**：[arXiv](https://arxiv.org/abs/2608.20887) | [PDF](https://arxiv.org/pdf/2608.20887)

## 摘要翻译

### 英文摘要
Automatic Medical Coding (AMC), which assigns standardized International Classification of Diseases (ICD) codes to clinical notes, is essential for medical reimbursement, quality reporting, and clinical research. Existing pre-trained language model (PLM)-based methods typically formulate AMC as an extreme multi-label classification problem over a predefined code set, while recent large language model (LLM)-based approaches instead frame it as generation or multi-step reasoning. However, key challenges remain, including the extreme length of clinical notes that hinders effective interpretation, the vast ICD label space, and complex coding rules that are not explicitly captured by LLMs. In this work, we propose Knowledge-Guided Reasoning over Clinical Evidence with LLMs (KREL), a framework that leverages LLMs for clinical text understanding and reasoning while integrating external ICD coding guidelines as structured knowledge. This design enables tight coupling between domain knowledge and LLM reasoning, reducing hallucinations and improving compliance with coding standards.

### 中文翻译
自动医疗编码（AMC）将标准化的国际疾病分类（ICD）编码分配给临床病历，是医疗报销、质量报告和临床研究的基础环节。现有基于预训练语言模型（PLM）的方法通常把 AMC 建模为在预定义编码集上的极端多标签分类问题，而近期基于大语言模型（LLM）的方法则把它形式化为生成或多步推理。然而，仍有几个关键挑战：临床病历极长、难以有效解读；ICD 标签空间巨大；以及编码规则复杂，无法被 LLM 显式捕捉。本文提出 KREL（基于 LLM 的临床证据知识引导推理），利用 LLM 进行临床文本理解与推理，同时将外部 ICD 编码指南作为结构化知识整合进来。这一设计使领域知识与 LLM 推理紧密耦合，减少了幻觉，并提高了对编码标准的遵从性。

### 核心要点提炼
- **研究背景**：自动医疗编码是医疗报销/质量报告/临床研究的基础，但临床病历极长、ICD 标签空间巨大、编码规则复杂
- **研究动机**：现有 PLM 方法受限于预定义编码集，LLM 方法无法显式捕捉编码规则、容易幻觉
- **核心方法**：KREL——把 AMC 重构为"层级感知检索 + 规则感知验证"流程，将外部 ICD 编码指南作为结构化知识注入 LLM 推理
- **主要结果**：在 MDACE 上 F1 从最佳基线 0.32 提升到 0.51，recall 从 0.26 提升到 0.53，一致超越 PLM 与 SOTA LLM 基线
- **研究意义**：通过领域知识与 LLM 推理的紧密耦合，同时提升编码准确性与可解释性

## 研究背景与动机

### 领域现状
自动医疗编码（AMC）需要将临床病历映射到标准化 ICD 编码。传统方法分为两派：
- **PLM 路线**：将 AMC 建模为极端多标签分类（如 CAML、LAAT、PLM-ICD），受限于预定义编码子集，难以扩展到完整 ICD 分类体系
- **LLM 路线**：把 AMC 建模为生成或多步推理（如 Code Like Humans、MedDCR），引入 ICD 资源与编码指南，但多数仍无法超越强传统基线（如 PLM-ICD）

### 现有方法的局限性
- **病历极长**：临床病历长度远超标准 PLM 的上下文窗口，难以有效解读
- **标签空间巨大**：完整 ICD 分类体系编码数庞大，直接生成/分类难以覆盖
- **编码规则未被显式建模**：复杂的编码规则（层级、组合、排除项）无法被 LLM 隐式捕捉，易产生幻觉

### 研究动机
需要一种方法，既利用 LLM 的临床文本理解与推理能力，又显式地注入结构化医学知识与编码规则，从而在完整标签空间下提升编码准确性，并保证可解释性与合规性。

## 研究问题

如何在完整 ICD 标签空间下，将结构化医学知识与编码规则显式注入 LLM 推理，实现准确、可解释的自动医疗编码？

## 方法概述

### 核心思想
将 AMC 重构为 **"层级感知检索 + 规则感知验证"** 的过程，而非直接预测编码：
1. 先从病历中抽取**证据支撑的临床查询（evidence-grounded clinical queries）**
2. 利用 ICD 层级结构为每个查询检索候选编码
3. 再结合病历、抽取的证据与编码规则对候选编码进行验证

### 方法框架

#### 整体架构

![[workflow.png|800]]

> 图1：KREL 框架流程。从病历抽取临床查询 → 层级感知检索候选 ICD 编码 → 规则感知验证 → 输出编码。

#### 各模块详细说明

**模块1：证据支撑的查询抽取（Query Formulation）**
- **功能**：从临床病历中抽取证据支撑的临床查询，改善临床表达与 ICD 概念之间的对齐
- **输入**：临床病历
- **输出**：一组证据支撑的临床查询

**模块2：层级感知召回（Structure-Aware Recall）**
- **功能**：利用 ICD 层级结构，为每个查询检索候选编码，提升大标签空间下的候选覆盖率
- **关键技术**：ICD 层级结构的利用

**模块3：重排序（Reranking）**
- **功能**：对召回的候选编码进行排序，优化候选顺序

**模块4：规则注入（Rule Injection）**
- **功能**：注入基于编码指南的依赖关系与规则

**模块5：证据支撑验证（Evidence-Grounded Verification）**
- **功能**：将候选编码与病历、抽取的证据、编码规则进行验证，抑制无支撑的预测

### 关键创新
1. **检索 + 验证范式**：将 AMC 从"直接预测"重构为"层级感知检索 + 规则感知验证"，显式分离编码定位与验证
2. **结构化知识注入**：把外部 ICD 编码指南作为结构化知识，与 LLM 推理紧密耦合，减少幻觉
3. **完整标签空间支持**：通过 ICD 层级检索，突破 PLM 方法受限于预定义编码子集的瓶颈

## 实验结果

### 数据集
- **MDACE**：医疗编码基准数据集
- **MIMIC-IV-Subset**：MIMIC-IV 的编码子集

### 实验设置
- **评估指标**：F1、Recall
- **两种设置**：受限标签空间（restricted label space）、完整标签空间（full label space）
- **基线方法**：PLM-ICD 等强传统基线，以及 SOTA LLM-based 基线

### 主要结果
- **完整标签空间（核心挑战场景）**：
  - MDACE：F1 从最佳基线 0.32 → 0.51，Recall 从 0.26 → 0.53
  - MIMIC-IV-Subset：F1 从 0.33 → 0.39
- **消融**：查询抽取、层级感知召回、重排序、规则注入、证据支撑验证各模块均对最终性能有贡献

### 实验结果图

![[figure1_page1.png|800]]

> 图2：KREL 在不同数据集与标签空间设置下的主结果对比。

## 深度分析

### 研究价值
- **理论贡献**：把 AMC 重构为"检索 + 验证"范式，显式建模编码规则与层级结构
- **实际应用**：直接服务于医疗报销、质量报告、临床研究等落地场景
- **领域影响**：为"领域知识 + LLM 推理"的医疗 NLP 方向提供了可复制框架

### 优势
- 完整标签空间下显著优于强传统基线与 SOTA LLM 基线
- 结构化知识注入减少幻觉，提升编码合规性
- 检索 + 验证流程带来可解释性

### 局限性
- 相比受限标签空间的提升，完整标签空间下的绝对 F1 仍较低（0.51），离实用仍有距离
- 依赖外部 ICD 编码指南的质量与覆盖度
- 未充分讨论跨机构、跨语言病历的泛化能力

### 适用场景
- 需要覆盖完整 ICD 分类体系、而非仅高频编码的医疗编码场景
- 对编码可解释性与合规性有要求的医疗信息学应用

## 与相关论文对比

- 相比 PLM-ICD：KREL 突破预定义编码子集限制，支持完整标签空间
- 相比 Code Like Humans / MedDCR：KREL 通过结构化知识与规则注入，实现更强的编码准确性与可解释性
- 相比直接生成式 LLM 方法：KREL 的检索 + 验证流程显著抑制幻觉

## 技术路线定位

本文属于 **医疗 NLP / 自动医疗编码** 技术路线，具体子方向为"知识引导的 LLM 医疗编码"。承上：继承 PLM 时代的多标签分类与层级建模传统；启下：为 LLM + 结构化医疗知识的推理框架提供了可复现范式。

## 未来工作建议

1. 提升完整标签空间下的绝对性能，缩小与实用门槛的差距
2. 探索更丰富的 ICD 层级与编码规则建模方式
3. 验证跨机构、跨语言病历的泛化能力

## 我的综合评价

### 价值评分
- **总体评分**：**8.0/10** - 框架清晰、实验扎实，但绝对性能离实用仍有距离

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "检索 + 验证"范式与结构化知识注入有清晰创新点 |
| 技术质量 | 8/10 | 各模块设计合理，消融充分 |
| 实验充分性 | 8/10 | 双数据集、双标签空间设置，消融完整 |
| 写作质量 | 8/10 | 结构清晰，问题定位准确 |
| 实用性 | 7/10 | 医疗场景价值高，但绝对 F1 仍偏低 |

### 突出亮点
- 把 AMC 重构为"层级感知检索 + 规则感知验证"
- 结构化 ICD 知识注入，减少幻觉
- 完整标签空间下一致优于强基线

### 重点关注
- 证据支撑的临床查询抽取的具体实现
- 规则注入与证据支撑验证如何协同抑制幻觉

## 相关论文
- [[A_corpus-specific_clinical_RAG_system_matches_or_outperforms_newer_frontier_LLMs_on_HealthBench|Corpus-Specific Clinical RAG]] - 同属医疗 RAG/LLM 方向
- [[A_Cost-Effective_Multimodal_LLM_Reasoning_Framework_for_Question_Answering_over_Irregular_Clinical_Time_Series|Clinical MLLM Reasoning]] - 临床数据上的 LLM 推理

## 外部资源
- [arXiv](https://arxiv.org/abs/2608.20887)
- [PDF](https://arxiv.org/pdf/2608.20887)

> [!tip] 关键启示
> 把复杂的分类问题重构为"检索 + 验证"，并用结构化领域知识约束 LLM 推理，是抑制幻觉、提升合规性的有效范式。

> [!warning] 注意事项
> - 完整标签空间下绝对 F1（0.51）仍偏低，需注意离实用门槛的距离
> - 依赖外部 ICD 编码指南的质量与覆盖度
