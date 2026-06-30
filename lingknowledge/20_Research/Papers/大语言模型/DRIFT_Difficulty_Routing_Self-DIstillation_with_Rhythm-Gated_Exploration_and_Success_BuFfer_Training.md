---
date: "2026-06-30"
paper_id: "arXiv:2606.30345"
title: "DRIFT: Difficulty Routing Self-DIstillation with Rhythm-Gated Exploration and Success BuFfer Training"
authors: "Haisen Luo, Yiwei Liu, Haoning Wang, Dan Liu, Junxi Yin, Haotian Wang, Lei Zhang, Xiaoyu Tian, Shuaiting Chen, Yuansheng Song, Baoyan Guo, Xiongfei Yan, Bolan Yang, Chengwei Liu, Ming Cui, Jiong Chen"
domain: "大语言模型"
tags:
  - 论文笔记
  - 自蒸馏
  - 强化学习
  - LLM后训练
  - 推理
  - Self-Distillation
  - GRPO
  - Reinforcement-Learning
  - Reasoning
quality_score: "8.0/10"
created: "2026-06-30"
updated: "2026-06-30"
status: analyzed
---

# DRIFT: 难度路由自蒸馏与节奏门控探索

## 核心信息
- **论文ID**：arXiv:2606.30345
- **作者**：Haisen Luo, Yiwei Liu, Haoning Wang, Dan Liu, Junxi Yin, Haotian Wang, Lei Zhang, Xiaoyu Tian, Shuaiting Chen, Yuansheng Song, Baoyan Guo, Xiongfei Yan, Bolan Yang, Chengwei Liu, Ming Cui, Jiong Chen（16位）
- **机构**：--
- **发布时间**：2026-06-29
- **分类**：cs.LG, cs.AI
- **链接**：[arXiv](http://arxiv.org/abs/2606.30345v1) | [PDF](https://arxiv.org/pdf/2606.30345v1)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Enabling large language models to achieve stable self-improvement without external expert supervision remains a central challenge in complex reasoning tasks. Existing self-distillation and reinforcement learning methods lack explicit mechanisms for tracking problem-level learning progress and adapting optimization strategies accordingly. Consequently, training may over-optimize easy problems, receive weak supervision from hard problems, and fail to sufficiently explore borderline cases. To resolve these issues, we propose DRIFT, an online self-evolution policy optimization framework for large language models. DRIFT regulates the model's self-improvement process through the joint use of Difficulty Routing and Rhythm Gating. The former identifies the model's learning state at the problem level and dynamically allocates self-distillation and reinforcement learning signals, while the latter refines policy updates at the token level, concentrating exploration on critical reasoning positions. By further incorporating a success buffer and a two-stage curriculum learning strategy, DRIFT preserves high-quality historical experience while progressively guiding the model from reliable behavior acquisition toward stable policy evolution. Evaluated across five benchmarks and three model scales, DRIFT surpasses the peak performance of both GRPO and SDPO across all evaluated metrics. On the average score over the five benchmarks, DRIFT achieves 79.5%, outperforming GRPO by 9.5% and SDPO by 7.5%, establishing a new state-of-the-art result. Notably, on ToolUse, DRIFT reaches an accuracy of 79.2%, improving over GRPO by 13.5% and SDPO by 10.7%, setting a new state-of-the-art and substantially outperforming all concurrent methods.

### 中文翻译
使大语言模型在无外部专家监督的情况下实现稳定的自我提升，仍是复杂推理任务中的核心挑战。现有的自蒸馏和强化学习方法缺乏显式机制来追踪问题级别的学习进度并相应调整优化策略。因此，训练可能过度优化简单问题、从困难问题获得弱监督信号、且未能充分探索边界案例。为解决这些问题，我们提出DRIFT——一个面向大语言模型的在线自我进化策略优化框架。DRIFT通过联合使用难度路由（Difficulty Routing）和节奏门控（Rhythm Gating）来调控模型的自我提升过程。前者在问题层面识别模型的学习状态并动态分配自蒸馏和强化学习信号，后者在token层面细化策略更新，将探索集中在关键推理位置。通过进一步融入成功缓冲区（success buffer）和两阶段课程学习策略，DRIFT保留了高质量历史经验，同时逐步引导模型从可靠行为获取走向稳定策略进化。在五个基准和三种模型规模上的评估显示，DRIFT在所有评估指标上超越了GRPO和SDPO的峰值性能。在五个基准的平均得分上，DRIFT达到79.5%，超越GRPO 9.5%、SDPO 7.5%，建立了新的最优结果。值得注意的是，在ToolUse上，DRIFT达到79.2%的准确率，超越GRPO 13.5%、SDPO 10.7%，大幅超越所有同期方法。

### 核心要点提炼
- **研究背景**：LLM自我进化缺乏问题级学习进度追踪和自适应策略调整
- **研究动机**：现有方法过优化简单题、弱监督困难题、忽略边界案例
- **核心方法**：难度路由（问题级信号分配）+ 节奏门控（token级探索控制）+ 成功缓冲区 + 两阶段课程学习
- **主要结果**：五个基准平均79.5%，超越GRPO 9.5%；ToolUse 79.2%，超越GRPO 13.5%
- **研究意义**：建立了无外部监督LLM自我进化的新SOTA

## 研究背景与动机

### 领域现状
LLM在复杂推理任务上的自我提升是后训练时代的核心议题。GRPO（Group Relative Policy Optimization）和SDPO等方法推动了这一方向，但存在根本性局限：它们对所有问题一视同仁，缺乏细粒度的学习进度追踪。

### 现有方法的局限性
1. **过优化简单问题**：简单问题被反复训练，边际收益递减
2. **弱监督困难问题**：困难问题无法提供有效的自蒸馏信号
3. **边界案例忽略**：模型难以从最有学习价值的"临界状态"中获益
4. **缺乏记忆机制**：无法保留和复用高质量历史经验

### 研究动机
DRIFT通过细粒度的学习进度追踪和自适应策略调控，实现更高效、更稳定的LLM自我进化。

## 研究问题

### 核心研究问题
1. 如何在问题级别识别模型的学习状态？
2. 如何根据学习状态动态分配自蒸馏和强化学习信号？
3. 如何在token级别精准控制探索行为？
4. 如何保留和复用历史成功经验？

## 方法概述

### 核心思想
将LLM的自我进化建模为一个受控优化过程：难度路由根据问题级学习状态动态分配优化信号，节奏门控在token级精准控制探索范围。

### 方法框架

#### 整体架构

![[drift_pipeline_new.png|800]]

> 图1：DRIFT框架整体架构——难度路由 + 节奏门控 + 成功缓冲区 + 两阶段课程学习

#### 各模块详细说明

**模块1：难度路由（Difficulty Routing）**
- **功能**：在问题级别识别模型的学习状态并动态分配信号
- **输入**：问题及其模型响应
- **输出**：自蒸馏信号（简单题）或强化学习信号（边界题/困难题）
- **处理流程**：
  1. 评估每个问题的模型当前表现
  2. 将问题分类为：简单（自蒸馏信号）、边界（RL探索）、困难（特殊处理）
  3. 动态调整信号分配的阈值
- **关键创新**：首次将问题难度与优化策略显式关联

**模块2：节奏门控（Rhythm Gating）**
- **功能**：在token级别精准控制探索行为
- **输入**：推理链中的token序列
- **输出**：加权后的策略更新信号
- **处理流程**：
  1. 识别推理链中的关键推理位置
  2. 对关键位置施加更高的探索权重
  3. 对非关键位置降低探索强度
- **关键创新**：token级细粒度探索控制

**模块3：成功缓冲区（Success Buffer）**
- **功能**：保留高质量历史经验
- **机制**：维护一个成功案例的缓冲区
- **复用**：在后续训练中回放成功经验

**模块4：两阶段课程学习**
- **阶段1**：可靠行为获取（Reliable Behavior Acquisition）
- **阶段2**：稳定策略进化（Stable Policy Evolution）
- **过渡**：根据模型表现自动切换阶段

## 实验结果

### 主要结果

| 方法 | 五基准平均 | ToolUse | 相对GRPO提升 |
|------|-----------|---------|-------------|
| GRPO | 70.0% | 65.7% | baseline |
| SDPO | 72.0% | 68.5% | +2.0% |
| **DRIFT** | **79.5%** | **79.2%** | **+9.5% (overall) / +13.5% (ToolUse)** |

![[drift_performance.png|800]]

> 图2：DRIFT与基线的性能对比

### 消融分析

![[drift_tooluse_ablation.png|800]]

> 图3：DRIFT各组件消融实验

关键消融发现：
- 移除难度路由：性能显著下降
- 移除节奏门控：收敛不稳定
- 移除成功缓冲区：遗忘历史经验
- 各组件协同效应显著

### 训练动态

![[drift_training_dynamics.png|800]]

> 图4：DRIFT训练过程中的学习动态

## 深度分析

### 研究价值评估

#### 理论贡献
- **学习状态感知的自我进化**：首次将问题级学习进度追踪融入LLM自我进化框架
- **难度自适应信号分配**：突破了现有方法对所有问题均匀处理的局限
- **token级探索控制**：在细粒度层面控制RL探索行为

#### 实际应用价值
- **无外部监督场景**：适用于无法获取高质量标注的领域
- **多规模验证**：三种模型规模均有效
- **工具使用能力**：ToolUse上的13.5%提升展示强实用潜力

### 方法优势详解
1. **难度路由**：动态识别并适配问题难度，使训练资源高效分配
2. **节奏门控**：token级精准探索，避免盲目探索
3. **成功缓冲区**：解决灾难性遗忘，保持历史最佳表现
4. **课程学习**：渐进式引导，从稳定到进化

### 局限性分析
1. **计算开销**：在线框架比离线蒸馏需要更多计算
2. **基准分布**：五个基准的代表性需要更多领域验证
3. **超参数敏感性**：难度阈值和门控参数可能需要领域适配

## 技术路线定位

本文属于**LLM自我进化/自蒸馏**研究路线，是GRPO/DAPO/SDPO系列方法的重大推进：
```
GRPO → SDPO → DRIFT (本文) → [未来: 多任务自我进化]
```

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** — 方法设计精巧，多组件协同创新，实验结果优异，实用潜力高

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 难度路由+节奏门控的组合创新，多组件协同 |
| 技术质量 | 8/10 | 方法设计严谨，消融实验全面 |
| 实验充分性 | 8/10 | 五基准+三规模+消融+训练动态，实验全面 |
| 写作质量 | 7/10 | 方法描述清晰，实验呈现完整 |
| 实用性 | 8/10 | 无外部监督进化，工具使用场景价值高 |

> [!tip] 关键启示
> LLM自我进化的关键在于"因材施教"——根据问题难度和学习状态自适应调整优化策略，而非对所有问题一视同仁。DRIFT的难度路由+节奏门控框架为这一方向提供了精妙的实现。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！今日最佳论文。方法设计精巧、实验全面扎实、性能提升显著，是LLM后训练方向的重要进展。
