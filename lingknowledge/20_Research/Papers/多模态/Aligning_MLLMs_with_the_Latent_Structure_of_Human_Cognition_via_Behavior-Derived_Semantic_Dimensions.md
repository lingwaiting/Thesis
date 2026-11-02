---
date: "2026-11-02"
paper_id: "arXiv:2610.41951"
title: "Aligning MLLMs with the Latent Structure of Human Cognition via Behavior-Derived Semantic Dimensions"
authors: "Ning E, Changde Du, Yizhuo Lu, Huiguang He"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - 对齐
  - 认知科学
  - 表征对齐
  - 可解释性
quality_score: "7.8/10"
created: "2026-11-02"
updated: "2026-11-02"
status: analyzed
---

# Aligning MLLMs with the Latent Structure of Human Cognition via Behavior-Derived Semantic Dimensions

## 核心信息
- **论文ID**：arXiv:2610.41951
- **作者**：Ning E, Changde Du, Yizhuo Lu, Huiguang He
- **机构**：--
- **发布时间**：2026-10-30
- **类别**：cs.CL, cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2610.41951) | [PDF](https://arxiv.org/pdf/2610.41951)
- **来源**：arXiv
- **代码**：https://github.com/ening-open/cogalign

## 摘要翻译

### 英文摘要
Multimodal Large Language Models (MLLMs) have achieved impressive performance across a wide range of reasoning tasks, yet it remains unclear whether their internal representations and decision criteria align with the latent structure of human cognition. In this work, we propose a novel framework to align MLLMs with the multidimensional mental representations underlying human similarity judgements. We build on a previously established behavior-derived semantic embedding space, in which objects are represented along 66 interpretable dimensions derived from 4.70 million human Odd-One-Out (O1O) judgements. For each triplet, we infer the most salient decision dimension and convert it into a dimension-grounded linguistic rationale. This enables MLLMs to learn both the final choice and a justification consistent with the inferred behavioral criterion. Experimental results demonstrate that our approach improves model consistency with human similarity judgements while retaining competitive performance on standard multimodal benchmarks. Furthermore, using searchlight Representational Similarity Analysis (RSA) on an independent fMRI Natural Scenes Dataset (NSD), we observe increased representational alignment between model and human.

### 中文翻译
多模态大语言模型（MLLM）在广泛的推理任务上已取得令人瞩目的性能，但其内部表征与决策标准是否与人类认知的潜在结构对齐仍不清楚。本文提出一个新框架，将 MLLM 与人类相似性判断背后的多维心理表征对齐。作者基于此前建立的行为派生的语义嵌入空间，其中对象沿 66 个可解释维度被表示，这些维度来自 470 万条人类"奇异性三选一"（Odd-One-Out, O1O）判断。对每个三元组，作者推断最显著的决策维度，并将其转化为"以维度为根据的语言性理由"。这使得 MLLM 能同时学习最终选择，以及与所推断行为标准一致的理由。实验表明，该方法提升了模型与人类相似性判断的一致性，同时在标准多模态基准上保持竞争力。此外，在独立 fMRI 自然场景数据集（NSD）上使用 searchlight 表征相似性分析（RSA），作者观察到模型与人类之间表征对齐度的提升。

### 核心要点提炼
1. **问题**：MLLM 性能虽强，但其表征与决策标准是否对齐人类认知的潜在结构尚不明确。
2. **方法**：用 470 万条人类 O1O 判断构建 66 维可解释语义空间，为每个三元组推断显著决策维度并生成"以维度为根据的理由"，让 MLLM 同时学习选择与理由。
3. **结果**：提升与人类相似性判断的一致性，且通过 fMRI NSD 上的 searchlight RSA 验证了模型-人类表征对齐度的提升。

## 方法概述

### 方法架构
![[2610.41951_fig1.png|700]]

对齐框架的核心逻辑：
- **行为派生语义空间**：以 470 万条人类 O1O 判断为数据，构建 66 个可解释维度的语义嵌入空间，作为人类认知结构的代理。
- **维度推断与理由生成**：对每个三元组推断最显著的决策维度，并把该维度转化为语言性理由（dimension-grounded rationale）。
- **双重监督**：让 MLLM 学习"最终选择 + 与行为标准一致的理由"，而非仅学标签。
- **神经验证**：用 searchlight RSA 在 fMRI NSD 上量化模型与人类表征的几何相似度。

### 关键创新
1. **数据驱动的认知对齐**：不依赖人工定义的概念/属性，而是从大规模人类行为数据中挖掘可解释维度。
2. **维度根据的理由**：把"为什么这么选"的认知标准显式化为理由，让对齐可解释、可监督。
3. **跨模态神经验证**：用 fMRI + RSA 给出模型-人类表征对齐的客观证据，超越纯行为/基准指标。

## 实验结果

- **一致性**：与人类相似性判断的一致性提升，同时保持标准多模态基准上的竞争力。
- **神经对齐**：在独立 fMRI NSD 上，searchlight RSA 显示模型与人类表征对齐度提升。
- **结论**：数据驱动的认知结构可以被注入 MLLM 对齐，带来行为与神经层面的双重收益。

## 深度分析

### 优势
- 把"对齐"从行为指标拓展到"表征/认知结构"层面，视角新颖且立意较高。
- 用大规模人类行为数据（470 万 O1O）作为认知结构的证据基础，比人工标注更客观、可扩展。
- 同时给出行为（相似性判断一致性）与神经（fMRI RSA）两层证据，论证链完整。

### 局限与开放问题
- O1O 语义空间覆盖的维度有限（66 维），能否外推到更复杂的认知能力（推理、因果、抽象）尚待验证。
- 提升幅度与标准基准上的 trade-off 细节未在摘要中量化，需看完整结果表。
- 神经证据依赖单一 fMRI 数据集（NSD），跨数据集、跨被试的稳健性待进一步检验。

### 与相关工作的关系
- 相比"基于人类偏好/指令的对齐"（RLHF 等）：聚焦认知结构的潜在维度，而非显式偏好。
- 相比认知科学的表征相似性研究：把行为派生的语义空间落地为 MLLM 的训练信号，是"认知启发 AI"路线的代表。
- 与可解释性（interpretability）工作互补：用维度理由让模型决策可解释。
