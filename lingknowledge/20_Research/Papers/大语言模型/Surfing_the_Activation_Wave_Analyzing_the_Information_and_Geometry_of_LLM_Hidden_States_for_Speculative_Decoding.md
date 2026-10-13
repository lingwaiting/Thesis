---
date: "2026-10-13"
paper_id: "arXiv:2610.12939"
title: "Surfing the Activation Wave: Analyzing the Information and Geometry of LLM Hidden States for Speculative Decoding"
authors: "Raghavv Goel, Sudhanshu Agrawal, Mukul Gagrani, Risheek Garrepalli, Mingu Lee"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Speculative-Decoding
  - Hidden-States
  - Entropy
  - CKA
quality_score: "7.8/10"
created: "2026-10-13"
updated: "2026-10-13"
status: analyzed
---

# Surfing the Activation Wave: Analyzing the Information and Geometry of LLM Hidden States for Speculative Decoding

## 核心信息
- **论文ID**：arXiv:2610.12939
- **作者**：Raghavv Goel, Sudhanshu Agrawal, Mukul Gagrani, Risheek Garrepalli, Mingu Lee
- **机构**：Qualcomm AI Research + Stanford University
- **发布时间**：2026-10-09
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2610.12939) | [PDF](https://arxiv.org/pdf/2610.12939)
- **引用**：--

## 摘要翻译

### 英文摘要
Decoder hidden states from different layers of large language models (LLMs) often encode complementary information. However, existing speculative decoding methods typically rely on a small set of target-model layers chosen using fixed, position-based heuristics. Selecting an optimal subset of layers is inherently a combinatorial problem, making exhaustive evaluation infeasible even for moderately deep models. In this work, we present a principled framework for multi-layer hidden-state selection for speculative decoding based on two complementary criteria: informativeness, measured through matrix-entropy analysis, and diversity, quantified using centered kernel alignment (CKA). We investigate layer-wise Shannon entropy, aggregate entropy under hidden-state concatenation, and pairwise CKA dynamics across depth, revealing distinct geometric and informational compartments within decoder representations. Motivated by these findings, we propose entropy and CKA-based layer selection algorithms that favor hidden-state sets that are both highly informative and minimally redundant. As part of this framework, we introduce token-entropy filtering, which removes hidden states associated with low predictive-entropy tokens, thereby improving the quality of layer-selection statistics. In EAGLE3-style speculative decoding, our selected sets outperform fixed baseline choices, including the default [2, N//2, N−3] rule, while providing a clearer explanation of why some defaults can remain competitive.

### 中文翻译
大语言模型（LLM）解码器不同层的隐藏状态往往编码互补的信息。然而，现有的**投机解码（speculative decoding）**方法通常依赖一小套**基于固定位置启发式**选择的目标模型层。选择最优层子集本质上是一个组合问题，即使对中等深度的模型，穷举评估也不可行。本研究提出一个**原则性框架**，基于两个互补准则为投机解码做多层隐藏状态选择：**信息量（informativeness）**——用矩阵熵分析度量；**多样性（diversity）**——用中心核对齐（CKA）量化。我们研究逐层 Shannon 熵、隐藏状态拼接下的聚合熵，以及跨深度的成对 CKA 动态，揭示解码器表示中不同的**几何与信息"分区"（compartments）**。基于这些发现，我们提出**基于熵与 CKA 的层选择算法**，偏好"信息量高且冗余度低"的隐藏状态集合。作为框架的一部分，我们引入 **token-entropy filtering**，剔除与低预测熵 token 关联的隐藏状态，从而提升层选择统计的质量。在 EAGLE3 风格的投机解码中，我们选择的集合超越固定基线（包括默认的 [2, N//2, N−3] 规则），同时更清晰地解释了为何某些默认选择仍具竞争力。

### 核心要点提炼
- **研究背景**：投机解码依赖目标模型若干层的隐藏状态，层选择通常靠固定位置启发式。
- **研究动机**：层子集选择是组合问题，需要原则性准则替代启发式。
- **核心方法**：信息量（矩阵熵）+ 多样性（CKA）双准则 + token-entropy filtering。
- **主要结果**：EAGLE3 风格投机解码中超越默认 [2, N//2, N−3] 等固定基线。
- **研究意义**：为投机解码提供可解释、可迁移的层选择配方，并揭示 LLM 隐藏状态的几何/信息结构。

## 研究背景与动机

### 领域现状
投机解码通过一个轻量草稿模型预测多个 token、再由目标模型并行验证来加速 LLM 推理。EAGLE 类方法进一步用目标模型自身的隐藏状态训练草稿头（draft head），显著提升接受率。但"用哪几层的隐藏状态"这一关键选择，通常靠固定位置启发式（如均匀取层）。

### 现有方法的局限性
- **固定位置启发式**：如默认 [2, N//2, N−3]，缺乏理论依据，对不同模型/任务未必最优。
- **组合爆炸**：从 N 层中选 k 层是组合问题，穷举不可行。
- **缺乏可解释性**：不清楚"为何某些层组合更好"，难以迁移。

### 研究动机
用信息论与几何的视角重新审视隐藏状态选择：既要有"信息量"（熵），又要"多样性"（CKA 低冗余），从而给出原则性、可解释的层选择算法。

## 研究问题

### 核心研究问题
如何基于信息量（矩阵熵）与多样性（CKA）两个互补准则，为投机解码原则性地选择目标模型隐藏状态层子集，从而超越固定位置启发式？

## 方法概述

### 核心思想
把隐藏状态层选择视为"最大化信息量、最小化冗余"的优化：用矩阵熵度量单层信息量，用 CKA 度量层间多样性，二者结合选出"信息量高且互补"的层集合，并辅以 token-entropy filtering 提升统计质量。

### 方法框架

#### 整体架构
框架由三部分构成：①信息量分析（逐层 Shannon 熵 + 拼接聚合熵）；②多样性分析（成对 CKA 动态）；③层选择算法（熵 + CKA）与 token-entropy filtering。

![[2610.12939_fig1.png|600]]

> 图1：基于信息量与多样性的多层隐藏状态选择框架概览。

#### 关键组件说明

**组件1：信息量度量（矩阵熵）**
- 逐层 Shannon 熵刻画单层隐藏状态的信息含量。
- 拼接聚合熵刻画多层组合后的整体信息量，揭示"分区"结构。

**组件2：多样性度量（CKA）**
- 中心核对齐（CKA）量化层间表示相似度。
- 跨深度成对 CKA 动态揭示哪些层高度冗余、哪些层互补。

**组件3：token-entropy filtering**
- 剔除与低预测熵 token 关联的隐藏状态。
- 低预测熵 token 的信息少，会污染层选择统计，剔除后选择质量提升。

## 实验结果

### 实验目标
验证熵 + CKA 层选择算法在 EAGLE3 风格投机解码中是否超越固定位置启发式。

### 数据集与基线
- **方法**：EAGLE3 风格投机解码。
- **基线**：固定位置选择（含默认 [2, N//2, N−3] 规则）等。

### 主要结果
- 所选层集合在 EAGLE3 风格投机解码中**超越固定基线选择**（包括默认 [2, N//2, N−3]）。
- 揭示了为何某些默认选择仍具竞争力（默认层恰好落在信息/几何"分区"的边界上）。
- token-entropy filtering 提升了层选择统计质量。

### 结果分析
该框架的核心价值在于"可解释性 + 有效性"：既给出了超越默认启发的层选择，又解释了默认规则为何有效——因为它无意中命中了表示空间中的信息/几何分区。这为投机解码的层选择提供了从"玄学调参"到"原则性设计"的转变路径。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：用矩阵熵 + CKA 双准则，把投机解码的层选择从启发式提升为原则性框架。
  - 创新点：引入信息量与多样性两个互补视角，并给出可解释的分区结构分析。
- **贡献2**：提出 token-entropy filtering，提升层选择统计质量。
  - 创新点：识别并剔除了低信息 token 的污染，是工程与理论结合的小而美设计。

#### 实际应用价值
- **应用场景1**：LLM 推理加速（投机解码），在边缘/移动设备（Qualcomm 场景）上尤为重要。
- **应用场景2**：为其他需要"选层/选特征"的模型压缩与加速方法提供通用分析范式。

### 局限性
- 主要在 EAGLE3 风格投机解码上验证，其他投机解码变体（Medusa、Lookahead 等）的通用性待验证。
- 熵与 CKA 的计算本身有开销，大规模模型上需权衡收益。
- "分区"结构的结论基于特定模型，跨架构的普适性待进一步验证。

### 未来工作
- 扩展到更多投机解码变体与模型架构。
- 研究更低成本的熵/CKA 近似，降低分析开销。
- 探索把该分析范式用于剪枝、蒸馏等更广泛的应用。

### 与相关工作对比
- **vs 固定位置层选择（EAGLE 默认等）**：依赖经验启发；本工作给出原则性选择并解释默认为何有效。
- **vs 其他隐藏状态分析工作**：多聚焦单一视角（熵或相似度）；本工作将信息量与多样性结合。
- **研究路线归属**：属于"LLM 推理加速 × 表示几何分析"交叉，为投机解码的层选择提供理论支撑。
