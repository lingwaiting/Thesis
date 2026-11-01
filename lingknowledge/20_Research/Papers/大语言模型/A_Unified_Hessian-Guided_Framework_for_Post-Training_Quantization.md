---
date: "2026-11-01"
paper_id: "arXiv:2610.40345"
title: "A Unified Hessian-Guided Framework for Post-Training Quantization"
authors: "Mads Toftrup, Marco Federici, Boris van Breugel, Paul Whatmough, Markus Nagel"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 模型量化
  - PTQ
  - 训练后量化
  - 模型压缩
quality_score: "7.8/10"
created: "2026-11-01"
updated: "2026-11-01"
status: analyzed
---

# A Unified Hessian-Guided Framework for Post-Training Quantization

## 核心信息
- **论文ID**：arXiv:2610.40345
- **作者**：Mads Toftrup, Marco Federici, Boris van Breugel, Paul Whatmough, Markus Nagel
- **机构**：Qualcomm AI Research
- **发布时间**：2026-10-29
- **类别**：cs.LG, cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2610.40345) | [PDF](https://arxiv.org/pdf/2610.40345)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Low-bit quantization enables efficient deployment of large language models by reducing memory footprint and computational cost. Achieving high accuracy at low precision requires selecting quantization ranges, linear transformations, and weight rounding decisions. Existing approaches typically optimize these components independently using local reconstruction objectives, which fail to account for the varying impact of quantization errors on downstream predictions. Direct optimization of task-level metrics would provide a more faithful objective, but is challenging due to the discontinuous nature of quantization operations and the high computational cost of evaluating their downstream effects. In this work, we derive a Hessian-guided approximation of the output change induced by joint weight and activation quantization. Based on a tractable Fisher approximation, we formulate a unified post-training quantization framework that enables the joint optimization of quantization ranges, linear transformations, and weight rounding under a common output-sensitivity objective.

### 中文翻译
低比特量化通过减少内存占用与计算开销，使大语言模型的高效部署成为可能。在低精度下实现高准确率，需要选择合适的量化范围、线性变换以及权重舍入决策。现有方法通常用局部重建目标独立地优化这些组件，却无法考虑量化误差对下游预测的不同影响。直接优化任务级指标本可提供更忠实的优化目标，但由于量化操作的离散性以及评估其下游效应的高计算成本而颇具挑战。本文推导了一个受 Hessian 引导的、对权重与激活联合量化引起的输出变化的近似。基于一个易处理的 Fisher 近似，作者构建了一个统一的训练后量化框架，使得量化范围、线性变换与权重舍入能够在一个共同的输出敏感性目标下被联合优化。

### 核心要点提炼
1. **问题**：现有 PTQ 方法把"量化范围、线性变换、权重舍入"三者独立优化，且用局部重建目标，无法对齐真实的下游预测影响。
2. **方法**：用 Hessian 引导的 Fisher 近似估计联合权重/激活量化导致的输出变化，把三者纳入统一的输出敏感性目标联合优化。
3. **贡献**：提出一个面向现代大模型的统一 PTQ 框架，在 channel-wise 与 block-wise 量化设置下均优于 SOTA 变换与舍入方法。

## 方法概述

### 方法架构
![[2610.40345_fig1.png|700]]

统一框架的核心逻辑：
- **Hessian 引导的误差建模**：推导联合权重与激活量化所引起的输出变化的二阶（Hessian）近似，量化误差不再是孤立看待，而是以"对输出的影响"为统一度量。
- **Fisher 近似的可处理化**：用 Fisher 信息矩阵近似 Hessian，使目标在计算上可处理，支撑现代大模型规模。
- **三组件联合优化**：量化范围（range）、线性变换（linear transformations）、权重舍入（weight rounding）在同一输出敏感性目标下联合优化，而非分步独立调参。

### 关键创新
1. **输出敏感性统一目标**：把"量化对下游预测的影响"作为单一目标，取代各自为政的局部重建目标。
2. **联合优化三组件**：范围、变换、舍入首次被纳入同一框架协同优化，而非启发式串联。
3. **工程可落地**：Fisher 近似保持计算可处理，直接适用于 channel-wise 与 block-wise 两种主流量化设置。

## 实验结果

- **设置**：channel-wise 与 block-wise 两种量化设置，覆盖现代大语言模型。
- **对比基线**：SOTA 的 transformation（线性变换）与 rounding（权重舍入）方法。
- **关键结果**：所提方法在两种设置下均**一致地优于** SOTA 变换与舍入方法，验证了联合优化与输出敏感性目标的收益。

## 深度分析

### 优势
- 从"输出敏感性"这一更根本的视角统一 PTQ 的三个组件，理论动机清晰。
- Fisher 近似让二阶误差建模在大模型上可处理，工程可行性强。
- 同时覆盖 channel-wise 与 block-wise，普适性好。

### 局限与开放问题
- 依赖 Fisher 近似，对激活量化的 Hessian 项是否充分刻画仍可进一步验证。
- 评测集中在 perplexity（图 2/3 均为 Wikitext perplexity），对下游任务级准确率的影响未见全面报告。
- 与量化感知训练（QAT）的结合空间尚未讨论。

### 与相关工作的关系
- 相比"独立优化范围/变换/舍入"的 SOTA：以联合优化 + 输出敏感性目标统一了三者，直接针对其"各自为政"的痛点。
- 相比直接优化任务级指标（不可微、开销高）：用 Hessian/Fisher 近似给出可处理的代理目标，在保真度与可计算性间取得平衡。
