---
date: "2026-11-22"
paper_id: "arXiv:2611.26252"
title: "The Stability Gap in Continual Reinforcement Learning"
authors: "Sebastian Pusch, Matthia Sabatelli, Gido M. van de Ven"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 持续强化学习
  - 稳定性缺口
  - 灾难性遗忘
  - Continual-Reinforcement-Learning
  - Stability-Gap
quality_score: "7.97/10"
created: "2026-11-22"
updated: "2026-11-22"
status: analyzed
---

# The Stability Gap in Continual Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2611.26252
- **作者**：Sebastian Pusch, Matthia Sabatelli, Gido M. van de Ven
- **机构**：荷兰格罗宁根大学 人工智能系（Department of Artificial Intelligence, University of Groningen）
- **发布时间**：2026-11-19
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2611.26252) | [PDF](https://arxiv.org/pdf/2611.26252)
- **代码**：https://github.com/sebapusch/stability-gap-drl

## 摘要翻译

### 英文摘要
Continual reinforcement learning (CRL) aims to enable agents to adapt to changing tasks while preserving previously acquired capabilities. Evaluation often relies on final aggregate metrics that can hide temporary performance degradation during adaptation. This work introduces a simple CRL benchmark that varies observation representations while preserving environment dynamics and rewards, supporting proof-of-concept demonstrations, inexpensive experimentation, and easily reproducible sanity checks. Using this benchmark, we demonstrate the stability gap in CRL: a substantial drop in prior-task performance after the introduction of new tasks, followed by partial or complete recovery. This pattern occurs despite the agent having complete access to past tasks.

### 中文翻译
持续强化学习（CRL）旨在让智能体适应不断变化的任务，同时保留此前习得的能力。现有评测通常依赖最终的聚合指标，这可能会掩盖适应过程中出现的暂时性性能下降。本工作引入了一个简洁的 CRL 基准，它仅改变观测表示而保持环境动力学与奖励不变，支持概念验证演示、低成本实验与易于复现的健全性检查。基于该基准，作者在 CRL 中展示了"稳定性缺口"（stability gap）现象：在引入新任务后，旧任务性能出现显著下降，随后部分或完全恢复——即便智能体始终拥有对过往任务的完整访问权限，这一模式依然出现。

### 核心要点提炼
- **研究背景**：持续学习（CL）中已有"稳定性缺口"概念，但此前仅在有监督 CL 中被确认，CRL 是否也存在该现象未被探索。
- **研究动机**：CRL 评测长期只看任务边界或收敛后的指标，暂时性性能崩坏可能被隐藏。
- **核心方法**：构建"变换状态"CRL 基准（用正交变换扰动观测），在增量联合训练范式下高频评测旧任务。
- **主要结果**：在 DQN/SAC-D（CartPole）与 DDPG/SAC（InvertedPendulum）上均观察到稳定性缺口，且在完全可访问旧任务时依然存在。
- **研究意义**：为 CRL 引入了首个系统性诊断稳定性的轻量基准，提示评测应关注训练过程中的暂时性退化而非仅看终点。

## 研究背景与动机

### 领域现状
顺序训练会严重损害神经网络在旧任务上的表现，即"灾难性遗忘"（catastrophic forgetting）。持续学习（CL）旨在适应新任务的同时保留已有知识，评测通常依赖任务边界指标（最终性能、遗忘度、后向迁移），这些指标会忽略适应过程中出现的暂时性失败。

### 现有方法的局限性
De Lange 等人在有监督 CL 中发现了"稳定性缺口"：任务切换后旧任务性能出现骤降，随后部分或完全恢复；即使旧任务数据全部可用（增量联合训练场景）也会发生。然而，CRL 领域是否同样存在稳定性缺口尚未被探索——现有 CRL 评测强调最终性能，或仅在任务边界/收敛后测量迁移与遗忘。

### 研究动机
由于 CRL 的暂时性性能下降可能一直被隐藏，作者希望隔离这一动态过程：通过一个轻量的变换状态基准，配合对旧任务的高频评测，检验 CRL 中是否存在稳定性缺口。

## 研究问题

### 核心研究问题
在持续强化学习（CRL）中，当智能体在增量联合训练范式下（始终拥有对全部已遇任务的完整访问权限）顺序学习新任务时，旧任务性能是否会出现"稳定性缺口"——即任务切换后的暂时性骤降与后续恢复？

## 方法概述

### 核心思想
借鉴 Permuted-MNIST 的思路，构建一个"变换状态"CRL 基准：所有任务共享相同的环境动力学、奖励与动作空间，仅通过不同的正交变换改变观测表示。这样任务之间只有观测表示不同，从而把学习过程的变化完全归因于任务切换本身，便于隔离并观测稳定性缺口。

![[benchmark_page1.png|600]]

> 图1(a)：基准构造——对基础状态 s 施加正交变换 T_i，并拼接 one-hot 任务标识 e_i，得到任务 i 的智能体输入。

### 方法框架

#### 整体架构
- **任务构造**：对每个实验种子，独立采样 N 个正交矩阵 $T_i \in \mathbb{R}^{d \times d}$，将状态 $s \in \mathbb{R}^d$ 变换为
  $$x_i = \begin{bmatrix} T_i s \\ e_i \end{bmatrix} \in \mathbb{R}^{d+N}$$
  其中 $e_i$ 是 one-hot 任务标识。$T_i$ 通过对高斯矩阵做 QR 分解获得，并修正列符号以保证 $T_i^\top T_i = I_d$。正交性保留了角度、距离与信息，从而维持学习底层动力学所需的几何结构；任务标识可省略以构成更难变体。
- **环境实例**：在 CartPole（离散动作）上采用 DQN 与离散 SAC（SAC-D），在 InvertedPendulum（连续动作）上采用 DDPG 与 SAC，两个环境均为四维状态（d=4），各构造 3 个任务（N=3）。

#### 训练范式（增量联合训练）
- 任务顺序引入，智能体持续对所有已遇任务进行训练，始终保留对全部旧任务的完整访问权限。
- 每个算法为每个任务维护独立 replay buffer，每次更新从各 buffer 等比例采样。
- 任务切换时重置 replay buffer 与优化器状态，但保留网络参数；随后从每个已遇任务收集 1000 条 transition 再恢复网络更新。
- 高频评测：每个任务前 30,000 步内每 15 步评测一次所有任务，之后每 250 步评测一次；性能以 15 个 episode 的平均回报度量。

#### 关键超参数
- 优化器 Adam，两层 256 单元全连接网络，batch size 128，折扣因子 $\gamma = 0.99$。
- 学习率逐算法调优（DQN $10^{-4}$、DDPG/SAC $3\times 10^{-4}$、SAC-D $10^{-3}$），在 50 个新种子上运行。
- DQN 采用 $\epsilon$-greedy 退火（$\epsilon$ 从 1.0 降到 0.05）；SAC/SAC-D 采用自动温度调节。

## 实验结果

### 实验目标
验证 CRL 在增量联合训练下是否存在稳定性缺口，并刻画其形态（骤降幅度、恢复程度）与跨算法/跨环境的一致性。

### 主要结果

![[mt_vs_ft_side_by_side_page1.png|600]]

> 图1(c)：多任务训练（左）能同时学会全部三个任务，而顺序微调（右）表现出灾难性遗忘。以 SAC-D 在 CartPole 上为例。

- **多任务 vs 顺序微调**：所有算法在多任务训练下均能学会三个任务，但在顺序微调下表现出灾难性遗忘，验证了基准的区分能力。
- **稳定性缺口确认**：在 DQN、SAC-D、DDPG、SAC 四种算法上，均在任务切换后观察到旧任务性能的显著下降，随后部分或完全恢复。
- **关键性质**：即使智能体对全部过往任务拥有完整访问权限（增量联合训练），该缺口依然存在——这与有监督 CL 中"增量联合训练下仍出现稳定性缺口"的发现一致。

### 消融与诊断
- 基准支持"证明概念、低成本实验、易复现的健全性检查"三类用途。
- 通过任务标识的省略构造更难变体，用于测试算法在无显式任务指示时的鲁棒性。

## 深度分析

### 研究价值评估

#### 理论贡献
- **CRL 中稳定性缺口的首次系统确认**：把有监督 CL 的"稳定性缺口"概念迁移到 RL 领域，填补了空白。
- **可复现的轻量基准**：基于正交变换的构造通用性强（可应用到任意向量观测环境），成本低、易复现，为后续诊断与缓解方法提供了标准测试床。

#### 实际应用价值
- **评测设计启示**：提示 CRL 研究与部署应监控训练过程中的暂时性退化，而非仅看终点指标。
- **缓解方法评估**：为"如何修复稳定性缺口"提供可对照的基准与评测协议。

### 方法优势详解
- **归因清晰**：任务间仅观测表示不同，动力学/奖励/动作空间一致，把性能波动唯一归因于任务切换。
- **成本低廉**：正交变换 + 现成控制环境，无需复杂环境工程，适合快速原型与 sanity check。
- **范式严格**：增量联合训练下仍现缺口，排除了"replay 近似不足"等解释，凸显现象本身的普遍性。

### 局限性分析
- **环境规模小**：仅在 CartPole 与 InvertedPendulum 两个简单控制环境上验证，任务数（N=3）与状态维度（d=4）较小，向高维视觉观测、大规模任务流的泛化尚未验证。
- **算法覆盖有限**：只覆盖 DQN/SAC-D/DDPG/SAC 四类 off-policy 算法，未涉足 on-policy（如 PPO）或模型-based 方法。
- **现象描述多于机制解释**：确认了缺口的形态，但对"为何在完全可访问旧任务时仍出现"的深层机制（优化器/表征动态）解释有限。

### 适用性与场景分析
- **适用场景**：需要在受控条件下诊断 CRL 算法稳定性的研究；测试遗忘缓解方法是否真正消除暂时性退化。
- **不适用场景**：追求真实复杂任务分布（视觉、长时程、稀疏奖励）或需要 SOTA 性能对比的场合。

## 与相关论文对比

### 对比论文选择依据
- 稳定性缺口概念的源头（有监督 CL）与 CRL 遗忘/迁移评测的代表工作。

| 对比维度 | De Lange et al. (有监督稳定性缺口) | 本文（CRL 稳定性缺口） |
|----------|-----------------------------------|--------------------------|
| 学习范式 | 有监督持续学习 | 持续强化学习 |
| 关键发现 | 任务切换后旧任务性能骤降再恢复 | 同一现象在 RL 中复现，且完全可访问旧任务时仍存在 |
| 基准设计 | 图像分类任务 | 正交变换观测的 CRL 控制任务 |
| 关系类型 | 源头 | 扩展（迁移到 RL 领域） |

## 技术路线定位

### 所属技术路线
持续强化学习（Continual RL）中的**灾难性遗忘与训练动力学**研究方向，聚焦于"稳定性缺口"这一暂时性退化现象的诊断与基准构建，属于评估方法学而非算法缓解路线的贡献。
