---
date: "2026-07-09"
paper_id: "arXiv:2607.01232"
title: "Is One Layer Enough? Training A Single Transformer Layer Can Match Full-Parameter RL Training"
authors: "Zijian Zhang, Rizhen Hu, Athanasios Glentis, Dawei Li, Chung-Yiu Yau, Hongzhou Lin, Mingyi Hong"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - RLHF
  - 后训练
  - Transformer
  - 层贡献度
  - 参数效率
quality_score: "8.5/10"
created: "2026-07-09"
updated: "2026-07-09"
status: analyzed
---

# Is One Layer Enough? Training A Single Transformer Layer Can Match Full-Parameter RL Training

## 核心信息
- **论文ID**：arXiv:2607.01232
- **作者**：Zijian Zhang (明尼苏达大学), Rizhen Hu (北京大学), Athanasios Glentis, Dawei Li, Chung-Yiu Yau (明尼苏达大学), Hongzhou Lin (Amazon), Mingyi Hong (明尼苏达大学)
- **机构**：明尼苏达大学, 北京大学, Amazon
- **发布时间**：2026-07-01
- **会议/期刊**：arXiv preprint
- **链接**：[arXiv](https://arxiv.org/abs/2607.01232) | [PDF](https://arxiv.org/pdf/2607.01232)
- **分类**：cs.LG, cs.CL

## 摘要翻译

### 英文摘要
Reinforcement learning (RL) has become a central component of post-training large language models (LLMs), yet little is understood about how RL adaptation is distributed across transformer layers. Existing approaches typically update all model parameters uniformly, implicitly assuming that every layer contributes similarly. This paper challenges that assumption through a systematic layer-wise study of RL training. The authors introduce "layer contribution" C(k), which measures the fraction of full RL improvement recovered by training a layer in isolation. Across 7 models spanning 2 model families (Qwen3, Qwen2.5), 3 RL algorithms (GRPO, GiGPO, Dr. GRPO), and multiple task domains (math reasoning, code generation, agentic decision-making), a remarkably stable pattern emerges: RL gains are highly concentrated in a small subset of transformer layers, with high-contribution layers consistently concentrated in the middle (~40–60% depth) of the transformer stack. Simple layer-aware training strategies consistently outperform standard full-parameter RL training.

### 中文翻译
强化学习已成为大语言模型后训练的核心组成部分，但我们对RL适应如何在Transformer层间分布知之甚少。现有方法通常均匀地更新所有模型参数，隐式地假设每个层贡献相似。本文通过系统的逐层RL训练研究挑战了这一假设。作者引入了"层贡献度"C(k)，衡量单独训练某一层能够恢复全参数RL改进的比例。跨7个模型（涵盖Qwen3和Qwen2.5两个模型家族）、3种RL算法（GRPO、GiGPO、Dr. GRPO）和多个任务领域（数学推理、代码生成、Agent决策），一个惊人稳定的模式浮现出来：RL增益高度集中在少数Transformer层中，高贡献层稳定集中在Transformer堆栈的中间（约40-60%深度）。简单的层感知训练策略一致地超越了标准全参数RL训练。

### 核心要点提炼
- **研究背景**：LLM的RL后训练默认更新所有参数，但没有人系统研究过RL增益在层间的分布
- **研究动机**：理解RL训练的层间动态可以大幅降低后训练成本
- **核心方法**：引入"C(k)层贡献度"指标，系统性测量每层对RL增益的独立贡献
- **主要结果**：RL增益高度集中在中间层（40-60%深度），单层训练可匹敌甚至超越全参数RL
- **研究意义**：可能彻底改变LLM后训练的成本结构——不需要更新所有参数，只需训练关键层

## 研究背景与动机

### 领域现状
当前LLM后训练的标准流程是：预训练 → 监督微调（SFT） → 强化学习（RLHF/GRPO等）。在RL阶段，几乎所有工作都默认更新全部模型参数。这带来了巨大的计算开销，尤其对于数百亿参数的大模型。

### 现有方法的局限性
- **全参数RL训练成本极高**：需要维护完整的模型副本、优化器状态和梯度
- **缺乏对层间动态的理解**：没有任何系统性研究揭示RL训练增益在各层间的分布
- **"均匀更新"假设未经检验**：默认所有层对RL改进同等重要的假设可能是错误的

### 研究动机
如果RL增益主要集中在少数层中，那么只需要训练这些关键层就可以达到几乎相同的效果——这将大幅降低后训练的计算成本。

## 研究问题

### 核心研究问题
1. RL训练的增益在Transformer各层间如何分布？
2. 训练单个层能在多大程度上恢复全参数RL的性能？
3. 高贡献层的分布规律是否跨模型、跨算法、跨任务稳定？
4. 如何利用这些发现设计更高效的层感知训练策略？

## 方法概述

### 核心思想
引入"层贡献度"C(k)作为衡量指标：对于第k层，冻结所有其他层，仅对该层进行RL训练，然后测量该层单独训练能恢复全参数RL改进的百分比。通过大规模的C(k)测量实验，揭示RL训练增益的层间分布规律。

### 方法框架

#### 整体架构

![[figure1_hero_page1.png|800]]

> 图1：核心发现——RL训练增益高度集中，左图为每层的C(k)贡献度分布，右图为仅训练单层的性能对比

#### C(k)的定义与测量

**层贡献度 C(k)**：
$$C(k) = \frac{\text{Perf}(\text{train layer } k) - \text{Perf}(\text{SFT})}{\text{Perf}(\text{full RL}) - \text{Perf}(\text{SFT})}$$

即：单独训练第k层获得的性能提升，占全参数RL训练总提升的比例。

实验覆盖范围：
- **7个模型**：Qwen3系列（0.6B, 1.7B, 4B, 8B, 14B, 32B）和Qwen2.5系列（7B）
- **3种RL算法**：GRPO, GiGPO, Dr. GRPO
- **多个任务**：数学推理（GSM8K, MATH）、代码生成（HumanEval, MBPP）、Agent决策（ALFWorld）

### 关键发现

![[fig_contribution_page1.png|800]]

> 图2：各层贡献度热力图——中间层（~40-60%深度）贡献度最高

**发现1：增益高度集中**
- 绝大多数RL增益可以通过训练少数中间层来恢复
- 某些情况下，单层训练甚至超越全参数训练

**发现2：高贡献层稳定集中在中间（40-60%深度）**
- 输入层和输出层贡献度最低
- 中间层的贡献度跨模型大小、算法和任务保持高度一致

**发现3：层贡献度排序跨场景稳定**
- 不同数据集间的层排序强相关
- 不同模型家族间（Qwen3 vs Qwen2.5）的层排序强相关
- 不同RL算法间的层排序强相关

**发现4：简单策略即可超越全参数RL**
- 仅训练中间层的RL策略一致超越全参数训练
- 自适应学习率（中间层高学习率，两端层低学习率）效果最佳

## 实验结果

### 主要结果

#### 单层训练 vs 全参数训练

![[aggregate_per_layer_overlay_delta_full_page1.png|800]]

> 图3：各层单独训练的汇总性能——深色柱代表全参数训练的delta，浅色代表单层训练

#### 跨任务验证

![[fig_cross_dataset_2panel_page1.png|800]]

> 图4：跨数据集的层贡献度一致性——不同数学推理数据集的层排序高度相关

#### 层感知训练策略

![[fig_guided_training_3models_page1.png|800]]

> 图5：层感知训练策略（自适应学习率、选择性层训练）vs 全参数训练——三个模型上一致超越

### 消融实验

![[fig_majority_vote_page1.png|800]]

> 图6：多数投票一致性的消融分析

## 深度分析

### 研究价值评估

#### 理论贡献
- **揭示RL后训练的层间不对称性**：首次系统性证明RL增益并非均匀分布
- **C(k)度量框架**：提供了一个可复用的分析工具，可用于研究其他训练范式的层间动态
- **稳定规律**：发现高贡献层集中在40-60%深度的规律跨模型、算法、任务高度稳定——这是一个罕见的"普适规律"

#### 实际应用价值
- **计算成本大幅降低**：单层训练可节省90%+的优化器状态和梯度计算
- **内存节省**：只需为关键层维护优化器状态，其余层可冻结或以低精度存储
- **即插即用的改进**：现有RL训练流程只需调整哪些层参与训练即可获得提升
- **加速实验迭代**：后训练实验可以只关注关键层，大幅提升研究效率

### 方法优势详解
1. **实验设计的系统性**：跨7个模型、3种算法、多任务的矩阵式实验设计极为严谨
2. **发现的普适性**：层贡献度排序跨多种维度保持稳定，增加了结论的可信度
3. **实践指导性强**：不仅发现现象，还提供了可直接使用的层感知训练策略

### 局限性分析
1. **仅覆盖Qwen系列**：虽然跨了两个模型家族，但缺乏对LLaMA、Gemma等其他热门架构的验证
2. **RL算法覆盖有限**：GRPO系列算法是当前主流，但类似PPO等经典方法未被覆盖
3. **未解释"为什么"是中间层**：现象描述充分，但对为什么会是中间层最高的机制解释不足
4. **层贡献度可能随训练阶段变化**：C(k)是否在RL训练的早期和后期保持一致？

### 适用性与场景分析
- **适用场景**：所有使用GRPO/PPO系列算法进行RL后训练的场景
- **不适用场景**：全量微调（非RL）、从头预训练

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** - 罕见的高质量实证研究，发现可能重新定义LLM后训练的成本结构，但机制解释有待深入

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 问题视角新颖但方法本身简单直接——系统测量+统计分析 |
| 技术质量 | 9/10 | 实验设计极为严谨和全面，跨7模型×3算法×多任务的矩阵式验证 |
| 实验充分性 | 9/10 | 覆盖维度全面，统计检验充分，跨场景一致性验证扎实 |
| 写作质量 | 8/10 | 清晰的问题陈述和数据呈现，图表的说服力强 |
| 实用性 | 10/10 | 立即可用于降低RL后训练成本，实际影响巨大 |

### 重点关注
- **C(k)的测量方法**：如何正确地设计这种"单层冻结训练"实验
- **为什么是中间层？** 这背后的表征学习理论值得深入探索

> [!tip] 关键启示
> 挑战"所有参数都需要更新"的默认假设，通过系统性测量揭示隐藏的结构化规律——这种"先测量、再优化"的研究范式值得在更多AI子领域推广。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！任何从事LLM后训练的人都应该读这篇论文——它可能为你节省90%的训练成本。

---

## 相关论文
- [[20_Research/Papers/大语言模型/Localized_LoRA-MoE_Block-wise_Low-Rank_Experts_With_Adaptive_Routing|Localized LoRA-MoE]] - 参数高效微调的另一条路线（MoE化LoRA）
- [[LoRA]] - 低秩适应的基础工作，与本文互补——LoRA关注"多少参数"，本文关注"哪些层"
