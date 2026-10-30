---
date: "2026-10-30"
paper_id: "arXiv:2610.40227"
title: "EpiAgent: Epistemic Self-Play for LLM Agents"
authors: "Xuanfa Jin, Xinyu Cui, Zhijian Ma, Xue Yan, Jian Zhao, Haifeng Zhang, Jun Wang"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 认知博弈论
  - 多智能体系统
  - 自博弈
  - LLM-Agent
  - 信念推理
quality_score: "8.0/10"
created: "2026-10-30"
updated: "2026-10-30"
status: analyzed
---

# EpiAgent: Epistemic Self-Play for LLM Agents

## 核心信息
- **论文ID**：arXiv:2610.40227
- **作者**：Xuanfa Jin, Xinyu Cui, Zhijian Ma, Xue Yan, Jian Zhao, Haifeng Zhang, Jun Wang
- **机构**：中国科学院自动化研究所、中国科学院大学、中关村学院、伦敦大学学院（UCL）
- **发布时间**：2026-10-29
- **类别**：cs.AI, cs.CL, cs.MA
- **链接**：[arXiv](https://arxiv.org/abs/2610.40227) | [PDF](https://arxiv.org/pdf/2610.40227)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Epistemic game theory explains an agent's strategic behavior via the beliefs it holds about others and how these beliefs are updated as the interactions unfold. Modern LLM-based agents, however, often lack an explicit mechanism for learning and updating such beliefs to guide decisions under incomplete information. To bridge this gap, we propose EpiAgent, a framework that equips LLM-based agents with an explicit, learnable epistemic layer, which maintains beliefs over others' private information and combines these beliefs with the agent's available information into an epistemic state for action selection. Concretely, the agent's policy is decomposed into two coupled stages: Epistemic Update, which updates these beliefs from interaction histories, and Action Planning, which selects actions conditioned on the updated epistemic state. By formulating their dependency as a bi-level optimization problem, we introduce Epistemic Self-Play, an algorithm that alternately optimizes the two stages through self-play, utilizing an intrinsic prediction reward to evaluate how well inferred beliefs predict subsequent observations. Empirical results across both mixed and competitive scenarios show that EpiAgent consistently outperforms prompting-based and RL-based baselines.

### 中文翻译
认知博弈论（epistemic game theory）通过智能体对他人的信念以及这些信念随交互如何更新来解释其策略行为。然而，现代基于 LLM 的智能体往往缺乏一个显式机制来学习和更新这些信念，从而在信息不完全情况下指导决策。为弥补这一空白，本文提出 EpiAgent：为 LLM 智能体引入一个显式的、可学习的"认知层"（epistemic layer），它维护关于他人私有信息的信念，并将这些信念与智能体自身可用信息结合成"认知状态"（epistemic state）用于动作选择。具体地，策略被分解为两个耦合阶段：**认知更新**（Epistemic Update，从交互历史更新信念）和**动作规划**（Action Planning，基于更新后的认知状态选择动作）。作者将二者的依赖关系建模为双层优化问题，提出**认知自博弈**（Epistemic Self-Play）算法，通过自博弈交替优化两个阶段，并用内在预测奖励评估推断信念对后续观测的预测能力。在混合博弈与竞争博弈两类场景中的实证结果表明，EpiAgent 一致优于基于提示和基于 RL 的基线。

### 核心要点提炼
- **研究背景**：多智能体场景存在非平稳性与信息不完全，私有信息影响他人行为，但 LLM 智能体缺乏显式的信念学习与更新机制
- **核心方法**：EpiAgent——显式可学习认知层，策略分解为"认知更新 + 动作规划"两阶段，用 Epistemic Self-Play（双层优化 + 内在预测奖励）交替训练
- **关键目标**：让信念推理与动作策略协同进化，而不依赖私有信息的真实标签
- **主要结果**：在 Bargaining（混合）与 Kuhn Poker（竞争）上一致优于提示类与 RL 基线；信念推断准确率提升，学到的信念显著改善动作规划

## 研究背景与动机

### 领域现状
LLM 智能体在单智能体任务上表现出色，但多智能体场景引入了非平稳性与信息不完全：环境动态不仅取决于自身决策，还取决于拥有私有信息的其他智能体的策略。已有 LLM 智能体通常把观测直接映射到动作，或用提示诱发自由形式推理，缺乏结构化的认知过程。

### 现有方法的局限性
1. **缺少显式认知层**：多数智能体"观测→动作"直接映射，或用提示把推理当作生成的副产物，而非结构化认知过程
2. **对手建模与策略学习解耦**：即便引入显式对手建模，也常与策略学习分离，二者不能协同演化
3. **缺乏真值监督**：现实中其他智能体的私有信息往往不可观测，直接监督信念推理困难

### 研究动机
受认知博弈论启发，作者提出为 LLM 智能体增加显式、可学习的认知层，并让"信念推理"与"动作策略"在交互中协同训练，从而在信息不完全场景下做出更优决策。

## 研究问题

### 核心研究问题
如何在 LLM 智能体中引入显式、可学习的信念（认知）层，使其能从交互历史推断他人私有信息，并据此指导动作选择——且在缺乏私有信息真实标签的情况下依然可训练？

## 方法概述

### 核心思想
把智能体策略显式分解为"认知更新 + 动作规划"两个耦合阶段，将二者的相互依赖建模为双层优化问题，用自博弈交替训练；引入内在预测奖励，让信念层无需真值标签即可通过"预测后续观测"来学习。

### 方法框架

![[framework_page1.png|800]]

> 图1：EpiAgent 框架与 Epistemic Self-Play 训练。Agent 0 与 Agent 1 共享同一 LLM；策略分解为认知更新（信念推断）与动作规划两阶段，通过自博弈交替优化。

#### 各模块详细说明

**模块1：认知更新（Epistemic Update）**
- **功能**：从交互历史更新关于他人私有信息的信念
- **训练信号**：内在预测奖励（intrinsic prediction reward）——以推断信念对后续观测的预测对数似然为奖励，无需私有信息真值
- **形式化**：在 POSG（部分可观测随机博弈）框架下，维护关于他人私有信息的后验信念

**模块2：动作规划（Action Planning）**
- **功能**：基于更新后的认知状态选择动作
- **训练信号**：环境奖励 + 动作多样性熵正则

**模块3：Epistemic Self-Play（双层优化）**
- **功能**：交替优化认知更新与动作规划两阶段
- **关键**：认知更新从动作策略生成的轨迹学习，动作规划基于演化中的信念决策，二者通过自博弈协同演化

### 方法架构图
EpiAgent 的核心是"认知状态 = 私有信息 + 观测历史 + 当前信念"的显式表示，动作规划据此进行策略推理与动作选择，这是其区别于传统"观测→动作"映射的本质。

## 实验结果

### 实验设置
- **环境**：Bargaining（混合谈判博弈，智能体需在自利与破裂风险间权衡）、Kuhn Poker（竞争博弈，含随机性与欺骗）
- **基线**：ReAct（推理型提示）、Reflexion、HM（提示类）、ReAct+RL（同 RL 算法但无认知层）
- **消融**：EpiAgent w/o Epistemic Training（保留两阶段但只训动作规划）

### 主要结果
- EpiAgent 在 Bargaining 与 Kuhn Poker 中**平均奖励最高**，一致优于 ReAct、Reflexion、HM 等提示类基线与 ReAct+RL 训练基线
- 在与 RL 训练对手对战时依然保持优势，说明收益并非来自对特定对手的过拟合

### 关键发现
- **认知训练的价值**：消融显示，去掉认知更新训练（只训动作规划）会导致性能下降，说明专属认知训练带来额外收益
- **信念推断准确率**：随训练提升，内在预测奖励有效驱动了信念层学习
- **信念干预**：干预实验显示学到的信念确实贡献于动作规划，收益并非仅来自动作优化

## 深度分析

### 研究价值评估

#### 理论贡献
- 将认知博弈论的思想落地为 LLM 智能体的显式认知层，提供"信念推理 + 动作规划"协同演化的可训练框架
- 内在预测奖励解决了私有信息无真值标签下的信念学习难题

#### 实际应用价值
- 适用于谈判、扑克、外交等信息不完全的混合/竞争多智能体场景
- 显式信念层使智能体决策可解释，便于干预与调试

### 局限性
- 环境为二人博弈（Bargaining、Kuhn Poker），大规模多智能体场景的扩展性待验证
- 共享单一 LLM 的自博弈设定，与真实异构智能体交互存在差异
- 内在预测奖励依赖"后续观测可预测"的假设，在高度噪声环境中可能退化

## 与相关论文对比

### ReAct / Reflexion（提示类智能体）
- **关系**：基线
- **差异**：二者通过提示交织推理与动作，但无显式信念建模；EpiAgent 引入结构化认知层

### SWEET-RL / RAGEN（多轮 RL 智能体训练）
- **关系**：LLM 智能体 RL 训练路线
- **差异**：二者聚焦多轮信用分配与轨迹级训练，但不显式建模他人私有信息；EpiAgent 补上认知推理这一维度

## 技术路线定位

本文属于**LLM 智能体 + 认知博弈/信念建模**路线：从"观测→动作"的黑箱决策，走向"显式信念推理 + 信念条件动作"的结构化决策。承上继承了 LLM 智能体 RL 训练（ReAct+RL），启下为信息不完全场景下可解释、可协同的智能体设计提供新范式。

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 理论动机扎实（认知博弈论）、框架清晰、内在预测奖励巧妙；是 LLM 智能体结构化认知建模的代表性工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 显式认知层 + 双层自博弈训练是清晰新颖的组合 |
| 技术质量 | 8/10 | POSG 形式化 + 内在预测奖励，方法严谨 |
| 实验充分性 | 7/10 | 两类环境、消融、干预分析齐全，但环境规模有限 |
| 写作质量 | 8/10 | 理论脉络清晰 |
| 实用性 | 7/10 | 方向有潜力，规模化待验证 |

## 相关论文
- [[ReAct]] - 推理型提示基线
- [[Reflexion]] - 反思型提示基线
- [[SWEET-RL]] - 多轮信用分配 RL
- [[RAGEN]] - 轨迹级智能体 RL 框架

> [!tip] 关键启示
> 把智能体决策显式拆成"先推断他人私有信息、再基于信念行动"两个阶段，并用自博弈让二者协同演化，是让 LLM 智能体在信息不完全场景下变强的关键。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读——LLM 智能体认知建模方向的代表工作，理论动机尤其值得借鉴。
