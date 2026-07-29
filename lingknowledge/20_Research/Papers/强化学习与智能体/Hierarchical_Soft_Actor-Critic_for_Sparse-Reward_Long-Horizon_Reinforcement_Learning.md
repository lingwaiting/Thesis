---
date: "2026-07-28"
paper_id: "2607.23726"
title: "Hierarchical Soft Actor-Critic for Sparse-Reward Long-Horizon Reinforcement Learning"
authors: "Zahra Abdalla Elashaal, Afef Hfaiedh, Nahla Khraief, Issmail Ellabib, Giansalvo Cirrincione"
domain: "强化学习与智能体"
tags: [论文笔记, Hierarchical-RL, SAC, Sparse-Reward, Long-Horizon, Search-and-Rescue]
quality_score: "9.17/10"
related_papers: []
created: "2026-07-28"
updated: "2026-07-28"
status: analyzed
---

# Hierarchical Soft Actor-Critic for Sparse-Reward Long-Horizon RL

## 核心信息
- **论文ID**：2607.23726
- **作者**：Zahra Abdalla Elashaal, Afef Hfaiedh, Nahla Khraief, Issmail Ellabib, Giansalvo Cirrincione
- **机构**：--
- **发布时间**：2026-07-26
- **链接**：[arXiv](https://arxiv.org/abs/2607.23726) | [PDF](https://arxiv.org/pdf/2607.23726)

## 摘要翻译

稀疏奖励长程任务中的探索对强化学习构成重大挑战。我们提出双层分层强化学习框架：第一层处理高层战略规划，低层使用连续控制 Soft Actor-Critic 算法，并利用熵正则化策略优化。框架在 Search-and-Rescue-2 (SAR-2) 数据集上训练和评估。HRL-SAC 有效解决了以延迟奖励和连续控制为特征的稀疏奖励长程搜索问题，在成功率、覆盖效率和收敛性方面均超越 flat SAC 基线。这些发现表明分层熵正则化策略是解决长程稀疏奖励 RL 的有前景方案。

## 研究问题
如何在稀疏奖励、长程、连续控制任务中实现有效探索和学习？

![[1_HRL-SAC_training_analysis_02-2026_1000_page1.png|800]]

> 图1：HRL-SAC 训练分析，展示分层架构的训练动态。

## 方法概述

### 核心思想
将长程任务分解：高层学习"去哪里"（子目标选择），低层学习"怎么去"（连续控制执行）。

### 方法框架
- **高层策略**：基于状态选择子目标，处理战略级长程规划
- **低层 SAC**：连续控制执行，使用熵正则化鼓励探索
- **熵正则化**：两层均使用，在探索和利用之间自动平衡

![[8_HRL_vs_SAC_EvalSuccess_02-2026_1000_page1.png|800]]

> 图2：HRL vs SAC 评估成功率对比。

### 关键创新
- 分层架构针对稀疏奖励长程场景的结构化分解
- 熵正则化在分层设置中的统一应用

## 实验结果
- **环境**：SAR-2 搜索救援
- **对比基线**：Flat SAC
- **评估维度**：成功率、覆盖效率、收敛速度——全部超越基线

> [!tip] 关键启示
> 分层分解是解决稀疏奖励长程 RL 的结构性方案——让高层负责"战略"，低层负责"执行"。

> [!success] 推荐指数
> ⭐⭐⭐ 在经典分层 RL 框架上的扎实应用，方法可推广到其他长程连续控制任务。
