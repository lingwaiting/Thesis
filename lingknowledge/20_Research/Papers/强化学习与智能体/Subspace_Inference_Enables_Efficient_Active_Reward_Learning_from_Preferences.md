---
date: "2026-09-05"
paper_id: "arXiv:2609.04066"
title: "Subspace Inference Enables Efficient Active Reward Learning from Preferences"
authors: "Yutai Zhou, Erdem Bıyık"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - RLHF
  - 主动学习
  - 奖励模型
  - 贝叶斯推断
  - 卡尔曼滤波
quality_score: "8.2/10"
created: "2026-09-05"
updated: "2026-09-05"
status: analyzed
---

# Subspace Inference Enables Efficient Active Reward Learning from Preferences

## 核心信息

- **论文ID**：arXiv:2609.04066
- **作者**：Yutai Zhou, Erdem Bıyık
- **机构**：University of Southern California（Thomas Lord Department of Computer Science）
- **发布时间**：2026-09-03
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI / cs.RO）
- **链接**：[arXiv](http://arxiv.org/abs/2609.04066v1) | [PDF](https://arxiv.org/pdf/2609.04066v1)
- **引用**：--

## 摘要翻译

### 英文摘要

Reinforcement learning from human feedback (RLHF) has emerged as a powerful yet sample-inefficient approach for learning reward models from human preferences, making active learning a critical component in synthesizing informative preference queries. However, effective uncertainty quantification required for active learning remains a key challenge for large neural network reward models. In this paper, we introduce PreferenceEKF, a sample-efficient approach that tracks reward model uncertainty by framing active preference learning as a sequential Bayesian filtering problem. Instead of relying on computationally prohibitive posterior inference over the full neural network parameter space, our method performs sequential inference via an extended Kalman filter within a low-dimensional parameter subspace, continuously updating the reward model posterior as new preference queries arrive. Our approach enables scalable sampling of neural network parameters to efficiently compute acquisition functions for active reward learning. Experiments on the D4RL and V-D4RL benchmarks demonstrate that our approach achieves better sample efficiency, runtime, scalability, and calibration compared to other Bayesian deep learning approaches, and the learned reward models lead to competitive offline reinforcement learning policy performance. This highlights the potential of scalable Bayesian methods for preference-based reward modeling in RLHF. Our code is available at https://github.com/yutaizhou/bnn_pref.

### 中文翻译

从人类反馈中强化学习（RLHF）已成为一种强大但样本效率低下的奖励模型学习方法，这使得主动学习成为合成有信息量偏好查询的关键组件。然而，主动学习所需的有效不确定性量化，对于大型神经网络奖励模型而言仍是一大挑战。本文提出 PreferenceEKF，一种样本高效的方法，通过将主动偏好学习建模为序列贝叶斯滤波问题来追踪奖励模型的不确定性。与在完整神经网络参数空间上进行计算代价高昂的后验推断不同，该方法在低维参数子空间内通过扩展卡尔曼滤波器进行序列推断，随着新偏好查询的到来持续更新奖励模型后验。该方法支持可扩展地采样神经网络参数，从而高效计算主动奖励学习的采集函数。在 D4RL 与 V-D4RL 基准上的实验表明，相比其他贝叶斯深度学习方法，本方法在样本效率、运行时间、可扩展性与校准性上均更优，且学到的奖励模型带来有竞争力的离线强化学习策略性能。这凸显了可扩展贝叶斯方法在 RLHF 偏好奖励建模中的潜力。

### 核心要点提炼

- **研究背景**：RLHF 学奖励模型样本效率低，主动学习能提升效率，但需要可靠的不确定性量化。
- **研究动机**：对大型网络奖励模型做完整后验推断计算代价极高，现有近似方法难以扩展。
- **核心方法**：把主动偏好学习建模为序列贝叶斯滤波，在低维子空间内用扩展卡尔曼滤波（EKF）追踪奖励模型不确定性。
- **主要结果**：在 D4RL / V-D4RL 上，样本效率、运行时、可扩展性与校准性均优于其他贝叶斯深度学习基线。
- **研究意义**：为 RLHF 偏好奖励建模提供了可扩展的贝叶斯方法路径。

## 研究背景与动机

### 领域现状

RLHF 通过学习人类偏好来训练奖励模型，但人类偏好标注昂贵，样本效率是关键瓶颈。主动学习通过挑选"最有信息量"的偏好查询来降低标注成本，其核心是**采集函数**（acquisition function），而采集函数的计算依赖对奖励模型**不确定性的可靠量化**。

贝叶斯深度学习方法（如深度集成、MC-Dropout、拉普拉斯近似、BNN）能提供不确定性，但对大型奖励模型而言，完整后验推断的计算代价往往难以承受。

### 现有方法的局限性

- **完整贝叶斯推断**：对全参数空间做后验推断计算量爆炸，无法扩展到大型网络。
- **启发式不确定性**：集成 / Dropout 等方法提供的不确定性可靠性存疑，影响采集函数质量。
- **样本效率 vs 可扩展性**：现有方法难以同时兼顾。

### 研究动机

如果把"主动偏好学习"重新建模为**序列状态估计问题**（每来一个偏好查询就更新一次对奖励模型参数的信念），就可以借助经典的贝叶斯滤波工具（卡尔曼滤波）来追踪不确定性，从而在不做完整后验推断的前提下获得可扩展、可校准的不确定性估计。

## 研究问题

### 核心研究问题

1. 如何用序列贝叶斯滤波框架高效追踪大型奖励模型的不确定性？
2. 低维子空间推断能否在保持校准性的同时大幅降低计算代价？
3. 由此得到的主动学习奖励模型能否在离线 RL 中取得有竞争力的策略性能？

## 方法概述

### 核心思想

**PreferenceEKF** 把主动偏好学习建模为序列贝叶斯滤波：将奖励模型参数视为待估计的隐状态，每个新偏好查询作为一次观测，用**扩展卡尔曼滤波（EKF）**在**低维参数子空间**内递归更新后验。这样既避免了全参数空间后验推断的爆炸式开销，又能高效采样参数以计算采集函数。

![[preferenceekf_main_results.png|600]]

> 图：PreferenceEKF 主结果——在 D4RL 多任务偏好学习上，以更少样本取得更高对数后验密度（样本效率优势）。

### 方法框架

#### 整体架构

```
 偏好查询流 ──► 低维子空间内的 EKF 后验更新 ──► 采样参数 ──► 采集函数 ──► 选择下一查询
                     │                            │
                     └── 持续追踪奖励模型不确定性 ──┘
```

#### 各模块详细说明

**模块1：低维参数子空间**

- **功能**：将奖励模型参数限制在一个低维子空间内，把推断从全参数空间降维。
- **动机**：神经网络参数在子空间内通常已能捕捉主要不确定性（如激活 / 梯度主方向）。

**模块2：扩展卡尔曼滤波（EKF）序列推断**

- **功能**：以递归方式更新奖励模型后验，每次新偏好查询到来即做一次滤波更新。
- **优势**：避免每步重新做全后验推断，计算代价与状态维度（而非参数量）相关。

**模块3：可扩展参数采样与采集函数**

- **功能**：从子空间后验中高效采样网络参数，计算主动学习的采集函数（如信息增益）。
- **结果**：得到有信息量的下一批偏好查询。

## 实验结果

### 实验目标

验证 PreferenceEKF 在样本效率、运行时、可扩展性与校准性上是否优于其他贝叶斯深度学习方法，并检验其学到的奖励模型在离线 RL 中的表现。

### 数据集与基线

- **基准**：D4RL、V-D4RL（视觉增强版）。
- **基线**：多种贝叶斯深度学习方法（深度集成、MC-Dropout、BNN 等）。

### 主要结果

| 维度 | 结果 |
|------|------|
| 样本效率 | 优于其他贝叶斯深度学习方法 |
| 运行时间 | 更快 |
| 可扩展性 | 可扩展到大型奖励模型 |
| 校准性 | 不确定性校准更好 |
| 离线 RL | 学到的奖励模型带来有竞争力的策略性能 |

![[preferenceekf_offline_rl.png|600]]

> 图：PreferenceEKF 学到的奖励模型在离线 RL 中的策略性能（与信息增益采集函数、多随机种子对比）。

## 深度分析

### 研究价值评估

#### 理论贡献

- **新视角**：将主动偏好学习重新表述为序列贝叶斯滤波，打通了"滤波理论"与"RLHF 奖励建模"两个领域。
- **子空间推断**：证明低维子空间足以承载主要不确定性，为大型网络的可扩展贝叶斯推断提供了一条新路径。

#### 实际应用价值

- **标注成本下降**：更高效地挑选偏好查询，直接降低 RLHF 的标注成本。
- **可扩展落地**：方法可扩展到大型奖励模型，契合现实 RLHF 需求。

### 方法优势详解

- **计算可扩展**：EKF 在子空间内运行，代价与子空间维度而非网络参数量相关。
- **校准性好**：相比启发式不确定性方法，提供更可靠的采集函数。
- **代码开源**：https://github.com/yutaizhou/bnn_pref

### 局限性分析

- **子空间选择**：低维子空间的构造方式（主方向选择）会影响推断质量，存在设计敏感点。
- **EKF 线性化近似**：EKF 对非线性奖励模型是一阶线性化近似，极端非高斯场景下精度受限。
- **视觉场景扩展**：V-D4RL 已覆盖，但更复杂的视觉语言偏好（如 MLLM 奖励）仍有待验证。

## 我的综合评价

### 价值评分

#### 总体评分

**8.2/10** — 视角新颖、工程实用，为 RLHF 奖励建模的贝叶斯化提供了可扩展的解法。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 序列贝叶斯滤波 + 子空间推断的组合视角新颖 |
| 技术质量 | 8/10 | 方法严谨，EKF 建模清晰 |
| 实验充分性 | 8/10 | D4RL / V-D4RL 双基准，对比充分 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 8/10 | 直接降低 RLHF 标注成本，代码开源 |

## 相关论文

- 相关技术路线：RLHF、偏好奖励建模、主动学习、贝叶斯深度学习（BNN / 拉普拉斯近似）

> [!tip] 关键启示
> 把"主动偏好学习"看成**序列状态估计**，用滤波工具追踪不确定性，是在大型奖励模型上做可扩展贝叶斯推断的一条优雅路径。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐给关注 RLHF 效率优化与贝叶斯奖励建模的研究者。
