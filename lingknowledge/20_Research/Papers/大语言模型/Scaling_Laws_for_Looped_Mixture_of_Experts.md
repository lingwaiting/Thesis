---
date: "2026-10-02"
paper_id: "arXiv:2609.40316"
title: "Scaling Laws for Looped Mixture of Experts"
authors: "Yanbei Chen, Anirudh Goyal, Raghuraman Krishnamoorthi"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Scaling-Law
  - Mixture-of-Experts
  - Looped-Transformer
quality_score: "9.0/10"
created: "2026-10-02"
updated: "2026-10-02"
status: analyzed
---

# Scaling Laws for Looped Mixture of Experts

## 核心信息
- **论文ID**：arXiv:2609.40316
- **作者**：Yanbei Chen, Anirudh Goyal, Raghuraman Krishnamoorthi
- **机构**：Meta AI
- **发布时间**：2026-09-30
- **会议/期刊**：ICLR 2027 投稿（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](http://arxiv.org/abs/2609.40316) | [PDF](https://arxiv.org/pdf/2609.40316)
- **引用**：--

## 摘要翻译

### 英文摘要
Looped transformers and Mixture-of-Experts (MoE) offer complementary routes to efficient scaling: recurrence increases computational depth at fixed parameters, while MoE sparsity expands total capacity at fixed active compute. Yet existing scaling laws model recurrence or sparsity in isolation. In this work, we introduce **Loop Scaling Laws**, the first scaling law to jointly model recurrence and sparsity alongside model size and data. At its core is a bounded, sparsity-conditional recurrence mapping that characterizes the effective-parameter gain from looping and how sparsity raises this gain. The laws predict the held-out loss of looped models more accurately than prior alternatives, and recover the standard dense and MoE scaling laws as special cases. Beyond prediction, the fitted laws provide a principled foundation for designing looped MoE models under compute and memory constraints. Downstream evaluations further demonstrate the complementary benefits of the two axes: sparsity delivers ~3× active-parameter efficiency, recurrence yields ~2× total-parameter efficiency on reasoning, and joint scaling further advances the performance frontier. As a practical extension, we show these gains hold at trillion-token scale: at matched training compute, a looped MoE with law-derived recurrence matches a ~2× larger non-looped MoE on the reasoning benchmarks, while enabling test-time scaling through recurrence.

### 中文翻译
循环 Transformer（Looped Transformer）与混合专家（MoE）提供了两条互补的高效扩展路径：循环在固定参数量下通过复用深度增加计算深度，而 MoE 稀疏性在固定激活计算量下扩大总容量。然而，现有的缩放定律（scaling law）要么只建模循环、要么只建模稀疏性，从未将二者联合考虑。本文提出 **Loop Scaling Laws**，这是首个同时联合建模循环与稀疏性（连同模型规模与数据量）的缩放定律。其核心是一个有界的、以稀疏性为条件的循环映射（recurrence mapping），它刻画了循环带来的有效参数增益，以及稀疏性如何放大该增益。该定律对循环模型留出损失（held-out loss）的预测优于此前的替代方案，并能将标准稠密模型与 MoE 缩放定律作为特例恢复出来。除了预测能力，拟合得到的定律还为在计算与内存约束下设计循环 MoE 模型提供了原则性基础。下游评测进一步展示了两条轴的互补收益：稀疏性带来约 3 倍的激活参数效率，循环在推理任务上带来约 2 倍的总参数效率，联合扩展进一步推高性能前沿。作为实际扩展，作者证明这些收益在万亿 token 规模上依然成立：在匹配的训练计算量下，一个由定律推导出循环次数的循环 MoE 在推理基准上可与约 2 倍大的非循环 MoE 媲美，同时还能通过循环实现测试时扩展（test-time scaling）。

### 核心要点提炼
- **研究背景**：MoE 稀疏化与循环 Transformer 是两种主流的高效扩展路线，但缺乏统一的理论框架来联合建模二者。
- **研究动机**：现有 scaling law 孤立建模循环或稀疏性，无法指导"循环 + MoE"联合设计。
- **核心方法**：提出 Loop Scaling Laws，用一个有界的、稀疏性条件的循环映射来刻画有效参数增益。
- **主要结果**：稀疏性 ~3× 激活参数效率，循环 ~2× 总参数效率，联合扩展在万亿 token 规模依然有效。
- **研究意义**：为在计算/内存约束下设计循环 MoE 模型提供了首个原则性 scaling law 基础。

## 研究背景与动机

### 领域现状
Scaling law 是指导大模型训练的核心工具，从 Kaplan 等人与 Chinchilla 定律开始，已广泛用于预测模型规模、数据量与损失的关系。近年来两条高效扩展路线分别兴起：

1. **MoE 稀疏化**：通过稀疏激活在固定激活计算量下扩大总参数容量（如 Mixtral、DeepSeek-MoE），已有对应 MoE scaling law。
2. **循环/复用深度**：如 Looped Transformer、Universal Transformer，通过权重共享的循环在不同层之间复用参数，以固定参数量换取更深计算深度。

但这两条路线长期被**分开研究**，缺乏统一理论。

### 现有方法的局限性
- 现有 scaling law 要么只建模循环（把循环折合成等价深度），要么只建模稀疏性（把 MoE 折合成等价参数），无法回答"循环与稀疏性如何相互作用"。
- 缺少一个能刻画"循环带来的有效参数增益如何随稀疏性变化"的形式化映射。
- 因此工程师在设计"循环 MoE"时只能靠经验试错，缺乏在计算/内存约束下的原则性设计工具。

### 研究动机
将循环与稀疏性纳入同一个 scaling law 框架，既能提升对循环模型损失的预测精度，又能为"循环 MoE"这一高效架构的联合设计提供理论指导。

## 研究问题

### 核心研究问题
1. 能否构建一个联合建模**循环、稀疏性、模型规模、数据量**的 scaling law？
2. 循环带来的"有效参数增益"如何被形式化刻画？稀疏性如何放大这一增益？
3. 该定律能否在万亿 token 规模上指导循环 MoE 的实际设计？

## 方法概述

### 核心思想
把"循环次数"和"稀疏性"视为两个独立的扩展轴，用一个**有界的、以稀疏性为条件的循环映射**（bounded, sparsity-conditional recurrence mapping）来刻画：循环等效于增加了多少有效参数，而稀疏性（更大的总参数/激活参数比）会放大这种等效增益。拟合后的定律能更准确地预测循环模型的留出损失，并将稠密与 MoE 定律作为特例包含进来。

### 方法框架

#### 整体架构

![[teaser_compose_page1.png|800]]

> 图1：论文的 teaser 图，展示循环（recurrence）与稀疏性（sparsity）两条轴如何互补地推进性能前沿。

#### 各模块详细说明

**模块1：稀疏性条件的循环映射（sparsity-conditional recurrence mapping）**
- **功能**：刻画循环次数如何转化为有效参数增益。
- **关键设计**：映射是**有界的**（循环增益随循环次数趋于饱和），且**以稀疏性为条件**（MoE 的总参数/激活参数比越高，循环的等效增益越大）。
- **数学形式**：将循环模型映射到一个等效的稠密参数规模 $N_{\text{eff}}$，使得损失预测可直接套用稠密 scaling law。

**模块2：Loop Scaling Laws 拟合**
- **功能**：在模型规模 $N$、数据量 $D$、循环次数 $r$、稀疏度 $s$ 四维上拟合损失。
- **关键结果**：对循环模型 held-out loss 的预测误差低于此前所有替代方案。

**模块3：约束下的模型设计**
- **功能**：在给定计算量（compute）与内存（memory）预算下，用拟合的定律反解最优的循环次数与稀疏度组合。
- **关键结果**：可推导出"计算最优循环"（compute-optimal recurrence）与"内存最优稀疏度"（memory-optimal sparsity）。

### 方法架构图

![[compute_optimal_recurrence_page1.png|800]]

> 图2：计算最优循环次数的推导结果，展示在不同预算下循环如何成为比单纯扩大参数更高效的扩展方式。

## 实验结果

### 实验目标
验证 Loop Scaling Laws 的预测精度、恢复已有定律的能力，以及其在万亿 token 规模与下游推理任务上的实际收益。

### 主要结果

#### 主实验结果
- **稀疏性收益**：稀疏化带来约 **3×** 的激活参数效率（在固定激活计算量下取得更好的推理性能）。
- **循环收益**：循环在推理任务上带来约 **2×** 的总参数效率。
- **联合扩展**：循环 + 稀疏化联合扩展进一步突破单轴扩展的性能前沿。
- **万亿 token 规模**：在匹配训练计算量下，循环 MoE 在推理基准上媲美约 2 倍大的非循环 MoE。

#### 结果分析
- 稀疏性与循环的收益是**互补**的：稀疏性节约激活计算，循环节约总参数，二者叠加能同时降低计算与内存开销。
- 循环映射的"有界性"解释了为何循环次数不能无限增大——增益会饱和，这为实际部署提供了明确的上界。

### 实验结果图

![[effective_parameter_multiplier_page1.png|800]]

> 图3：有效参数乘数随稀疏性与循环次数的变化，展示二者如何共同放大有效参数量。

![[downstream_recurrence_overall_reasoning_page1.png|800]]

> 图4：下游推理任务上循环带来的整体收益。

## 深度分析

### 研究价值评估

#### 理论贡献
- **首个联合 scaling law**：首次将循环与稀疏性纳入同一 scaling law 框架，是高效扩展理论的重要补全。
  - 创新点：提出稀疏性条件的循环映射这一可形式化的核心概念。
  - 学术价值：为高效架构（循环 MoE）提供了此前缺失的理论基础。
  - 影响范围：大模型预训练、MoE 架构设计、推理优化。

#### 实际应用价值
- **应用场景1：大规模预训练成本控制**
  - 适用性：在固定计算/内存预算下指导架构选型。
  - 优势：将经验试错替换为定律反解。
  - 潜在影响：降低大模型训练成本。

- **应用场景2：测试时扩展（test-time scaling）**
  - 适用性：通过循环实现推理时深度扩展。
  - 优势：不增加参数存储即可提升推理能力。

#### 领域影响
- **短期影响**：为循环 MoE 架构提供理论背书，可能推动更多实践探索。
- **中期影响**：成为 MoE scaling law 与循环模型研究的统一参照。
- **长期影响**：推动"计算/内存约束下的联合架构搜索"成为标准范式。

### 方法优势详解

#### 优势1：统一性与特例恢复
- **描述**：稠密 scaling law 与 MoE scaling law 都是本定律的特例，理论自洽。
- **技术基础**：稀疏性条件的循环映射在稀疏度/循环次数取边界值时退化回已有定律。

#### 优势2：预测精度
- **描述**：对循环模型 held-out loss 的预测优于此前所有方案。

#### 优势3：可指导实际设计
- **描述**：可直接反解"计算最优循环"与"内存最优稀疏度"，具备工程落地价值。

### 局限性分析

#### 局限1：依赖拟合数据分布
- **描述**：定律参数需要在特定模型/数据分布上拟合。
- **影响**：外推到未覆盖的架构或数据域时精度可能下降。

#### 局限2：主要验证于推理类任务
- **描述**：下游收益证据集中在 reasoning benchmarks。
- **影响**：其他任务类型上的泛化性有待验证。

### 适用性与场景分析

#### 适用场景
- 大规模预训练中需要在算力受限下最大化性能。
- 希望以较少参数实现深度计算与测试时扩展。

#### 不适用场景
- 显存极度充裕、更看重绝对最优而非效率的场景，直接扩大稠密参数可能更简单可靠。

## 与相关论文对比

### 对比论文选择依据
选择 scaling law 与高效架构两条路线上的代表性工作作为对比基准。

### [[Chinchilla|Chinchilla: Training Compute-Optimal Large Language Models]]
- **关系**：本文定律在循环/稀疏边界值处退化回经典稠密 scaling law，是 Chinchilla 定律的推广。
- **本文改进**：新增循环与稀疏性两个扩展轴。

### [[Mixture-of-Experts|Mixture-of-Experts Scaling Law]]
- **关系**：现有 MoE scaling law 只建模稀疏性，本文将其作为特例统一。
- **本文改进**：联合建模循环与稀疏性的交互。

### 对比总结
Loop Scaling Laws 的核心贡献在于**统一**：它不推翻既有稠密/MoE 定律，而是把它们作为边界特例纳入一个更一般的框架，并补齐了"循环 × 稀疏"这一空白维度。

## 技术路线定位

### 所属技术路线
本文属于**大模型高效扩展（efficient scaling）**技术路线，核心特点：
- 以 scaling law 为理论工具指导架构与训练设计。
- 强调在计算/内存约束下的效率优化。

### 本文在技术路线中的位置
- **承上**：继承 Chinchilla 等稠密 scaling law 与 MoE scaling law。
- **启下**：为"循环 MoE"这类联合高效架构提供了理论起点。
- **关键节点**：首次打通循环与稀疏两个此前独立的研究方向。

## 未来工作建议

### 基于分析的未来方向
1. **方向1：扩展到多模态与长上下文**
   - 动机：验证定律在非推理、长序列任务上的泛化性。
2. **方向2：循环机制的自动化搜索**
   - 动机：用定律指导自动搜索最优循环结构与循环次数。
3. **方向3：与推理时计算（inference-time compute）结合**
   - 动机：循环天然支持测试时扩展，值得进一步系统化。

## 我的综合评价

### 价值评分

#### 总体评分
**9.0/10** - 理论补全意义明确、预测精度优于基线、具备工程落地价值，是高效扩展方向的重要工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 首个联合建模循环与稀疏性的 scaling law |
| 技术质量 | 9/10 | 理论自洽、能恢复已有定律、预测精度高 |
| 实验充分性 | 8/10 | 覆盖万亿 token 与下游任务，但泛化场景待拓展 |
| 写作质量 | 9/10 | 结构清晰、论证完整 |
| 实用性 | 9/10 | 可直接指导循环 MoE 架构设计 |

## 相关论文

### 直接相关
- [[Chinchilla]] - 稠密 scaling law 的经典之作，本文为其推广
- [[Mixture-of-Experts]] - MoE 架构与 scaling 研究

### 后续工作
- 循环 Transformer / Universal Transformer 类工作，是本文循环轴的方法基础

> [!tip] 关键启示
> 循环与稀疏性是两条互补的扩展轴：稀疏性省激活计算，循环省总参数；用一个"稀疏性条件的循环映射"即可把二者统一进同一个 scaling law，为循环 MoE 的高效设计提供理论依据。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐。这是高效扩展理论的重要补全，对关注 MoE 与循环架构的读者极具参考价值。
