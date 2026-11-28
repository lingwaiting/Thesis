---
date: "2026-11-28"
paper_id: "arXiv:2611.34634"
title: "LatentRL-QAS: Transfer via Latent Reinforcement Learning for Quantum Architecture Search"
authors: "Maria Ilektra Plakitsi, Sebastian Feld, Akash Kundu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 强化学习
  - 量子架构搜索
  - 迁移学习
quality_score: "7.8/10"
related_papers: []
created: "2026-11-28"
updated: "2026-11-28"
status: analyzed
---

# LatentRL-QAS: Transfer via Latent Reinforcement Learning for Quantum Architecture Search

## 核心信息
- **论文ID**：arXiv:2611.34634
- **作者**：Maria Ilektra Plakitsi, Sebastian Feld, Akash Kundu
- **机构**：--
- **发布时间**：2026-11-25
- **会议/期刊**：arXiv 预印本（quant-ph / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.34634) | [PDF](https://arxiv.org/pdf/2611.34634)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Reinforcement learning (RL) emerges as a prominent approach to quantum architecture search (QAS), the automated design of quantum circuits. However, transferring what an agent has learned from one number of qubits to another is still weakly explored: the state, action space and neural network of a state-of-the-art RL-QAS agent all grow with the number of qubits, making transfer learning across qubit counts prohibitive. We introduce LatentRL-QAS, a latent space transfer and refresh framework: a circuit variational autoencoder encodes a circuit as one token per gate and maps circuits on any number of qubits into the same latent space; an RL agent navigates this space instead of adding gates, so its state, actions and network have the same size at every qubit count. The latent space is periodically refreshed with the agent's new discoveries and, for a larger system, grown from the solutions of the smaller one.

### 中文翻译
强化学习（RL）已成为量子架构搜索（QAS，即自动设计量子电路）的主流方法。然而，把智能体从一个量子比特数上学到的知识迁移到另一个量子比特数，目前探索甚少：现有 SOTA RL-QAS 智能体的状态、动作空间与神经网络都随量子比特数增长，导致跨比特数的迁移学习难以为继。为此我们提出 LatentRL-QAS——一个"潜空间迁移 + 刷新"框架：电路变分自编码器把电路按"每个门一个 token"编码，将任意比特数的电路映射到同一潜空间；RL 智能体在该潜空间中导航而非逐门添加，因此其状态、动作与网络在任意比特数下都保持相同规模。潜空间会周期性用智能体的新发现刷新，对更大系统则从小系统的解中"生长"出来。

### 核心要点提炼
- **研究背景**：RL-QAS 智能体的规模随量子比特数增长，无法跨比特数迁移。
- **研究动机**：量子电路设计成本高，急需"小系统学到的表示可复用到大系统"的能力。
- **核心方法**：电路 VAE 把任意比特电路映射到统一潜空间，RL 智能体在此空间导航。
- **主要结果**：无需示例即发现 5、6 比特 AME 态，89–99% 评估成功率，远超基线。
- **研究意义**：把"迁移电路表示"而非"迁移网络权重/经验"作为 QAS 迁移的基础。

## 研究背景与动机

### 领域现状
量子架构搜索用 RL 自动设计量子电路，但主流方法中智能体的状态编码、动作空间与策略网络都直接绑定量子比特数，因此不同比特数之间无法共享表示，每种规模都要从头训练。

### 现有方法的局限性
- 状态/动作/网络随比特数线性或更快增长，跨规模迁移代价过高。
- 迁移通常只迁"网络权重"或"历史经验"，本质仍是规模相关的，难以泛化。
- 缺少一种与比特数解耦的电路表示，导致大系统无法复用小系统的发现。

### 研究动机
若能找到一个**与量子比特数无关**的电路表示空间，让智能体在该空间中工作，就能从根本上解除 RL 智能体对规模的依赖，使"小系统学到的电路结构"可迁移到大系统。

## 研究问题

核心研究问题：**如何构建一个与量子比特数解耦的电路表示，使 RL 智能体学到的架构搜索能力可以跨比特数迁移？**

## 方法概述

### 核心思想
把"在门序列空间里做离散搜索"改造为"在一个共享的连续潜空间里导航"——电路 VAE 把任意比特数的电路编码成统一潜向量，RL 智能体直接在该潜空间上做决策，从而让状态、动作、网络的维度都与比特数无关。

### 方法框架

![[pipeline_page1.png|800]]

> 图1：LatentRL-QAS 流水线。电路 VAE 将任意比特数的电路映射到统一潜空间，RL 智能体在该空间导航，周期性刷新潜空间，并从小系统解生长到大系统。

#### 关键流程
1. **电路编码**：电路变分自编码器按"每个门一个 token"对电路编码，得到统一的潜空间表示。
2. **潜空间导航**：RL 智能体在潜空间中做决策（而非逐门添加），状态/动作/网络维度恒定。
3. **周期性刷新**：用智能体新发现的电路刷新潜空间，保持表示的多样性。
4. **规模生长**：从较小系统的解初始化较大系统的潜空间，实现表示的迁移。

### 关键创新
- **与比特数解耦的表示**：用电路 VAE 统一表示任意规模的电路，是迁移的关键前提。
- **迁移"表示"而非"权重/经验"**：让跨规模 QAS 首次具备可迁移的基础。
- **刷新 + 生长机制**：潜空间随搜索动态更新，并支持从小到大的生长式迁移。

## 实验结果

### 主要结果
- 在无需被展示目标态的情况下，LatentRL-QAS **发现 5 与 6 比特 AME 态**，并在 89–99% 的评估 episode 中成功制备。
- 相同预算下，基线 RL-QAS 即便用 **10–17 倍大的网络**也找不到这些态。
- 在去极化噪声（单比特门误差放大 10 倍、双比特门误差放大 5 倍，参照 IBM 硬件）下仍保持鲁棒。

![[ladder_final5_page1.png|800]]

> 图2：5 比特 AME 态的发现结果，展示 LatentRL-QAS 相对基线的制备成功率优势。

## 深度分析

### 研究价值
- **理论贡献**：提出"潜空间迁移 + 刷新"的 QAS 框架，把迁移问题从规模依赖中解放。
- **实际价值**：量子纠错资源（AME 态）的自动发现，跨规模复用可显著降低电路设计成本。

### 局限性
- 主要验证在 AME 态这一特定目标，向更广泛量子优化问题的泛化待考察。
- 电路 VAE 的编码质量直接影响潜空间导航效率，编码器本身仍有改进空间。
- 尚未在真实量子硬件上验证端到端闭环。

### 适用场景
- 需要跨量子比特数复用的量子电路自动设计。
- 量子纠错、变分量子算法等对电路结构敏感的领域。

## 我的综合评价

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "迁移表示而非权重"的解耦思路新颖 |
| 技术质量 | 8/10 | VAE+RL 组合清晰，刷新/生长机制完整 |
| 实验充分性 | 7/10 | 目标态验证扎实，但任务较单一 |
| 写作质量 | 8/10 | 结构清晰，动机充分 |
| 实用性 | 7/10 | 面向量子计算的垂直应用，受众较窄 |

> [!tip] 关键启示
> 迁移学习的关键往往不是"迁移什么权重"，而是"找到一个与问题规模无关的表示空间"——这对任何"规模敏感"的搜索问题都有启发。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐。跨规模迁移的表示解耦思路很有价值，做 RL + 组合搜索的研究者值得一读。
