---
date: "2026-08-20"
paper_id: "arXiv:2608.17941"
title: "Efficient RLVR Scheduling via Graph-Structured Online Difficulty Estimation"
authors: "Zhizhao Liu, Zhiliang Tian, Xi Wang, Zhihua Wen, Yihang Xiong, Zhiquan Lai, Dongsheng Li"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 强化学习
  - RLVR
  - 推理
  - 图模型
quality_score: "7.8/10"
created: "2026-08-20"
updated: "2026-08-20"
status: analyzed
---

# Efficient RLVR Scheduling via Graph-Structured Online Difficulty Estimation

## 核心信息
- **论文ID**：arXiv:2608.17941
- **作者**：Zhizhao Liu, Zhiliang Tian, Xi Wang, Zhihua Wen, Yihang Xiong, Zhiquan Lai, Dongsheng Li
- **机构**：--
- **发布时间**：2026-08-18
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2608.17941v1) | [PDF](https://arxiv.org/pdf/2608.17941v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Reinforcement learning with verifiable rewards (RLVR) improves the reasoning capabilities of large language models but relies on costly rollout exploration. Assigning the same exploration budget to samples with different difficulty levels is inefficient: easy samples may receive redundant rollouts, whereas difficult but learnable samples may receive too little exploration. Existing adaptive schedulers address this mismatch through curriculum-based sample selection or non-uniform rollout allocation based on estimated sample difficulty. However, obtaining reliable online difficulty estimates remains challenging: dedicated probing adds substantial generation overhead, whereas history-based estimators face a cold start with no initial observations and stale feedback, and typically ignore relations among samples. To address these limitations, we propose a plug-and-play graph-based online difficulty estimator that shares rollout feedback across related samples and continuously updates their difficulty estimates, mitigating cold start and staleness without dedicated probing. Specifically, we first construct a difficulty-aware sample graph based on semantic and reasoning similarities. Based on this graph, we introduce latent difficulty states and use a Potts prior to encourage neighboring samples to share the same state. We then employ a state-level Beta-Binomial model to aggregate the rollout outcomes associated with each state. Finally, we use an online mean-field variational algorithm to continuously update the latent-state assignments and state-level difficulty as new feedback arrives. Our framework can be integrated into sample-selection and rollout-allocation schedulers, enabling difficulty-adaptive exploration without dedicated probing. Experiments across multiple base models, RL schedulers, and benchmarks demonstrate that our framework achieves better performance.

### 中文翻译
带可验证奖励的强化学习（RLVR）能够提升大语言模型的推理能力，但依赖昂贵的 rollout 探索。对不同难度的样本分配相同的探索预算会导致效率低下：简单样本可能获得冗余的 rollout，而困难但可学习的样本可能获得过少的探索。现有自适应调度器通过基于课程的样本选择，或基于估计样本难度的非均匀 rollout 分配来应对这一不匹配。然而，获得可靠的在线难度估计仍然具有挑战性：专门的探测会带来显著的生成开销，而基于历史的估计器面临冷启动（无初始观测）和反馈陈旧的问题，并且通常忽略样本之间的关系。为解决这些局限，我们提出一个即插即用的基于图的在线难度估计器，它在相关样本之间共享 rollout 反馈并持续更新其难度估计，从而在无需专门探测的情况下缓解冷启动与陈旧问题。具体而言，我们首先基于语义相似性与推理相似性构建一个难度感知的样本图。基于该图，我们引入潜在难度状态，并使用 Potts 先验鼓励相邻样本共享相同状态。随后，我们采用状态级 Beta-Binomial 模型来聚合每个状态关联的 rollout 结果。最后，我们使用在线 mean-field 变分算法，随着新反馈的到来持续更新潜在状态分配与状态级难度。我们的框架可集成到样本选择与 rollout 分配调度器中，实现无专门探测的难度自适应探索。跨多个基座模型、RL 调度器和基准的实验表明，我们的框架取得了更优的性能。

### 核心要点提炼
- **研究背景**：RLVR 是提升 LLM 推理能力的主流手段，但 rollout 探索成本高昂，统一预算分配低效。
- **研究动机**：现有难度估计要么引入额外探测开销，要么面临冷启动与反馈陈旧，且忽略样本间关系。
- **核心方法**：构建难度感知样本图 + 潜在难度状态（Potts 先验）+ 状态级 Beta-Binomial 模型 + 在线 mean-field 变分推断。
- **主要结果**：即插即用，跨多基座模型/RL 调度器/基准取得更优性能。
- **研究意义**：为 RLVR 提供了无需专门探测的难度自适应探索机制，可无缝集成到现有调度器。

## 研究背景与动机

### 领域现状
RLVR 通过为推理任务提供可验证的奖励信号（如答案是否正确），成为提升 LLM 数学/代码推理能力的关键训练范式。其核心开销在于对每个样本进行多次 rollout 采样以估计梯度。

### 现有方法的局限性
1. **统一预算低效**：对简单样本和困难样本分配相同 rollout 次数，简单样本冗余、困难样本探索不足。
2. **专用探测开销大**：为估计难度而额外做生成探测，直接增加了本就昂贵的生成成本。
3. **历史估计器的问题**：
   - **冷启动**：训练初期无历史观测，难度估计不可靠；
   - **反馈陈旧**：历史统计滞后于样本的实时可学习性；
   - **忽略样本关系**：把样本视为独立，浪费了样本间相似性带来的信息共享机会。

### 研究动机
作者希望构建一个**无专门探测**、**无冷启动/陈旧**、**能利用样本间关系**的在线难度估计器，作为即插即用组件提升 RLVR 的探索效率。

## 研究问题

**核心研究问题**：如何在不断引入额外生成开销的前提下，为 RLVR 提供可靠、实时、可共享的样本难度估计，以支持难度自适应的样本选择与 rollout 分配？

## 方法概述

### 核心思想
利用样本之间的**语义与推理相似性**构建图结构，把"单个样本的难度估计"转化为"图上潜在状态的联合推断"——相邻（相似）样本倾向于共享同一难度状态，从而在样本间**共享 rollout 反馈**，一举解决冷启动与反馈陈旧问题，且无需额外探测。

![[0728_page1.png|600]]

> 图1：方法整体框架图，展示"难度感知样本图 → 潜在状态推断 → 难度自适应调度"的完整流程。

### 方法框架

#### 整体架构
框架由四个步骤构成：

1. **难度感知样本图构建**：基于语义相似性与推理相似性，将样本连接成图。
2. **潜在难度状态建模（Potts 先验）**：为每个样本引入潜在难度状态，用 Potts 先验鼓励相邻样本共享同一状态。
3. **状态级 Beta-Binomial 聚合**：对每个难度状态，用 Beta-Binomial 模型聚合该状态下所有样本的 rollout 成功/失败结果。
4. **在线 mean-field 变分更新**：随着新反馈到来，用 mean-field 变分算法持续更新状态分配与状态级难度。

#### 各模块详细说明

**模块1：难度感知样本图**
- **功能**：捕捉样本之间的关系，为反馈共享提供拓扑结构。
- **输入**：样本的语义表征与推理特征。
- **输出**：样本图（节点=样本，边=相似性）。
- **关键技术**：语义相似性（嵌入相似度）+ 推理相似性（推理路径/结构相似度）。

**模块2：潜在难度状态 + Potts 先验**
- **功能**：为每个样本分配离散难度状态，并通过先验强制"相似样本同难度"。
- **核心公式**：Potts 先验 $p(z) \propto \exp\left(\beta \sum_{(i,j)\in E} \mathbb{1}[z_i = z_j]\right)$，其中 $z_i$ 为样本 $i$ 的潜在难度状态，$E$ 为图边集。
- **意义**：$\beta$ 控制相邻样本状态一致性的强度，实现信息在图上传播。

**模块3：状态级 Beta-Binomial 模型**
- **功能**：为每个难度状态聚合 rollout 结果，估计该状态的"成功率"。
- **核心公式**：状态 $k$ 的成功概率 $p_k \sim \text{Beta}(\alpha_k, \beta_k)$，该状态下的成功次数 $s_k \sim \text{Binomial}(n_k, p_k)$。
- **意义**：用 Beta 先验 + Binomial 似然，得到平滑、可更新的状态级难度估计。

**模块4：在线 mean-field 变分算法**
- **功能**：新反馈到达时，增量更新状态分配与难度估计。
- **输入**：新 rollout 结果 + 当前状态估计。
- **输出**：更新后的潜在状态分配与状态级难度。
- **关键技术**：mean-field 变分推断，将后验分解为独立分布，实现高效的在线更新。

### 可集成性
该估计器可集成到两类调度器：
- **样本选择调度器**：优先选择"困难但可学习"的样本；
- **rollout 分配调度器**：按难度非均匀分配 rollout 次数。

## 实验结果

### 实验目标
验证图结构在线难度估计器在提升 RLVR 训练效率与最终推理性能上的效果，并考察其跨模型、跨调度器的通用性。

### 实验设置
- **基座模型**：多个基座模型（如 Qwen、Llama 系列）。
- **RL 调度器**：多种样本选择与 rollout 分配调度器。
- **基准**：多个推理基准。

### 关键结果
![[qwen_llama_batch_level_ours_window78.png|600]]

> 图2：batch-level 对比结果，展示本文方法（ours）在不同模型与窗口下的性能优势。

![[qwen_two_slice_rl_step_1_10_em_elbo_combined.png|600]]

> 图3：消融/训练动态结果，展示 RL 训练步骤中难度估计与模型性能的演化。

### 结果分析
- **性能提升**：跨多基座模型、多 RL 调度器、多基准均取得更优性能，说明方法的即插即用性与通用性。
- **无额外探测**：相比专用探测方法，本文以更低的生成开销获得可靠的难度估计。
- **冷启动/陈旧缓解**：图上的反馈共享使新样本也能借助相似样本快速获得难度估计。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：图结构难度估计范式**：将样本难度估计从"独立样本统计"提升为"图上潜在状态联合推断"。
  - 创新点：用 Potts 先验 + 状态级 Beta-Binomial 建模样本间关系。
  - 学术价值：为"如何利用样本相关性提升在线估计"提供了可复用的概率图模型框架。

- **贡献2：无探测的即插即用组件**：避免了专用探测的生成开销，可直接集成到现有调度器。

#### 实际应用价值
- **应用场景**：LLM 推理能力的大规模 RLVR 训练，尤其是推理样本规模大、rollout 成本敏感的场景。
- **优势**：在提升性能的同时不增加生成开销，训练效率更高。
- **潜在影响**：可降低 RLVR 训练的算力成本，加速推理模型迭代。

### 方法优势详解

#### 优势1：无需专门探测
- **描述**：通过图上的反馈共享获得难度估计，而非额外生成探测。
- **技术基础**：样本图 + 状态级聚合。
- **实验验证**：在无探测前提下性能优于基线。

#### 优势2：缓解冷启动与陈旧
- **描述**：新样本借助相似样本的历史反馈快速获得难度估计；状态级聚合持续更新。
- **技术基础**：Potts 先验的图平滑 + 在线变分更新。

### 局限性分析

#### 局限1：图构建依赖表征质量
- **描述**：语义/推理相似性的质量直接影响图结构与难度估计效果。
- **影响**：若相似性度量失真，图上的错误平滑可能误导难度估计。
- **可能的解决方案**：引入更鲁棒的相似性度量或端到端学习图结构。

#### 局限2：离散状态建模的粒度
- **描述**：用离散潜在状态近似连续难度，粒度选择影响精度。
- **可能的解决方案**：探索连续难度建模或分层状态。

## 技术路线定位

### 所属技术路线
本文属于**RLVR + 概率图模型 + 高效训练**交叉路线，核心特点：
- 特点1：以难度自适应探索提升 RLVR 训练效率。
- 特点2：用图结构建模样本间关系，实现反馈共享。
- 特点3：强调即插即用与低开销。

### 本文在技术路线中的位置
- **承上**：继承了 RLVR、课程学习、样本难度估计等前期工作。
- **启下**：为"关系感知的在线难度估计"提供了新范式，可推广到更广泛的 RL/主动学习场景。

## 未来工作建议

### 基于分析的未来方向
1. **方向1：端到端学习图结构**
   - 动机：当前图依赖预定义相似性，可学习更贴合任务的图结构。
   - 可能的方法：联合优化图构建与难度估计。

2. **方向2：连续/分层难度建模**
   - 动机：离散状态可能无法精细刻画难度梯度。
   - 可能的方法：高斯过程或深度隐变量模型。

3. **方向3：推广到更广泛的 RL/主动学习**
   - 动机：图结构在线估计的思想不局限于 RLVR。

## 我的综合评价

### 价值评分

#### 总体评分
**7.8/10** - 提出了一种清晰、可复用的图结构在线难度估计方法，用概率图模型优雅地解决了冷启动/陈旧/样本关系三大痛点，且即插即用、实验充分，是 RLVR 高效训练方向的扎实工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 将 Potts 先验 + Beta-Binomial 引入难度估计，组合新颖但组件成熟 |
| 技术质量 | 8/10 | 概率图模型推导严谨，方法清晰 |
| 实验充分性 | 8/10 | 跨多基座模型/调度器/基准验证，较全面 |
| 写作质量 | 8/10 | 问题-方法-实验逻辑清晰 |
| 实用性 | 8/10 | 即插即用、无额外探测，落地友好 |

### 重点关注

#### 值得关注的技术点
- Potts 先验在图结构难度共享中的作用与超参数 $\beta$ 的选择。
- 在线 mean-field 变分更新的具体推导与复杂度。

#### 需要深入理解的部分
- 状态级 Beta-Binomial 模型如何从样本级 rollout 结果聚合到状态级难度。

## 相关论文

### 直接相关
- [[A_Graph-Based_Reinforcement_Learning_Framework_for_Structured_Drift_Diagnosis_and_Recovery_in_Autonomous_LLM_Agents|A Graph-Based RL Framework]] - 图结构 + RL 的交叉
- [[Mixture-of-Expert_Blocks_Contain_Strong_Hallucination_Detection_Signals|Mixture-of-Expert Blocks]] - 大模型相关

### 背景相关
- [[On_the_Fragility_of_Self-Improving_Agents_Variance,_Task_Order,_and_Underspecification|On the Fragility of Self-Improving Agents]] - 自我改进可靠性

## 外部资源
- arXiv 页面：http://arxiv.org/abs/2608.17941v1

> [!tip] 关键启示
> 把"独立样本的难度估计"升级为"图上潜在状态的联合推断"，用样本间相似性实现反馈共享，是提升在线估计效率的有效范式。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！RLVR 高效训练方向的扎实工作，概率图模型方法清晰可复用，适合关注推理模型训练效率的读者。
