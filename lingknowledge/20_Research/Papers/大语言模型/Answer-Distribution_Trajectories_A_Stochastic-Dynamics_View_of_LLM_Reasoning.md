---
date: "2026-09-10"
paper_id: "arXiv:2609.09030"
title: "Answer-Distribution Trajectories: A Stochastic-Dynamics View of LLM Reasoning"
authors: "Mar Gonzàlez I Català, Haitz Sáez de Ocáriz Borde, Davide Murari, Carola-Bibiane Schönlieb, Pietro Liò, George Montañez"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Reasoning
  - Chain-of-Thought
  - Stochastic-Dynamics
  - Interpretability
quality_score: "8.5/10"
related_papers: []
created: "2026-09-10"
updated: "2026-09-10"
status: analyzed
---

# Answer-Distribution Trajectories: A Stochastic-Dynamics View of LLM Reasoning

## 核心信息
- **论文ID**：arXiv:2609.09030
- **作者**：Mar Gonzàlez I Català, Haitz Sáez de Ocáriz Borde, Davide Murari, Carola-Bibiane Schönlieb, Pietro Liò, George Montañez
- **机构**：--
- **发布时间**：2026-09-08
- **会议/期刊**：--
- **链接**：[arXiv](https://arxiv.org/abs/2609.09030) | [PDF](https://arxiv.org/pdf/2609.09030)
- **分类**：cs.AI, cs.CL, cs.IT, cs.LG

## 摘要翻译

### 英文摘要
Chain-of-thought reasoning provides a structured computation between a model's input and final answer. Yet it is often evaluated through endpoint accuracy, which ignores the path taken to reach that answer. An emerging line of work addresses this limitation using entropy profiles, which track how uncertainty evolves over the reasoning process but do not reveal which competing hypotheses account for that uncertainty. We introduce answer-distribution trajectories, a stochastic-dynamics-inspired representation that tracks the model's full predictive distribution over answers as reasoning unfolds. As a strictly finer representation than endpoint and entropy summaries, answer-distribution trajectories enable us to characterize a trace through a dynamical reasoning profile spanning exploration, revision, motion, and commitment, and to distinguish different dynamical mechanisms of reasoning success and failure. Across sixteen open-weight language models and four reasoning benchmarks, we show that traces with the same endpoint and similar entropy profiles can exhibit substantially different reasoning dynamics. We further find substantial variation in these dynamics both within and across models and tasks, with different objectives favoring different dynamical profiles. Additionally, we show that training and inference choices systematically reshape these profiles. Our results suggest that answer-distribution trajectories provide a rich framework for analysing and evaluating the dynamics of LLM reasoning.

### 中文翻译
思维链推理在输入与最终答案之间提供了结构化的计算过程，但现有评估往往只关注「端点正确率」，忽略了抵达答案所经历的路径。已有工作尝试用熵曲线刻画推理过程中不确定性的演化，但熵无法揭示究竟是哪些候选假设构成了这种不确定性。本文提出「答案分布轨迹」（answer-distribution trajectories），一种受随机动力学启发的表征，跟踪模型在推理展开过程中对答案的完整预测分布。作为比端点与熵更精细的表征，答案分布轨迹使我们能够刻画一条贯穿「探索—修正—漂移—收敛」的动态推理轮廓，并区分推理成功与失败的不同动力学机制。在 16 个开源权重模型与 4 个推理基准上，作者发现：端点相同、熵曲线相近的轨迹，其推理动力学可能截然不同；这些动力学在同一模型、不同任务之间都存在显著差异，不同目标函数偏好不同的动态轮廓；训练与推理阶段的配置会系统性地重塑这些轮廓。

### 核心要点提炼
- **研究背景**：CoT 评估长期只看最终答案是否正确，忽略了推理过程本身的动态结构。
- **研究动机**：熵曲线虽能反映不确定性变化，但无法说明「不确定性来自哪些候选答案」，信息粒度不足。
- **核心方法**：提出「答案分布轨迹」，跟踪推理每一步对答案集合的完整预测分布（比端点、熵都更精细）。
- **主要结果**：相同端点和熵曲线可能对应完全不同的推理动力学；训练/推理配置会系统性改变这些动力学。
- **研究意义**：为 LLM 推理的动力学分析与评估提供了一个新的、信息更丰富的框架。

## 研究背景与动机

### 领域现状
思维链（Chain-of-Thought, CoT）已成为提升 LLM 推理能力的主流手段，让模型在给出最终答案前先展开中间推理步骤。然而，几乎所有的评测（如 GSM8K、MATH 等）都以「最终答案正确率」为核心指标，把推理过程当作一个黑盒，只关注输出的端点。

### 现有方法的局限性
最近兴起的「熵曲线」（entropy profile）方法开始关注推理过程中的不确定性演化，但仍有两处关键不足：
1. 熵是一个标量，只表示「有多不确定」，不表示「在哪些选项之间不确定」；
2. 无法区分「在两个错误答案间犹豫」与「在正确答案和一个干扰项间犹豫」这两种截然不同的情形。

### 研究动机
作者借鉴随机动力学（stochastic dynamics）的思想，主张应该直接跟踪模型在每一步对**整个答案分布**的预测，从而揭示推理的真实动力学轨迹——即模型如何探索、修正、漂移并最终收敛。

## 研究问题

### 核心研究问题
1. 如何构建一个比「端点」和「熵」都更精细的推理表征？
2. 用这种表征能否区分推理成功与失败的不同动力学机制？
3. 推理动力学在模型之间、任务之间呈现怎样的差异？训练与推理配置又如何改变它们？

## 方法概述

### 核心思想
把推理过程看作一个动态系统，在每一步观察模型对答案集合的完整预测分布（而非单一的 argmax 答案或标量熵）。由此得到一条「答案分布轨迹」，可用随机动力学的语言刻画推理的探索、修正、漂移与收敛。

### 方法框架

#### 整体架构
![[answer_distribution_trajectories_page1.png|800]]

> 图1：答案分布轨迹的核心思想示意——推理过程中模型对候选答案的预测分布随时间演化，形成一条可刻画「探索—修正—漂移—收敛」的动态轨迹。

#### 各模块详细说明

**模块1：答案分布轨迹的构建**
- **功能**：在 CoT 推理的每个中间步骤，提取模型对答案集合的完整预测分布。
- **输入**：模型的中间推理状态与候选答案集合。
- **输出**：一条按时间展开的答案分布序列。
- **关键技术**：受随机动力学启发，将「预测分布随步数的演化」形式化为轨迹。

**模块2：动态推理轮廓（dynamical reasoning profile）**
- **功能**：从轨迹中刻画「探索（exploration）、修正（revision）、漂移（motion）、收敛（commitment）」四个阶段的动态特征。
- **输出**：一个可解释的推理轮廓，用于区分不同推理行为。
- **关键点**：相同的端点和相近的熵曲线，可能对应完全不同的轮廓。

**模块3：跨模型/跨任务/跨配置分析**
- **功能**：在 16 个开源模型、4 个推理基准上系统比较推理动力学。
- **输出**：揭示推理动力学在模型间、任务间的高度异质性，以及训练/推理配置对轮廓的系统性重塑。

### 关键创新
1. **答案分布轨迹**——首次将推理过程表征为对答案完整预测分布的轨迹，信息粒度远超端点和熵。
2. **动态推理轮廓**——用「探索/修正/漂移/收敛」刻画推理的成功与失败机制。
3. **系统性实证**——在 16 模型 × 4 基准的规模上证明了推理动力学的异质性与可塑性。

## 实验结果

### 实验设置
- **模型**：16 个开源权重语言模型
- **基准**：4 个推理基准
- **对比维度**：端点、熵曲线、答案分布轨迹

### 主要结果
1. **端点与熵无法区分**：端点相同、熵曲线相近的轨迹，推理动力学可以截然不同。
2. **动力学高度异质**：推理动力学在同一模型内部、跨模型、跨任务之间均存在显著差异。
3. **目标函数塑造动力学**：不同训练目标偏好不同的动态轮廓。
4. **配置可重塑动力学**：训练（如 RLHF/微调）与推理（如采样温度、解码策略）选择会系统性改变推理轮廓。

## 深度分析

### 研究价值
- **理论贡献**：为 LLM 推理提供了「随机动力学」视角下的新表征，把「如何推理」从黑盒变成可观测的动态轨迹。
- **实际应用**：可用于更细粒度的推理质量评估、失败模式诊断，以及训练目标的设计。
- **领域影响**：连接了可解释性、推理评估与训练方法三条研究线。

### 优势
- 表征信息粒度显著高于现有端点/熵指标
- 实证规模大（16 模型 × 4 基准），结论扎实
- 提出了「成功/失败机制」的可区分性框架

### 局限性
- 依赖对「候选答案集合」的先验定义，开放式生成场景下适用性受限
- 分析以开源模型为主，闭源前沿模型的推广性待验证
- 目前偏描述性，尚未给出「如何直接利用轨迹改进模型」的可操作方法

### 适用场景
- 推理模型（reasoning model）的失败模式诊断与评估
- 训练目标与推理策略的消融对比分析

## 我的综合评价

### 价值评分
- **总体评分**：**8.5/10** — 视角新颖、实证扎实的推理动力学分析工作

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 从端点/熵推进到完整答案分布轨迹，概念清晰且新颖 |
| 技术质量 | 8/10 | 方法严谨，随机动力学类比得当 |
| 实验充分性 | 9/10 | 16 模型 × 4 基准，跨维度系统分析 |
| 写作质量 | 8/10 | 结构清晰，动机充分 |
| 实用性 | 8/10 | 为评估与诊断提供有力工具，但直接改进模型的路径尚不明确 |

### 突出亮点
- 证明「端点正确 + 熵相似 ≠ 推理过程相同」，直指现有评估的盲区
- 给出可解释的「探索/修正/漂移/收敛」动力学轮廓

## 相关论文
- [[A_-Thought-V2_Efficient_Latent_Reasoning_via_Geometric_Dynamics_of_LLM|A*-Thought-V2]] - 同样关注推理内部动态的潜在推理工作

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.09030)
- [PDF](https://arxiv.org/pdf/2609.09030)

> [!tip] 关键启示
> 评估推理模型不能只看最终答案——同样的正确答案背后，可能是完全不同的推理动力学；「怎么想出来的」与「想没想对」同样重要。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐关注推理过程动态、可解释性或推理评估的研究者阅读。
