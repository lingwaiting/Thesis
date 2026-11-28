---
date: "2026-11-28"
paper_id: "arXiv:2611.34777"
title: "When Every Rollout Fails: Splicing Learner-Conditioned Options into Long-Horizon Agent RL"
authors: "Haowen Hou, Yicheng Liu, Zhen Huang, Jiaqi Wang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 强化学习
  - 长程智能体
  - 智能体训练
quality_score: "8.0/10"
related_papers: []
created: "2026-11-28"
updated: "2026-11-28"
status: analyzed
---

# When Every Rollout Fails: Splicing Learner-Conditioned Options into Long-Horizon Agent RL

## 核心信息
- **论文ID**：arXiv:2611.34777
- **作者**：Haowen Hou, Yicheng Liu, Zhen Huang, Jiaqi Wang
- **机构**：--
- **发布时间**：2026-11-25
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2611.34777) | [PDF](https://arxiv.org/pdf/2611.34777)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Group-based reinforcement learning with verifiable rewards often yields all-fail groups on long-horizon agent tasks: every sampled trajectory receives zero reward, leaving group-relative methods without a reward-based policy gradient. A common remedy supplies complete solutions from a stronger model, but full trajectories are expensive and expose the learner to long off-policy histories. We introduce SPLICE, which constructs local training targets from failed current-policy trajectories online. For each all-fail group, a proposal model selects a splice point in a current-policy trajectory and generates several bounded options from that point; we estimate each option's value using repeated current-policy continuations, convert positive estimates into a return-weighted local target, and fit the current policy on the options.

### 中文翻译
基于组的强化学习配合可验证奖励，在长程智能体任务上常出现"全失败组"：每条采样轨迹都得零奖励，导致组相对方法拿不到基于奖励的策略梯度。常见补救是让更强模型提供完整解，但完整轨迹昂贵且让学习器暴露于过长的离策略历史。为此我们提出 SPLICE——在线从**失败**的当前策略轨迹中构造局部训练目标：对每个全失败组，提议模型在当前策略轨迹中选一个"剪接点"，从该点生成若干有界选项；用重复的当前策略续写估计每个选项的价值，把正值估计转化为回报加权的局部目标，再用这些选项拟合当前策略。

### 核心要点提炼
- **研究背景**：长程智能体 RL 常因稀疏奖励导致"全失败组"，组相对方法无梯度可用。
- **研究动机**：完整解引导太贵且引入过长离策略历史，需要更便宜的局部信号。
- **核心方法**：SPLICE 从失败轨迹中"剪接"出局部选项，用当前策略续写估计价值。
- **主要结果**：在 SWE-bench 与 BrowseComp-Plus 四组设置中全面超越 GRPO 与完整轨迹引导基线。
- **研究意义**：把"全失败"的死局变成可用的局部训练信号，显著提升长程智能体训练效率。

## 研究背景与动机

### 领域现状
LLM 智能体的强化学习普遍采用基于组的算法（如 GRPO），依赖组内轨迹的相对奖励来估计梯度。但长程任务（软件工程、深度研究）奖励稀疏，常出现整组轨迹全部失败、奖励全为零的情况，梯度信号彻底消失。

### 现有方法的局限性
- 用更强模型提供完整解是常见补救，但完整轨迹生成成本高。
- 完整轨迹长度远，学习器暴露于严重离策略的历史，损害 on-policy 学习。
- 缺乏一种"从失败中就地取材"的局部信号构造方式。

### 研究动机
失败轨迹往往并非全无价值——它在某一步之前是合理的。若能在失败轨迹的合理前缀处"剪接"并生成有界选项，就能用当前策略自身去评估这些选项，构造出无需外部强模型的局部训练目标。

## 研究问题

核心研究问题：**当长程智能体 RL 出现"全失败组"、组相对方法无梯度可用时，如何低成本地构造有效的局部训练信号？**

## 方法概述

### 核心思想
把"全失败组"从死局转化为机会：在每个失败轨迹中定位一个剪接点，从该点生成若干有界选项，用当前策略自身的重复续写去估计选项价值，把正值估计转成回报加权局部目标来拟合策略。

### 方法框架

![[method_overview_page1.png|800]]

> 图1：SPLICE 方法总览。从失败轨迹选剪接点 → 生成有界选项 → 当前策略重复续写估计价值 → 构造回报加权局部目标拟合策略。

#### 关键流程
1. **剪接点选择**：提议模型在当前策略的失败轨迹中选一个剪接点。
2. **有界选项生成**：从剪接点生成若干有界长度的候选选项。
3. **价值估计**：用当前策略的多次重复续写估计每个选项的价值。
4. **局部目标拟合**：把正值估计转化为回报加权的局部目标，拟合当前策略。

### 关键创新
- **就地取材的局部信号**：无需外部强模型的完整解，只依赖当前策略自身。
- **有界选项 + 重复续写**：把价值估计限定在短程，避免过长离策略历史。
- **回报加权局部目标**：只对正价值选项赋予权重，稳定梯度方向。

## 实验结果

### 主要结果
- 在两个长程任务（SWE-bench Verified 软件工程、BrowseComp-Plus 深度研究）× 两类策略家族的**四组设置中全面超越 GRPO 与完整轨迹引导基线**。
- 相对 GRPO 提升 **2.8–9.8 个百分点**。
- 在初始失败的 SWE 任务上，把 GRPO 的"毕业率"提升 **10.8 个百分点**。
- 额外实验显示 SPLICE 在提升性能的同时**降低了昂贵的提议模型计算量**。

![[training_efficiency_multiseed_page1.png|800]]

> 图2：多种子训练效率对比，展示 SPLICE 相对基线的成功率与效率提升。

## 深度分析

### 研究价值
- **理论贡献**：提出"从全失败组中剪接局部选项"的思路，缓解了稀疏奖励下组相对方法无梯度的问题。
- **实际价值**：降低长程智能体 RL 对强模型完整解的依赖，训练更省算力、更稳定。

### 局限性
- 价值估计依赖当前策略的重复续写，续写质量本身受策略能力约束。
- 剪接点选择与选项生成引入提议模型的额外计算（虽已比完整解便宜）。
- 验证集中在软件工程与深度研究两个领域，通用性待进一步检验。

### 适用场景
- 奖励稀疏、常现全失败组的长程智能体 RL 训练。
- 希望降低对外部强模型依赖、节省训练算力的场景。

## 我的综合评价

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "失败轨迹剪接出局部信号"的思路务实新颖 |
| 技术质量 | 8/10 | 有界选项+重复续写估计，方法完备 |
| 实验充分性 | 8/10 | 四组设置+多基线+效率分析，扎实 |
| 写作质量 | 8/10 | 结构清晰，动机充分 |
| 实用性 | 8/10 | 对长程智能体训练有直接工程价值 |

> [!tip] 关键启示
> 奖励稀疏导致"全失败组"时，不要急着引入更强的外部模型——先看看失败轨迹里有没有"可救的局部"，用学习者自身就能构造出有效的局部信号。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐。做智能体 RL / GRPO 训练优化的研究者必读，工程价值很高。
