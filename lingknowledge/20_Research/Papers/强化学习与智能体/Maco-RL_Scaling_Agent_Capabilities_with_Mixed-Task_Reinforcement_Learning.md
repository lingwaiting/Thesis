---
date: "2026-11-28"
paper_id: "arXiv:2611.34703"
title: "Maco-RL: Scaling Agent Capabilities with Mixed-Task Reinforcement Learning"
authors: "Jiacheng Zhang, Qixing Zhou, Haiyang Wang, Qipeng Gu, Shuzhe Wu, Feiyang Pan, Lue Fan, Dandan Tu, Zhaoxiang Zhang"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 强化学习
  - 多任务学习
  - 智能体
quality_score: "8.0/10"
related_papers: []
created: "2026-11-28"
updated: "2026-11-28"
status: analyzed
---

# Maco-RL: Scaling Agent Capabilities with Mixed-Task Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2611.34703
- **作者**：Jiacheng Zhang, Qixing Zhou, Haiyang Wang, Qipeng Gu, Shuzhe Wu, Feiyang Pan, Lue Fan, Dandan Tu, Zhaoxiang Zhang
- **机构**：--
- **发布时间**：2026-11-25
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2611.34703) | [PDF](https://arxiv.org/pdf/2611.34703)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Mixed-task reinforcement learning (MixRL) directly trains a unified policy across domains to continually expand agent capabilities without training separate specialists or requiring an extra distillation stage. Despite its appeal, scaling MixRL poses significant challenges in achieving stable and efficient mixed-task learning, effectively allocating rollout compute, and supporting continual domain expansion. We propose Maco-RL, which employs a quota-based scheduler for asynchronous rollouts across multiple task domains, stabilizing training by balancing gradient contributions and mitigating off-policy effects. Orthogonally, Maco-RL introduces Tri-Buffer Sampling (TBS) to allocate compute among samples with varying levels of mastery, promoting sustained frontier exploration while preserving acquired capabilities.

### 中文翻译
混合任务强化学习（MixRL）直接跨领域训练一个统一策略，持续扩展智能体能力，无需训练独立专家也无需额外蒸馏阶段。尽管前景诱人，扩展 MixRL 仍面临三大挑战：实现稳定高效的混合任务学习、有效分配 rollout 算力、支持持续的领域扩展。为此我们提出 Maco-RL：采用**基于配额的调度器**在多个任务领域间做异步 rollout，通过平衡梯度贡献、缓解离策略效应来稳定训练；在正交维度上引入**三重缓冲采样（TBS）**，在不同掌握程度的样本间分配算力，在保持既有能力的同时推动持续的前沿探索。

### 核心要点提炼
- **研究背景**：MixRL 用一个统一策略跨领域扩展智能体能力，但扩展难（稳定、算力、持续扩展）。
- **研究动机**：现有方法难以在混合任务下稳定训练、合理分配 rollout 算力。
- **核心方法**：配额调度器（平衡梯度）+ 三重缓冲采样 TBS（分层分配算力）。
- **主要结果**：五个基准上把 Qwen3.5-27B 总分从 43.6 提到 54.2，持续扩展后到 60.1。
- **研究意义**：证明 MixRL 是实用且算力高效的后训练路线，可稳定扩展通用智能体能力。

## 研究背景与动机

### 领域现状
混合任务 RL 用一个统一策略同时跨多个领域训练，天然支持持续扩展智能体能力。但多领域混合带来的梯度冲突、rollout 算力分配不均、以及新增领域时的灾难性遗忘，都制约其规模扩展。

### 现有方法的局限性
- 缺乏对多领域异步 rollout 的稳定调度，梯度贡献不平衡导致训练震荡。
- rollout 算力分配粗放，无法区分"已掌握"与"待探索"的样本。
- 持续扩展新领域时，旧能力容易被覆盖，前沿探索动力不足。

### 研究动机
要让 MixRL 真正成为可扩展的后训练范式，必须同时解决"稳定训练""算力分配""持续扩展"三个正交问题。

## 研究问题

核心研究问题：**如何让混合任务强化学习在跨领域扩展时保持稳定训练、高效分配算力并支持持续的领域扩展？**

## 方法概述

### 核心思想
沿两条正交轴发力：一是用配额调度器在任务领域间做异步 rollout、平衡梯度贡献；二是用三重缓冲采样按"掌握程度"分层分配算力，在保旧与拓新之间取得平衡。

### 方法框架

![[expanserl-framework_page1.png|800]]

> 图1：Maco-RL 框架总览。配额调度器跨领域异步 rollout 平衡梯度，Tri-Buffer Sampling 分层分配算力支持持续扩展。

#### 关键流程
1. **配额调度器**：按配额在多任务领域间调度异步 rollout，平衡各领域的梯度贡献。
2. **离政策缓解**：通过调度策略减轻混合任务下的离政策效应。
3. **Tri-Buffer Sampling（TBS）**：把样本按掌握程度分到不同缓冲，分层分配算力。
4. **持续扩展**：在保持既有能力的同时，推动对新领域的前沿探索。

### 关键创新
- **配额式异步调度**：从调度层面稳定混合任务训练，平衡梯度。
- **三重缓冲采样**：按掌握程度分层分配算力，兼顾"保旧"与"拓新"。
- **持续扩展能力**：无需蒸馏即可持续吸收新领域，逼近甚至超越前沿模型。

## 实验结果

### 主要结果
- 五个基准上，Maco-RL 把 **Qwen3.5-27B 总分从 43.6 提升到 54.2**，持续扩展后进一步到 **60.1**。
- 达到由 Qwen3.8-27B 保持的前沿水平，并进一步将前沿**外推 +5.7**。
- 相比单任务 RL，Maco-RL 以**更少的策略更新算力**取得更好结果。

![[expanserl-performance-efficiency_page1.png|800]]

> 图2：性能与效率对比，展示 Maco-RL 在持续扩展下总分提升及相对单任务 RL 的算力优势。

## 深度分析

### 研究价值
- **理论贡献**：系统拆解 MixRL 扩展的三大挑战，给出调度+采样两条正交的解法。
- **实际价值**：为通用智能体后训练提供一条算力高效、可持续扩展的路线。

### 局限性
- 主要验证在 Qwen 系列模型上，对其他底座模型的通用性待检验。
- 配额与缓冲的调度策略依赖任务领域划分，跨领域粒度选择需经验调优。
- 持续扩展的长期稳定性（更多轮扩展）仍需观察。

### 适用场景
- 需要同时训练多领域能力的通用智能体后训练。
- 追求算力高效、可持续扩展的 RL 训练管线。

## 我的综合评价

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 调度+采样组合思路务实，非全新范式 |
| 技术质量 | 8/10 | 两轴解耦清晰，工程实现完整 |
| 实验充分性 | 8/10 | 五基准+持续扩展+效率对比，扎实 |
| 写作质量 | 8/10 | 结构清晰，数据翔实 |
| 实用性 | 8/10 | 对通用智能体后训练有直接价值 |

> [!tip] 关键启示
> 扩展混合任务 RL 的关键不在"更大模型"，而在"更聪明的算力分配"——用调度平衡梯度、用分层采样平衡保旧与拓新。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐。做通用智能体 RL 后训练 / 多任务扩展的研究者值得一读。
