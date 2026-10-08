---
date: "2026-10-08"
paper_id: "arXiv:2610.08989"
title: "LASER: Latent Space Adjoint Matching for Support-Constrained Entropy-Regularized Offline RL"
authors: "Songyuan Zhang, Oswin So, Eric Yang Yu, Matthew Cleaveland, Peter Crowley-Dolen, Chuchu Fan"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Offline-RL
  - Flow-Matching
  - Latent-Space
  - Entropy-Regularization
quality_score: "8.5/10"
related_papers: []
created: "2026-10-08"
updated: "2026-10-08"
status: analyzed
---

# LASER: Latent Space Adjoint Matching for Support-Constrained Entropy-Regularized Offline RL

## 核心信息
- **论文ID**：arXiv:2610.08989
- **作者**：Songyuan Zhang, Oswin So, Eric Yang Yu, Matthew Cleaveland, Peter Crowley-Dolen, Chuchu Fan
- **机构**：MIT（作者背景推断，项目主页 mit-realm）
- **发布时间**：2026-10-06
- **分类**：cs.LG / cs.AI / cs.RO / math.OC / stat.ML
- **链接**：[arXiv](https://arxiv.org/abs/2610.08989) | [PDF](https://arxiv.org/pdf/2610.08989)
- **引用**：--
- **项目主页**：https://mit-realm.github.io/laser/

## 摘要翻译

### 英文摘要
Offline RL enables policy optimization from static datasets without costly online interaction, but remains bottlenecked by the risk of executing out-of-distribution (OOD) actions. Recent approaches learn a behavior-cloning policy through flow matching and then perform RL within its constrained latent space. However, naively optimizing the latent policy can cause collapse into a brittle mode or exploit sharp artifacts of the learned critic. The authors find entropy regularization is essential in latent-space RL. They introduce LASER, an offline RL algorithm applying latent-space adjoint matching to achieve entropy-regularized latent-space RL with expressive flow policies while avoiding backpropagation through time. On 40 challenging OGBench tasks with varying dataset qualities, LASER achieves SOTA. Notably, LASER uses fixed method-specific hyperparameters across all tasks and outperforms baselines including those with task- and dataset-specific tuning.

### 中文翻译
离线强化学习（offline RL）能让我们在不进行昂贵在线交互的情况下，从静态数据集中优化策略，但仍受制于执行分布外（OOD）动作的风险。近期方法通过学习基于 flow matching 的行为克隆策略，然后在其约束的潜空间中进行 RL。然而，天真地优化潜策略很容易导致策略坍缩为脆弱的模式，或利用所学 critic 的尖锐伪影。本文发现，**熵正则化**在潜空间 RL 中是解决这些挑战的关键。作者提出 LASER，一种离线 RL 算法，通过在潜空间施加"伴随匹配（adjoint matching）"来实现带熵正则化的潜空间 RL，同时支持表达力强的 flow 策略，并避免沿时间反向传播（BPTT）。在 40 个具有不同数据质量的 OGBench 任务上，LASER 达到最优（SOTA）。值得注意的是，LASER 在所有任务上使用固定的方法级超参数，且超越了包括那些针对任务和数据集单独调参的基线。

### 核心要点提炼
- **研究背景**：离线 RL 受 OOD 动作风险制约
- **研究动机**：flow matching + 潜空间 RL 的朴素优化会坍缩/利用 critic 伪影
- **核心方法**：LASER = 潜空间伴随匹配 + 熵正则化 + flow 策略 + 免 BPTT
- **主要结果**：40 个 OGBench 任务 SOTA，固定超参数胜出
- **研究意义**：为潜空间离线 RL 提供稳定、免调参的高性能算法

## 研究背景与动机

### 领域现状
离线 RL 从静态数据集中学习，避免了在线交互的成本与风险，但学习到的策略容易执行数据覆盖范围之外的动作（OOD），导致价值估计灾难性偏差。近期工作用 flow matching 学习行为克隆策略，把动作映射到受约束的潜空间，再在潜空间内做 RL。

### 现有方法的局限性
- **潜策略坍缩**：天真优化潜策略容易坍缩到脆弱模式
- **利用 critic 伪影**：潜空间 RL 可能利用所学 critic 的尖锐伪影，导致策略不鲁棒
- **BPTT 开销**：flow 策略通常需要沿时间反向传播，计算昂贵

### 研究动机
熵正则化被证明是潜空间 RL 稳定性的关键，但如何在表达力强的 flow 策略上高效地实现熵正则化、同时避免 BPTT，仍是一个未解决的问题。

## 研究问题

### 核心研究问题
如何在受 support 约束的潜空间中，实现**带熵正则化**的离线 RL，同时保持 flow 策略的表达力并避免 BPTT，从而在无需逐任务调参的情况下稳定达到 SOTA？

## 方法概述

### 核心思想
LASER 在 flow matching 的潜空间中进行策略优化，通过"伴随匹配（adjoint matching）"技巧计算熵正则化项所需的梯度，从而在避免 BPTT 的同时引入熵正则化，防止策略坍缩。

### 方法框架

![[algo_page1.png|600]]

> 图1：LASER 算法——在 flow matching 约束的潜空间中，用伴随匹配实现熵正则化的潜空间策略优化，避免沿时间反向传播。

### 关键创新

1. **潜空间伴随匹配** - 用 adjoint 方法计算熵正则化梯度，避免 BPTT，兼顾表达力与效率
2. **熵正则化入潜空间 RL** - 系统揭示熵正则化是防止潜策略坍缩/利用 critic 伪影的关键
3. **固定超参数的鲁棒性** - 全任务固定方法级超参数即达 SOTA，超越逐任务/逐数据集调参基线

## 实验结果

### 数据集 / 实验设置
- 基准：40 个 OGBench 任务（不同数据集质量）
- 对比：包括针对任务/数据集单独调参的基线

### 主要结果
- 在 40 个 OGBench 任务上达到 SOTA
- 使用固定方法级超参数，鲁棒性优于逐任务调参基线
- 熵正则化被证明对潜空间 RL 稳定性至关重要

## 深度分析

### 研究价值
- **理论贡献**：把熵正则化与 flow matching 潜空间 RL 结合，并提供免 BPTT 的计算方案
- **实际应用**：机器人、自动驾驶等无法在线交互的离线决策场景
- **领域影响**：为离线 RL 的"潜空间 + 生成模型"路线提供稳定、免调参的算法

### 优势
- 免 BPTT，计算高效
- 熵正则化解决坍缩/伪影问题，鲁棒性强
- 固定超参数即可 SOTA，实用性强（无需昂贵调参）

### 局限性
- 依赖 flow matching 学习行为先验，先验质量影响上界
- 摘要未展开 adjoint matching 的近似误差分析
- 主要在 OGBench 上验证，真实机器人/连续控制的迁移待观察

### 适用场景
- 无在线交互的离线决策（机器人、自动驾驶、工业控制）
- 数据质量参差的离线数据集

## 技术路线定位

本文属于**离线强化学习 + 生成式行为建模（flow matching）**路线，具体聚焦"受约束潜空间中的熵正则化策略优化"，由 MIT Reliable Autonomous Systems 组（推测）推进。

## 我的综合评价

### 价值评分
- **总体评分**：8.5/10
- **创新性**：8/10（adjoint 匹配 + 熵正则化入潜空间 RL）
- **技术质量**：9/10
- **实验充分性**：8/10（40 任务 + 对比调参基线）
- **写作质量**：8/10
- **实用性**：9/10（固定超参数，落地友好）

### 突出亮点
- 免 BPTT 的 adjoint 匹配实现熵正则化
- 固定超参数即可 SOTA，实用性突出
- 明确揭示熵正则化在潜空间 RL 中的关键作用

### 重点关注
- adjoint 匹配的数值稳定性与近似误差
- 与扩散策略（diffusion policy）路线的横向对比

### 可借鉴点
- 用 adjoint 方法避开 BPTT 的梯度计算技巧
- "熵正则化防止潜策略坍缩"这一 insight 可迁移到其他生成式策略模型
- 固定超参数的设计哲学

## 相关论文
- 待补充

## 外部资源
- [arXiv](https://arxiv.org/abs/2610.08989)
- [PDF](https://arxiv.org/pdf/2610.08989)
- [项目主页](https://mit-realm.github.io/laser/)
