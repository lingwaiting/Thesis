---
date: "2026-10-15"
paper_id: "arXiv:2610.18808"
title: "Optimizing Team Behavior via Extremum-seeking Control in Multi-agent Reinforcement Learning"
authors: "Svar Rajankumar Patel, Kshitij Jerath"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - 多智能体
  - 行为多样性
  - 极值搜索控制
  - MARL
quality_score: "8.0/10"
related_papers: []
created: "2026-10-15"
updated: "2026-10-15"
status: analyzed
---

# Optimizing Team Behavior via Extremum-seeking Control in Multi-agent Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2610.18808
- **作者**：Svar Rajankumar Patel、Kshitij Jerath
- **机构**：马萨诸塞大学洛厄尔分校（UMass Lowell），计算机科学系 / 机械与工业工程系
- **发布时间**：2026-10-13
- **会议/期刊**：IEEE（cs.MA / cs.AI / cs.RO / eess.SY）
- **链接**：[arXiv](https://arxiv.org/abs/2610.18808) | [PDF](https://arxiv.org/pdf/2610.18808)
- **引用**：--

## 摘要翻译

### 英文摘要
In cooperative Multi-Agent Reinforcement Learning (MARL), agents must learn to coordinate their actions to achieve shared objectives. A key indicator of effective coordination is behavioral diversity... We propose Adaptive Diversity Control (ADiCo), a two-timescale framework that adapts behavioral diversity online during training. On a fast timescale, Diversity Control (DiCo) regulates system diversity toward a target value. On a slower timescale, an Extremum Seeking Controller (ESC) adjusts this target using only mean episodic returns, without requiring an explicit model or analytical gradient of the diversity-performance landscape. We evaluate ADiCo on six configurations across two cooperative MARL tasks... ADiCo matches or exceeds the mean performance of DiCo (best grid-searched diversity target) and Unconstrained DiCo.

### 中文翻译
在合作型多智能体强化学习（MARL）中，智能体必须学习协调动作以实现共同目标。有效协调的一个关键指标是**行为多样性**——智能体在相同观测下能执行不同且互补的控制策略的潜力。虽然行为多样性有助于角色分化与更丰富的联合行为，但其合适的水平高度依赖上下文与任务，且通常调参成本高昂。作者提出 **Adaptive Diversity Control（ADiCo）**，一个双时间尺度框架，在训练中在线自适应行为多样性：快时间尺度上，**DiCo**（多样性控制）将系统多样性调控到目标值；慢时间尺度上，**ESC**（极值搜索控制器）仅用平均 episodic 回报来调整该目标值，无需显式模型或多样性-性能景观的解析梯度。在 2 个合作 MARL 任务、6 种配置上，ADiCo 匹配或超过了 DiCo（最佳网格搜索目标）与 Unconstrained DiCo 的平均性能。

### 核心要点提炼
- **研究背景**：行为多样性对 MARL 协调有益，但合适水平强依赖任务、调参昂贵。
- **研究动机**：如何在线、无模型地自适应行为多样性的目标值？
- **核心方法**：ADiCo 双时间尺度框架 = 快 DiCo 调控多样性 + 慢 ESC 用平均回报调整目标。
- **主要结果**：2 任务 6 配置下匹配或超过最佳网格搜索的 DiCo。

## 研究背景与动机

### 领域现状
合作 MARL 中，行为多样性已被证明能带来互补角色涌现、更好探索与更强韧性。在 CTDE 范式下，多样性由参数共享、辅助目标、内在奖励等设计选择所塑造。

### 现有方法的局限性
- 现有方法多「诱导」或「鼓励」多样性，但不显式调控其大小到可度量的目标水平。
- 合适的多样性目标依赖任务且通常事先未知，需人工网格搜索，成本高。

### 研究动机
把「多样性目标的选择」形式化为一个反馈优化问题，用经典反馈控制（极值搜索）实现无模型的在线自适应。

## 方法概述

### 核心思想
- 用 **System Neural Diversity（SND）** 度量行为多样性（聚合智能体策略两两差异）。
- 用 **DiCo** 把每个智能体策略表示为「共享分量 + 动态缩放的个体分量」，从而朝给定 SND 目标调控多样性。
- 用 **ESC** 对多样性目标施加小幅正弦扰动，观察任务表现变化以估计局部改进方向，进而自适应标称多样性设定点。

### ADiCo 双时间尺度框架
- **快时间尺度（DiCo）**：把系统多样性调控到当前目标值。
- **慢时间尺度（ESC）**：仅用平均 episodic 回报，无模型、无解析梯度地调整多样性目标。
- 兼容 on/off-policy actor-critic（IPPO、MADDPG）与确定性/随机连续动作策略。

![[task_environments_page1.png|600]]

> 图：评测所用的合作 MARL 任务环境示意

## 实验结果

### 主要结果
- 在 2 个合作 MARL 任务、6 种配置上，ADiCo 保持任务合适的行为多样性水平：同质策略有利时保持低多样性，异质策略需要时提升多样性。
- **对比基线**：匹配或超过 DiCo（最佳网格搜索目标）与 Unconstrained DiCo（不设目标、自由涌现）。

![[result_1_page1.png|600]]

> 图：ADiCo 与基线的性能对比（结果 1）

## 深度分析

### 亮点
1. **跨学科视角**：把经典反馈控制（极值搜索）引入 MARL 训练，用成熟控制理论解决多样性调参难题。
2. **无模型自适应**：ESC 只需平均回报，无需显式建模「多样性-性能」景观，通用性好。
3. **免网格搜索**：相比 DiCo 需人工网格搜索最优目标，ADiCo 在线自适应，降低调参成本。

### 局限
- 评测规模较小（2 任务 6 配置），ESC 收敛速度与在更大规模 MARL 上的表现待验证。
- 正弦扰动可能引入额外训练开销，对长 horizon 任务的稳定性需进一步分析。

### 对研究方向的启示
「把 MARL 的超参调优形式化为反馈控制问题」是一条有潜力的思路，可推广到学习率、温度、熵系数等其他超参的自适应。
