---
date: "2026-09-24"
paper_id: "arXiv:2609.26704"
title: "Beyond Repeated Sampling: Learning Search Policies for LLM Reasoning"
authors: "Ismail Labiad, Matthieu Kowalski, Marc Schoenauer, Rémi Munos, Julia Kempe"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 推理
  - 测试时计算
  - 强化学习
quality_score: "8.7/10"
related_papers: []
created: "2026-09-24"
updated: "2026-09-24"
status: analyzed
---

# Beyond Repeated Sampling: Learning Search Policies for LLM Reasoning

## 核心信息
- **论文ID**：arXiv:2609.26704
- **作者**：Ismail Labiad, Matthieu Kowalski, Marc Schoenauer, Rémi Munos, Julia Kempe
- **机构**：--
- **发布时间**：2026-09-22
- **会议/期刊**：cs.CL / cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2609.26704) | [PDF](https://arxiv.org/pdf/2609.26704)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models increasingly tackle hard reasoning problems by spending more test-time compute, yet the dominant strategy remains naive repeated sampling: draw many independent solutions and hope one is correct. Because such sampling explores only through local decoding noise, it tends to produce many near duplicate attempts rather than genuinely different ideas. We ask whether exploration can instead be steered at a semantic level, by first sampling problem specific concepts, hints, or strategies and then conditioning answer generation on them. We refine this into a simple, more exploratory procedure that emits many diverse concepts in a single trajectory, and evaluate it on hard problems where repeated sampling struggles. We then go a step further and make concept generation trainable: a small concept generator is optimized with reinforcement learning so that its concepts maximize the downstream success of a larger, frozen answer generator. On hard mathematical reasoning problems, the trained concept generator substantially improves the answer generator's pass@k over naive repeated sampling at the same answer generation allocation, surpasses concepts drawn from much larger untuned models, and transfers to answer generators it was never trained against, including a model from a different family. A small model can thus be trained into an effective, reusable search policy for a much larger one.

### 中文翻译
大语言模型越来越多地通过投入更多测试时计算来攻克困难推理问题，但主流策略仍是朴素的重复采样：抽取大量独立解，寄希望于其中一个是正确的。由于这种采样只通过局部解码噪声进行探索，它往往产生大量近乎重复的尝试，而非真正不同的思路。我们追问：能否在**语义层面**引导探索——先采样问题特定的概念、提示或策略，再以它们为条件生成答案？我们将其精炼为一个更简单的、更具探索性的流程，在单条轨迹中一次性发出大量多样化概念，并在重复采样表现不佳的难题上加以评估。我们进一步让概念生成变得**可训练**：用一个强化学习优化的小型概念生成器，使其生成的概念最大化一个更大的、被冻结的答案生成器的下游成功率。在困难数学推理问题上，训练后的概念生成器在相同答案生成预算下，大幅提升了答案生成器的 pass@k，超过朴素重复采样，也超过了从大得多、未调优的模型中抽取的概念；并且能迁移到它从未训练过的答案生成器上，包括来自不同模型族的模型。因此，一个小模型可以被训练成一个有效的、可复用的搜索策略，服务于一个大得多的模型。

### 核心要点提炼
- **研究背景**：test-time compute 是攻克难题的主流路径，但重复采样探索效率低。
- **研究动机**：局部解码噪声只能产生近重复尝试，缺乏真正的语义多样性。
- **核心方法**：先采样问题特定概念/提示/策略，再条件化生成答案；用 RL 训练小型概念生成器。
- **主要结果**：训练后的概念生成器大幅提升 pass@k，超过重复采样与大模型概念，且可跨模型族迁移。
- **研究意义**：把"小模型"训练成"大模型的搜索策略"，为 test-time scaling 开辟新范式。

## 研究背景与动机

### 领域现状
Test-time scaling（测试时扩展）已成为提升 LLM 推理能力的核心手段，主流方法包括 repeated sampling、多数投票、以及带搜索的推理（如 [[Tree of Thoughts]]、[[MCTS]] 类方法）。

### 现有方法的局限性
重复采样是最朴素也最普遍的 test-time 策略，但其探索机制仅依赖解码的**局部噪声**，导致生成的多个候选解高度相似——"多样性"只是表层的措辞差异，而非真正的思路分叉。在高难度问题上，这会导致 pass@k 的收益随采样数迅速饱和。

### 研究动机
作者提出关键问题：能否在**语义层面**而非**词元层面**进行探索？即先显式采样"概念/提示/策略"，再以它们为条件生成答案，从而让不同候选解对应真正不同的解题思路。

## 研究问题

### 核心研究问题
如何让 test-time 探索发生在语义层面，并将"概念生成"本身训练成一个可复用、可迁移的搜索策略，以服务一个更大的冻结答案生成器？

## 方法概述

### 核心思想
将"生成答案"拆解为两阶段：**先采样概念，再条件化生成**。概念生成器（一个小模型）负责发出多样化的解题提示/策略，答案生成器（一个大而冻结的模型）在这些概念的条件化下生成答案。概念生成器用 RL 训练，以"最大化下游答案生成成功率"为奖励。

![[method_figure_page1.png|800]]

> 图1：方法架构——小型概念生成器（RL 训练）发出多样化概念，条件化一个冻结的大答案生成器，实现语义级探索。

### 方法框架

#### 整体架构
1. **概念采样**：概念生成器在单条轨迹中发出大量多样化的 problem-specific 概念（提示、策略、中间假设）。
2. **条件化生成**：答案生成器以每个概念为条件，生成对应答案。
3. **RL 训练**：冻结答案生成器，用强化学习优化概念生成器，奖励信号为"下游答案的正确率（pass@k）"。

#### 训练目标
概念生成器的目标是最大化 `$k$` 次答案生成后的成功率，而非生成单个最优概念。这使得它学会**探索性**地覆盖多样化解题方向，而非收敛到单一"最好"提示。

### 关键创新
1. **语义级探索**：把探索从"词元噪声"提升到"概念/策略"层面，真正增加思路多样性。
2. **可训练的概念生成器**：用 RL 优化"概念 → 下游成功率"这一端到端目标。
3. **小模型服务大模型**：证明小模型可被训练成大模型的通用搜索策略，且可跨模型族迁移。

## 实验结果

### 数据集
- 困难数学推理问题集（高难度数学题，重复采样在此类问题上挣扎）。

### 实验设置
- **评估指标**：pass@k（k 次采样中至少一次正确的概率）。
- **关键对比**：
  - 朴素重复采样；
  - 从大得多、未调优模型中抽取的概念；
  - 跨答案生成器（包括不同模型族）的迁移。

### 主要结果
- 训练后的概念生成器在**相同答案生成预算**下，pass@k 显著优于朴素重复采样。
- 其概念优于从更大未调优模型抽取的概念。
- 可迁移到未训练过的答案生成器（含不同模型族），保持增益。

#### 结果分析
- 语义级探索带来了比重复采样更陡峭的 pass@k 增长曲线，说明"思路多样性"比"候选数量"更关键。
- 可迁移性表明，学到的是"通用的解题策略"而非对特定模型的过拟合。

### 消融实验
论文分析了概念数量（num_concepts）、多样性（Vendi Score 等）、概念位置偏差（position bias）等因素对 pass@k 的影响，验证概念生成的多样性与质量对下游收益的贡献。

## 深度分析

### 研究价值
- **理论贡献**：为 test-time scaling 提供了一个"可学习的搜索策略"框架，把探索显式建模为可优化对象。
- **实际应用**：小模型做"策略头"、大模型做"执行体"，可显著降低高质量推理的推理成本。
- **领域影响**：与 [[DeepSeek-R1]]、[[o1]] 类"test-time 强化推理"路线互补，但把焦点从"推理链本身"转移到"解题策略的生成与迁移"。

### 优势
- 直击重复采样"多样性不足"的本质问题，动机清晰。
- 小模型 + 冻结大模型的组合，工程上经济且易部署。
- 跨模型族迁移能力极强，通用性好。

### 局限性
- 实验集中于数学推理，其他领域（代码、开放域问答）的泛化待验证。
- RL 训练概念生成器仍需一定算力与奖励信号设计。
- 概念生成本身增加了推理步数与延迟，收益与开销的权衡需具体场景评估。

### 适用场景
- 高难度、多解法的推理任务（数学、竞赛题、定理证明）。
- 需要在固定 token 预算下最大化正确率的 test-time scaling 场景。

## 技术路线定位

本文属于 **Test-time Scaling / 推理时搜索** 路线，核心贡献是把"搜索策略"从手工设计（如 CoT、ToT、MCTS）推进到"可学习、可迁移"。它与 [[Tree of Thoughts]]、[[MCTS]]、[[DeepSeek-R1]] 等共享"用更多计算换取更好推理"的目标，但提出了"概念级探索 + 小模型策略头"这一新范式。

## 未来工作建议

1. 扩展到代码生成、定理证明、开放域问答等更多推理任务。
2. 探索概念生成器与答案生成器的联合训练，进一步压缩端到端成本。
3. 结合验证器/奖励模型，让概念生成器学会"自适应"地决定何时停止探索。

## 我的综合评价

### 价值评分
- **总体评分**：8.7/10
- **分项评分**：
  - 创新性：9/10（"可学习的语义级搜索策略"这一视角新颖）
  - 技术质量：8/10（RL 训练 + 迁移实验设计严谨）
  - 实验充分性：8/10（消融充分，但领域较单一）
  - 写作质量：9/10（motivation 极其清晰，问题定义漂亮）
  - 实用性：8/10（小模型服务大模型，落地潜力大）

### 突出亮点
- "语义级探索 vs. 词元级噪声"的问题定位直击要害。
- 小模型 → 大模型的"策略头"范式，经济且可迁移。
- 跨模型族迁移是强证据，说明学到的是通用策略。

### 重点关注
- 概念生成器的 RL 奖励设计与训练稳定性。
- 概念多样性（Vendi Score）与下游 pass@k 的定量关系。

### 可借鉴点
- 把"探索"作为显式可训练对象的思路，可迁移到检索、规划等场景。
- 小模型做策略、大模型做执行的组合范式。

### 批判性思考
- 概念的语义空间是否受限？过强的概念先验是否会压制答案生成器的创造力？
- 数学领域的成功能否迁移到"答案正确性难以自动判定"的开放任务？
- 概念生成引入的额外延迟与 token 开销，在低预算场景是否划算？

## 我的笔记

%% 用户可在此补充个人阅读笔记 %%

## 相关论文
- [[Tree of Thoughts]] - 通过显式思维树搜索提升推理，与本工作同属"结构化 test-time 探索"
- [[DeepSeek-R1]] - 用 RL 强化推理链，本工作把 RL 对象转向"解题策略"而非"推理链本身"

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.26704)
- [PDF](https://arxiv.org/pdf/2609.26704)

> [!tip] 关键启示
> 重复采样的低效源于"词元级噪声"而非"语义级探索"；把探索显式建模为可 RL 训练的概念生成器，能让一个小模型成为大模型通用、可迁移的搜索策略。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐——对做推理、test-time scaling、RL 的研究者而言，这是一篇视角新颖、动机清晰、实验扎实的代表作。
