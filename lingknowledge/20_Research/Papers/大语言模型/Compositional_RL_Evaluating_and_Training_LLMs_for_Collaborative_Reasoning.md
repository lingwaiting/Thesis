---
date: "2026-10-14"
paper_id: "arXiv:2610.17456"
title: "Compositional RL: Evaluating and Training LLMs for Collaborative Reasoning"
authors: "Shangbin Feng, Yulia Tsvetkov, Jan Kautz, David Acuna"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 多智能体
  - 强化学习
  - 协作推理
  - Compositional-RL
quality_score: "8.5/10"
related_papers: []
created: "2026-10-14"
updated: "2026-10-14"
status: analyzed
---

# Compositional RL: Evaluating and Training LLMs for Collaborative Reasoning

## 核心信息
- **论文ID**：arXiv:2610.17456
- **作者**：Shangbin Feng、Yulia Tsvetkov（University of Washington）、Jan Kautz、David Acuna（NVIDIA）
- **机构**：University of Washington、NVIDIA
- **发布时间**：2026-10-12
- **会议/期刊**：ICLR 2027 投稿（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.17456) | [PDF](https://arxiv.org/pdf/2610.17456)
- **引用**：--

## 摘要翻译

### 英文摘要
Language models are often evaluated alone but increasingly deployed together, routed between, made to debate, ensembled, or collaboratively trained. For a model inside a system, what matters most is not how well it performs on its own, but how much the system gains from its presence. We call this a model's compositional strength and formalize it using the Shapley value of a cooperative game over coalitions of a model pool, and use a model's leave-one-out contribution to the full coalition as a tractable proxy, requiring n+1 rather than 2^n system evaluations. Evaluating individual vs. compositional strengths across five model pools, twelve collaboration methods, and twelve datasets, we find them largely uncorrelated: in 12 of 15 (pool, method-family) settings, individual strength does not translate into compositional advantages, and the coupling is weakest among general-purpose models. We then propose compositional reinforcement learning (C-RL), which trains a policy inside a live multi-model system and rewards the outcome produced by the system as a whole, rather than the policy's correctness in isolation. The leave-one-out baseline in Shapley values cancels under a group policy gradient. Across three collaboration protocols and eight tasks spanning reasoning, coding, and scientific discovery, C-RL produces the strongest collaborator in 21 of 24 settings, lifting collaborative performance by +4.0 to +5.4 pts on average.

### 中文翻译
语言模型通常被单独评估，但越来越多地被一起部署——被路由、辩论、集成或协同训练。对于系统中的某个模型，最重要的不是它单独表现如何，而是系统因它的加入获得了多少增益。作者称这一量为模型的「组合强度」（compositional strength），并用合作博弈中模型池联盟上的 Shapley 值将其形式化；同时用模型对全联盟的留一（leave-one-out）贡献作为可计算的近似，只需 n+1 次而非 2^n 次系统评估。在五个模型池、十二种协作方法、十二个数据集上评估个体强度与组合强度，发现二者基本不相关：在 12/15 的（池、方法族）设置中，个体强度无法转化为组合优势，且这种脱耦在通用模型中最为明显。作者进一步提出组合强化学习（C-RL），在真实多模型系统内部训练策略，奖励系统整体的产出而非策略单独的准确性。Shapley 值中的留一基线在组策略梯度下相互抵消，因此无需额外 rollout 成本。在三种协作协议、八类任务（推理、编程、科学发现）上，C-RL 在 24 个设置中的 21 个产生了最强协作者，协作性能平均提升 +4.0 至 +5.4 分。

### 核心要点提炼
- **研究背景**：LLM 越来越多地以「系统中的一员」而非孤立模型的方式被部署。
- **研究动机**：现有评估与训练都只优化模型「单独的正确性」，无法衡量/优化模型在协作系统中的价值。
- **核心方法**：用 Shapley 值定义「组合强度」；用留一边际作为可计算近似；提出 C-RL 用系统整体结果作为奖励信号训练策略。
- **主要结果**：个体强度与组合强度基本不相关；C-RL 在 21/24 设置中训练出最强协作者，平均提升 4.0-5.4 分。
- **研究意义**：首次系统性地论证「单体排行榜」无法作为「系统级智能」的代理，并提出面向协作的训练范式。

## 研究背景与动机

### 领域现状
LLM 的评估范式长期以「单模型、单基准」为中心：模型独立完成 benchmark、得分、登上 leaderboard。然而实际部署的系统中，模型极少单独工作——它们被用于路由（routing）、辩论（debate）、排序融合（rank-fusion）、集成（ensemble）甚至协同微调（collaborative fine-tuning）。

### 现有方法的局限性
- 标准评估只捕捉模型「单独完成任务」的能力，无法衡量模型对系统的边际贡献。
- 标准后训练（post-training）流程优化的同样是「单独正确性」，导致模型在系统中的价值既不被度量、也不被优化。

### 研究动机
作者提出一个关键量——**组合强度（compositional strength）**：一个模型加入系统后，系统整体因它提升了多少。本文回答两个问题：(1) 如何评估组合强度？(2) 如何为组合强度训练模型？

## 研究问题

### 核心研究问题
1. **评估**：如何形式化并高效地度量一个模型在协作系统中的「组合强度」？
2. **诊断**：个体强度（solo strength）能否预测组合强度？
3. **训练**：如何训练一个模型，使其成为更强的「协作者」（collaborator）？

## 方法概述

### 核心思想
借鉴合作博弈论中的 **Shapley 值**：把「某协作方法 + 某任务」建模为合作博弈，博弈的联盟是模型池的子集；一个模型的组合强度就是它在这个博弈中的 Shapley 值。由于精确计算 Shapley 值需要 2^n 次评估、代价不可接受，作者用**留一边际（leave-one-out marginal）**——即从全联盟中移除该模型后系统性能的下降——作为可计算近似，成本仅 n+1 次运行，且在实验中与精确值高度一致。

### 方法框架

#### 整体架构
1. **组合强度评估**：给定模型池 + 协作方法 + 任务，计算每个模型的留一边际作为组合强度。
2. **组合强化学习（C-RL）**：把一个策略（policy）放进活的多模型系统，用「系统整体产出」作为奖励信号进行 RL 训练，而非策略单独的正确性。

![[2610.17456_fig1.png|600]]

> 图1：组合强度与 C-RL 训练的核心示意（论文 Fig.1，展示个体强度 vs 组合强度的解耦，以及 C-RL 以系统结果为导向的训练回路）

### 关键创新

1. **组合强度（compositional strength）的形式化** - 用 Shapley 值 + 留一边际近似，首次把「模型在系统中的价值」变成可度量、可优化的量。
2. **个体强度与组合强度的系统性解耦证据** - 在大规模（5 池 × 12 方法 × 12 数据集）上证明排行榜名次无法预测协作价值，且通用模型解耦最严重。
3. **C-RL 训练范式** - 关键洞察是「留一基线在组策略梯度下自然抵消」，使得 C-RL 无需额外 rollout 成本、也无需运行「不含该策略的联盟」，即可训练出最强协作者。

## 实验结果

### 数据集
- **5 个模型池**、**12 种协作方法**、**12 个数据集**（覆盖推理、编程、科学发现）。

### 主要结果
- **个体 vs 组合强度**：15 个（池、方法族）相关设置中，12 个二者无统计显著关系；显著的三处相关系数也很弱（|ρ| ≤ 0.29）。
- **C-RL 训练效果**：三种协作协议 + 八类任务，24 个设置中 21 个产生最强协作者；协作性能平均提升 **+4.0 至 +5.4 分**（约为基线的 3 倍），最高 **+13.1 分**。
- **稳健性**：C-RL 是唯一一个「从不让系统比未训练策略更差」的方法，且可迁移到未见数据集、未见协作协议和前沿闭源模型池。

## 深度分析

### 研究价值
- **理论贡献**：把「模型协作价值」严格形式化为 Shapley 值，并给出留一边际的高效近似与理论依据。
- **实际应用**：为路由、辩论、集成、协同微调等真实多模型系统提供「选型 + 训练」两个环节的新准则。
- **领域影响**：直接挑战「单体排行榜」的评估范式，推动评估从「模型中心」转向「系统中心」。

### 优势
- 问题定义新颖且贴近真实部署趋势（多模型协同）。
- 实验规模大、维度全，结论可信度高。
- C-RL 的留一基线抵消洞察使其训练成本几乎不高于普通 RL。

### 局限性
- 留一边际只是 Shapley 值的近似，在强交互效应下可能偏差。
- 协作方法/协议空间巨大，当前覆盖仍有限。
- 组合强度依赖「系统产出」的可奖励性，开放域任务上的奖励定义仍是难点。

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** - 以清晰的博弈论视角重新定义并解决了「模型协作价值」的评估与训练问题，是评估范式的重要补充。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 组合强度概念 + C-RL 训练范式均新颖且有洞察 |
| 技术质量 | 8/10 | 形式化严谨，留一近似有理论支撑 |
| 实验充分性 | 9/10 | 5 池 × 12 方法 × 12 数据集，规模大 |
| 写作质量 | 9/10 | 结构清晰、论证扎实 |
| 实用性 | 8/10 | 对多模型系统部署有直接指导意义 |

### 重点关注
- **值得关注的技术点**：留一边际为何能在组策略梯度下抵消、C-RL 的具体奖励设计。
- **需要深入理解的部分**：Shapley 值近似误差在强交互场景下的表现。

## 相关论文
- 路由：RouteLLM；辩论：Du et al. 2023；融合：Jiang et al. 2023；协同微调：SPARTA 等。

## 外部资源
- arXiv: https://arxiv.org/abs/2610.17456

> [!tip] 关键启示
> 「单体排行榜名次」无法预测「模型在协作系统中的价值」；评估与训练都应转向以系统整体产出为导向。

> [!warning] 注意事项
> - 留一边际是 Shapley 值的近似，非精确解
> - 组合强度高度依赖协作方法与任务的组合
> - 开放域任务上的系统级奖励仍有挑战

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐——评估范式从「模型中心」到「系统中心」的代表性工作。
