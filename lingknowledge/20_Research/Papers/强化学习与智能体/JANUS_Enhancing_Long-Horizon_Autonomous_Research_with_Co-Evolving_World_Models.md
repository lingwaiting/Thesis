---
date: "2026-10-13"
paper_id: "arXiv:2610.13194"
title: "JANUS: Enhancing Long-Horizon Autonomous Research with Co-Evolving World Models"
authors: "Mengdi Liu, Chenghao Jia, Hong Chang, Mingjie He, Shiguang Shan, Xilin Chen"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - LLM-Agent
  - World-Model
  - Autonomous-Research
  - Co-Evolution
quality_score: "8.2/10"
created: "2026-10-13"
updated: "2026-10-13"
status: analyzed
---

# JANUS: Enhancing Long-Horizon Autonomous Research with Co-Evolving World Models

## 核心信息
- **论文ID**：arXiv:2610.13194
- **作者**：Mengdi Liu, Chenghao Jia, Hong Chang, Mingjie He, Shiguang Shan, Xilin Chen
- **机构**：中国科学院计算技术研究所（ICT, CAS）
- **发布时间**：2026-10-09
- **会议/期刊**：arXiv 预印本（cs.AI / cs.CL / cs.MA）
- **链接**：[arXiv](https://arxiv.org/abs/2610.13194) | [PDF](https://arxiv.org/pdf/2610.13194)
- **引用**：--

## 摘要翻译

### 英文摘要
Long-horizon autonomous research has shown promise in accelerating scientific progress through sustained cycles of hypothesis generation, implementation, and validation. However, this Generate-Execute-Feedback paradigm relies on reactive trial-and-error, where costly and time-consuming executions introduce severe feedback delays, throttling evolutionary progress under explosively expanding search space. To address this challenge, we propose JANUS, a framework that unifies future outcomes forecasting and past experience reflection through a Predict-Verify-Evolve loop driven by a co-evolving world model. Within an evolving graph, the research agent proposes candidates while world model scores them by forecasting value improvement, information gain, and exploration utility, then accordingly selects, refines, or prunes branches prior to execution. After receiving ground-truth feedback, JANUS distills experience into evolving skills: hierarchical skills for research agent, exploration-strategy skills for world model, and their coordination protocols. This mechanism enables continual co-evolution: the research agent sharpens candidate generation while the world model refines its predictive judgment over extended horizons. Extensive evaluations on MLS-Bench-Lite and real research tasks demonstrate that JANUS significantly outperforms state-of-the-art baselines, achieving both higher evolving efficiency and ceiling.

### 中文翻译
长时程自主研究通过"假设生成 → 实现 → 验证"的持续循环，展现出加速科学进步的潜力。但这一 **Generate-Execute-Feedback（生成-执行-反馈）** 范式依赖**反应式试错**，代价高昂且耗时的执行带来严重的反馈延迟，在爆炸式扩张的搜索空间下抑制了进化进度。为此，我们提出 **JANUS**，一个通过**协同进化世界模型（co-evolving world model）**驱动的 **Predict-Verify-Evolve（预测-验证-进化）** 循环，统一"未来结果预测"与"过去经验反思"。在一个进化图（evolving graph）中，研究智能体提出候选方案，世界模型通过预测"价值提升、信息增益、探索效用"对候选打分，进而在执行前选择、精炼或剪枝分支。收到真实反馈后，JANUS 把经验蒸馏为**进化技能**：研究智能体的层次技能、世界模型的探索策略技能，以及二者的协调协议。这一机制实现持续协同进化——研究智能体不断强化候选生成，世界模型则在更长时程上精炼其预测判断。在 MLS-Bench-Lite 与真实研究任务上的大量评估表明，JANUS 显著超越最先进基线，同时实现更高的进化效率与天花板。

### 核心要点提炼
- **研究背景**：自主科研智能体采用"生成-执行-反馈"范式，但昂贵执行导致反馈延迟，试错效率低下。
- **研究动机**：需要一个能"在执行前预测结果、执行后反思经验"的机制，减少无谓执行、加速进化。
- **核心方法**：协同进化世界模型 + Predict-Verify-Evolve 循环 + 进化图 + 经验蒸馏为进化技能。
- **主要结果**：MLS-Bench-Lite 与真实任务上显著超越 SOTA，进化效率与天花板双高。
- **研究意义**：把世界模型引入自主科研智能体，实现"预测性试错"而非"反应性试错"。

## 研究背景与动机

### 领域现状
自主研究智能体（autonomous research agent）试图用 LLM 持续进行"提出假设 → 实现 → 验证"的循环来加速科研。近年工作在代码生成、实验设计等子任务上取得进展，但整体仍以"生成-执行-反馈"为主，执行（跑代码、做实验）是昂贵且耗时的瓶颈。

### 现有方法的局限性
- **反应式试错**：必须先执行才能获得反馈，执行成本高、延迟大。
- **搜索空间爆炸**：长时程任务中候选方案指数增长，无选择地执行导致资源浪费。
- **经验不沉淀**：过去的失败经验没有被系统性地提炼为可复用的技能。

### 研究动机
借鉴世界模型（world model）思想，让智能体在执行前就"预测"哪些候选值得做，在执行后用真实反馈"反思"并沉淀经验，从而把昂贵的执行用在刀刃上。

## 研究问题

### 核心研究问题
在长时程自主研究任务中，如何用一个协同进化的世界模型，在执行前预测候选方案的价值/信息增益/探索效用，在执行后把经验蒸馏为进化技能，从而显著提升进化效率与最终性能上限？

## 方法概述

### 核心思想
JANUS 用"预测-验证-进化"循环取代纯"生成-执行-反馈"：世界模型在进化图上对候选打分（预测），真实执行提供真值（验证），经验蒸馏为技能促进协同进化（进化）。

### 方法框架

#### 整体架构
JANUS 由研究智能体（candidate generator）与世界模型（predictive scorer）两部分构成，在进化图上交互。世界模型从三个维度打分：价值提升（value improvement）、信息增益（information gain）、探索效用（exploration utility），据此选择/精炼/剪枝分支。

![[2610.13194_fig1.png|600]]

> 图1：JANUS 框架——Predict-Verify-Evolve 循环与协同进化世界模型。

#### 关键组件说明

**组件1：进化图（Evolving Graph）**
- 候选方案以图结构组织，节点为候选，边表示改进/扩展关系。
- 世界模型在图上预测各分支的潜力，指导执行前的选择。

**组件2：协同进化世界模型**
- 预测三个维度：价值提升、信息增益、探索效用。
- 执行前据此选择（select）、精炼（refine）或剪枝（prune）分支，避免无谓执行。

**组件3：经验蒸馏为进化技能**
- 层次技能（hierarchical skills）：赋予研究智能体更好的候选生成能力。
- 探索策略技能：赋予世界模型更好的预测判断。
- 协调协议：让二者高效协作，实现持续协同进化。

## 实验结果

### 实验目标
验证 JANUS 在长时程自主研究任务上的进化效率与性能天花板。

### 数据集与基线
- **基准**：MLS-Bench-Lite（机器学习科学任务基准）+ 真实研究任务。
- **基线**：与最先进（SOTA）自主研究智能体对比。

### 主要结果
- **进化效率**：JANUS 显著高于基线，即用更少的执行次数达到同等或更好的性能。
- **性能天花板**：最终性能上限更高。
- **真实任务**：在真实研究任务上也稳定超越基线。

### 结果分析
JANUS 的核心优势在于"预测性试错"：世界模型在执行前筛掉低价值分支，把有限预算集中到高价值方向，从而在爆炸式搜索空间中保持高效。同时，经验蒸馏让智能体与世界模型持续协同进化，随时间推移"生成"与"判断"能力同步提升，拉高最终天花板。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：把世界模型引入自主科研智能体，用 Predict-Verify-Evolve 循环统一"结果预测"与"经验反思"。
  - 创新点：从"反应式试错"转向"预测性试错"，直击反馈延迟痛点。
- **贡献2**：提出研究智能体与世界模型的协同进化机制，二者能力同步提升。
  - 创新点：不仅智能体进化，世界模型的预测判断也随经验蒸馏而精炼。

#### 实际应用价值
- **应用场景1**：自动化机器学习（AutoML）、科学发现等长时程智能体任务，显著降低计算/时间成本。
- **应用场景2**：任何"执行昂贵、反馈延迟"的 LLM agent 场景（代码调试、实验设计、药物发现）。

### 局限性
- 世界模型的预测质量决定效率上限，预测不准时仍可能误剪有价值分支。
- 主要验证于 MLS-Bench-Lite 与特定真实任务，跨领域（化学、材料等）的通用性待验证。
- 进化图的结构设计（如何定义边、如何扩展）仍有较大设计空间。

### 未来工作
- 提升世界模型的预测准确性与不确定性估计。
- 在更广泛的长时程科研任务（跨学科）上验证。
- 研究更自动化的进化图构建与剪枝策略。

### 与相关工作对比
- **vs 反应式自主研究智能体（如 AI Scientist 等）**：依赖"生成-执行-反馈"试错；JANUS 用世界模型预测先行，减少无谓执行。
- **vs 单智能体世界模型（如 D2R 等）**：世界模型仅用于复述/预测；JANUS 强调智能体与世界模型的协同进化。
- **研究路线归属**：属于"世界模型 + LLM 自主智能体"交叉，重点解决长时程任务中的反馈延迟与搜索效率问题。
