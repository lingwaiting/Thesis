---
date: "2026-11-13"
paper_id: "arXiv:2611.15404"
title: "Is the Importance Ratio Necessary for Stable Reinforcement Learning in LLMs?"
authors: "Shuibai Zhang, Junhyuck Kim, Gyeongman Kim, Jaewoong Cho"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 强化学习
  - GRPO
  - Policy-Optimization
quality_score: "8.5/10"
related_papers: []
created: "2026-11-13"
updated: "2026-11-13"
status: analyzed
---

# Is the Importance Ratio Necessary for Stable Reinforcement Learning in LLMs?

## 核心信息
- **论文ID**：arXiv:2611.15404
- **作者**：Shuibai Zhang, Junhyuck Kim, Gyeongman Kim, Jaewoong Cho
- **机构**：--
- **发布时间**：2026-11-11
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2611.15404) | [PDF](https://arxiv.org/pdf/2611.15404)
- **引用**：--

## 摘要翻译

### 英文摘要
Reinforcement learning (RL) has become central to post-training large language models (LLMs). However, popular RL methods like GRPO incur non-negligible overhead by computing both old-policy and current-policy likelihoods to form importance sampling ratios. In this work, we propose Likelihood-Gated Policy Optimization (LGPO), which enforces a soft trust region constraint via likelihood-based gating, eliminating the need to compute old-policy likelihoods. Empirically, we show that removing the importance sampling correction term does not harm training stability, whereas removing the trust region mechanism can lead to collapse.

### 中文翻译
强化学习已成为大语言模型后训练的核心手段，但 GRPO 等流行方法需要通过同时计算旧策略与当前策略的似然来构造重要性采样比率，带来不可忽略的计算开销。本文提出 Likelihood-Gated Policy Optimization（LGPO），通过基于似然的门控机制施加软信任域约束，从而无需再计算旧策略似然。实验表明，去掉重要性采样修正项并不会损害训练稳定性，而去掉信任域机制则会导致训练崩溃。

### 核心要点提炼
- **研究背景**：GRPO 等 LLM 强化学习方法依赖重要性采样比率，计算开销大。
- **研究动机**：重要性比率是否真的是稳定训练所必需？
- **核心方法**：LGPO，用似然门控的软信任域约束替代重要性比率。
- **主要结果**：LGPO 在 GRPO 稳定的场景下达到可比的稳定性与峰值性能，同时平均减少约 18% 训练时间；在完全 on-policy 场景下（GRPO 失效）依然稳定。

## 研究问题

核心研究问题：**LLM 强化学习中的重要性采样比率（importance ratio）是否是训练稳定的必要条件？能否在移除该比率的同时保持甚至提升训练稳定性与效率？**

## 方法概述

### 核心方法

1. **Likelihood-Gated Policy Optimization（LGPO）**
   - 用"似然门控"施加**软信任域约束**，取代基于重要性比率的 clip 机制。
   - 不再计算旧策略似然，省去一次前向/缓存开销。

2. **对比分析 GRPO 的稳定性来源**
   - 实验验证：移除重要性采样修正项 → 训练依然稳定。
   - 移除信任域机制 → 训练崩溃。
   - 结论：**稳定性来自信任域约束，而非重要性比率本身**。

3. **on-policy 场景分析**
   - 完全 on-policy 训练时，重要性比率恒为 1，基于比率的信任域约束永远不激活。
   - 解释了 GRPO 在 on-policy 场景失效的原因。

### 方法架构

![[main_training_curves_horizontal_page1.png|600]]

> 图：LGPO 与 GRPO 在标准训练设置下的训练曲线对比。LGPO 在移除重要性比率后仍保持可比稳定性与峰值性能。

### 关键创新

1. **提出 LGPO** - 首次系统论证"重要性比率可移除"，用似然门控替代，降低约 18% 训练开销。
2. **定位稳定性真正来源** - 通过消融区分"重要性修正"与"信任域约束"各自的作用。
3. **揭示 on-policy 失效机制** - 指出比率恒为 1 时基于比率的约束永不激活，解释 GRPO 的失效边界。

## 实验结果

### 实验设置
- **对比基线**：GRPO 及其标准实现（重算旧策略似然）。
- **核心指标**：训练稳定性、峰值性能、训练时间。

### 主要结果
- **标准训练设置**：LGPO 达到与 GRPO 可比的训练稳定性与峰值性能，平均减少约 18% 训练时间（相对重算旧策略似然的标准实现）。
- **完全 on-policy 训练**：GRPO 失效（崩溃），LGPO 依然稳定，可在多种训练机制下实现更高效、更鲁棒的 LLM RL 后训练。
- **消融结论**：去掉重要性采样修正项不影响稳定性；去掉信任域机制导致崩溃。

## 深度分析

### 研究价值评估
- **理论贡献**：厘清 LLM RL 中"重要性比率"与"信任域约束"的功能分离，为高效策略优化提供新视角。
- **实际应用**：LLM 后训练（如 RLHF/GRPO 路线）可直接采用 LGPO 降低计算成本。

### 方法优势
1. **效率**：无需计算旧策略似然，减少约 18% 训练时间。
2. **鲁棒性**：在 GRPO 失效的 on-policy 场景依然稳定。
3. **简洁**：机制更简单，工程实现门槛低。

### 局限性
- 论文主要在小规模/特定设置下验证，大规模（>70B）模型上的稳定性与收益需进一步确认。
- 18% 的时间节省来自"重算旧策略似然"的标准实现对比，若实现中已缓存旧策略似然，收益可能收窄。

## 与相关论文对比
- **GRPO**（DeepSeekMath）：本工作直接对标，指出其重要性比率的冗余性与 on-policy 失效问题。
- **PPO / TRPO**：信任域思想的经典来源，LGPO 将信任域约束以更轻量的似然门控形式重新实现。

## 我的综合评价

### 总体评分
**8.5/10** - 针对 LLM RL 后训练中一个被广泛使用却少有反思的组件（重要性比率）给出了清晰、可操作的简化方案，实验论证扎实，工程价值明确。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 视角新颖，正面回答"比率是否必需" |
| 技术质量 | 8/10 | 消融严谨，因果定位清晰 |
| 实验充分性 | 7/10 | 设置覆盖 on/off-policy，但规模有限 |
| 写作质量 | 9/10 | 结构清晰，论证流畅 |
| 实用性 | 9/10 | 可直接落地到 RL 后训练管线 |

> [!tip] 关键启示
> LLM 强化学习的稳定性来自信任域约束而非重要性采样比率本身；LGPO 以更轻量的似然门控保留了这一关键机制，同时省去了旧策略似然的计算。

## 相关论文
- [[GRPO_DeepSeekMath|GRPO]] - 本文对标的方法
