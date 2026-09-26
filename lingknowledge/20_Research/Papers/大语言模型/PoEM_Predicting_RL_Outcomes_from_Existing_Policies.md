---
date: "2026-09-26"
paper_id: "arXiv:2609.30226"
title: "PoEM: Predicting RL Outcomes from Existing Policies"
authors: "Kimia Hamidieh, Giannis Daras, Antonio Torralba"
domain: "大语言模型"
tags:
  - 论文笔记
  - RLHF
  - Policy-Prediction
  - Post-Training
  - Alignment
  - Reward-Modeling
quality_score: "8.5/10"
created: "2026-09-26"
updated: "2026-09-26"
status: analyzed
---

# PoEM: Predicting RL Outcomes from Existing Policies

## 核心信息
- **论文ID**：arXiv:2609.30226
- **作者**：Kimia Hamidieh, Giannis Daras, Antonio Torralba
- **机构**：--
- **发布时间**：2026-09-24
- **会议/期刊**：arXiv 预印本（cs.LG, cs.AI, cs.CL, cs.CV）
- **链接**：[arXiv](http://arxiv.org/abs/2609.30226v1) | [PDF](https://arxiv.org/pdf/2609.30226v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Foundation models are post-trained with reinforcement learning (RL) to maximize specific rewards, such as human alignment, correctness, or instruction following. This post-training process is computationally intensive, sometimes unstable, and has to be run from scratch every time the reward model changes or when we want to combine multiple rewards. We hence ask: given a new reward function, is it possible to predict the RL outcomes without actually running RL on it? We answer this in the affirmative by introducing PoEM, a framework to predict the outputs of RL on a new reward function using a set of models already post-trained on other rewards. First, we show that if the new reward function can be written as a linear combination of existing ones, then the new policy in log-space can be written as a linear combination of the existing log-policies. Surprisingly, even in cases where the rewards are not linearly connected, we observe that often log-policies from RL training span an approximately low-rank subspace across rewards. To our benefit, the weighting coefficients for this combination can be estimated using only the reward or basis policy outputs on the samples. We turn these observations into an algorithm that takes post-trained models and a new reward function, and approximates the target RL policy without actually running any additional RL training. We experimentally validate our approach across synthetic and real rewards, spanning both text and image modalities.

### 中文翻译
基础模型通过强化学习（RL）进行后训练，以最大化特定奖励，例如人类对齐、正确性或指令遵循。这一后训练过程计算密集、有时不稳定，且每当奖励模型改变或希望组合多个奖励时都必须从头重新运行。因此我们提出：给定一个新的奖励函数，能否在不实际对其运行 RL 的情况下预测 RL 的结果？我们通过提出 PoEM 给出肯定回答——PoEM 是一个利用一组已在其他奖励上后训练过的模型，来预测 RL 在新奖励函数上的输出的框架。首先，我们证明：如果新奖励函数可以写成现有奖励函数的线性组合，那么新策略在 log 空间中也可以写成现有 log 策略的线性组合。令人惊讶的是，即使奖励之间非线性相连，我们也观察到 RL 训练得到的 log 策略在奖励维度上往往张成一个近似低秩的子空间。对我们有利的是，这个组合的加权系数可以仅用样本上的奖励（或基策略）输出来估计。我们将这些观察转化为一个算法：输入后训练模型和一个新奖励函数，即可在不实际运行任何额外 RL 训练的情况下近似目标 RL 策略。我们在合成和真实奖励上、跨越文本与图像两种模态实验验证了该方法。

### 核心要点提炼
- **研究背景**：基础模型的 RL 后训练（对齐、正确性等）计算密集且不稳定，换奖励就得重跑
- **研究动机**：能否不跑 RL，直接从已有后训练模型"外推/组合"出新奖励下的策略
- **核心方法**：利用"log 策略在奖励维度上近似低秩"的性质，用已有 log 策略的线性组合近似新策略
- **主要结果**：无需额外 RL 训练即可预测新奖励下的策略输出，覆盖文本与图像模态
- **研究意义**：为高效、可组合的 RL 后训练提供了理论洞察与实用工具

## 研究背景与动机

### 领域现状
基础模型（LLM、文生图等）普遍通过 RL 后训练（RLHF、GRPO 等）来对齐人类偏好、提升正确性或指令遵循。每次改变奖励函数或组合多个奖励，都需要从头重跑 RL 后训练，成本高昂。

### 现有方法的局限性
- **计算密集**：RL 后训练需要大量采样、优化与不稳定调参
- **不稳定**：训练过程易发散或策略坍塌
- **不可复用**：换一个奖励函数就得从头训练，无法复用已有后训练模型的"经验"
- **多奖励组合困难**：组合多个奖励目标时需重新设计训练目标并重跑

### 研究动机
能否把"已后训练的模型"当作可复用的基元（basis），对新奖励函数直接预测/组合出对应策略，从而免去重跑 RL？

## 研究问题

### 核心研究问题
给定一组已在其他奖励上后训练的模型和一个新奖励函数，能否在不运行额外 RL 训练的情况下，近似得到该新奖励对应的 RL 策略？

## 方法概述

### 核心思想
利用两个关键观察——(1) 新奖励是现有奖励的线性组合时，log 策略也是 log 策略的线性组合；(2) 即使非线性相连，log 策略在奖励维度上近似低秩——将"预测 RL 结果"转化为"在已有 log 策略的低秩子空间中做加权组合"。

### 方法框架

#### 整体架构

![[Figure1_page1.png|800]]

> 图1：PoEM 核心思路示意——用已有后训练模型的 log 策略线性组合来近似新奖励下的策略

#### 各模块详细说明

**关键观察1：线性奖励 → 线性 log 策略**
- 若新奖励 $r_{\text{new}} = \sum_i \alpha_i r_i$，则新策略在 log 空间满足 $\log \pi_{\text{new}} = \sum_i \alpha_i \log \pi_i$（在适当近似下）
- 意义：策略组合的"代数结构"与奖励组合一致

**关键观察2：近似低秩子空间**
- 即使奖励间非线性相连，RL 训练得到的 log 策略在奖励维度上也常张成近似低秩子空间
- 意义：用少数几个基策略即可近似任意新奖励下的策略

**加权系数估计**
- 组合系数可仅用"奖励函数输出"或"基策略在样本上的输出"来估计，无需额外训练
- 将上述观察封装为一个无需额外 RL 训练的算法

### 方法架构图
![[Figure1_page1.png|800]]

## 实验结果

### 实验目标
验证 PoEM 在合成与真实奖励、文本与图像模态上预测 RL 结果的能力。

### 实验设置
- **奖励类型**：合成奖励 + 真实奖励（对齐、正确性等）
- **模态**：文本、图像

### 主要结果
- 在合成与真实奖励上，PoEM 无需额外 RL 训练即可近似目标策略
- 覆盖文本与图像两种模态，验证了方法的通用性

## 深度分析

### 研究价值评估

#### 理论贡献
- **策略空间的结构性洞察**：揭示"log 策略在奖励维度上近似低秩"这一结构性规律
- **线性组合定理**：给出"奖励线性组合 ⟹ log 策略线性组合"的明确关系
- **可组合后训练的基石**：为"后训练结果可复用、可组合"提供了理论支撑

#### 实际应用价值
- **降低对齐成本**：多奖励目标组合时免去重跑 RL
- **快速实验迭代**：奖励设计者可快速预估新奖励下的策略行为
- **模型复用**：已有后训练模型成为可组合的"策略基元"

### 方法优势详解
- **优势1：省去 RL 训练**——核心价值，把昂贵的后训练变成线性组合
- **优势2：通用性强**——覆盖文本与图像模态、合成与真实奖励
- **优势3：理论清晰**——有明确的线性组合定理支撑

### 局限性分析
- **局限1：近似误差**——低秩近似在奖励差异大时可能引入误差
- **局限2：基策略覆盖**——需要一组覆盖充分的后训练基模型
- **局限3：真实 RL 的复杂动态**——实际训练中的随机性与非线性可能偏离低秩假设

## 技术路线定位

本文属于 **RL 后训练 / 对齐** 技术路线，并带有"可组合性（compositionality）"的理论色彩：
- **承上**：继承 RLHF、GRPO 等后训练范式的积累
- **启下**：为"后训练结果的组合与复用"开辟方向，可能催生新的对齐工具链
- **关键节点**：首次明确刻画"log 策略在奖励维度上的低秩结构"，是从"重复训练"走向"组合复用"的关键一步

## 相关论文
- 直接相关：[[20_Research/Papers/大语言模型/A_Zeroth-Order_Paradigm_for_LLM_Preference_Alignment|Zeroth-Order Preference Alignment]] - 偏好对齐的高效方法
- 背景相关：RLHF / GRPO / 对齐与后训练系列工作

## 外部资源
- arXiv: http://arxiv.org/abs/2609.30226

> [!tip] 关键启示
> 若"log 策略在奖励维度上近似低秩"成立，那么昂贵的 RL 后训练可被"已有策略的线性组合"取代，后训练将走向可组合、可复用。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读：对 RL 后训练给出了一个兼具理论深度与实用价值的结构性洞察，方向新颖，可能影响后续对齐研究。
