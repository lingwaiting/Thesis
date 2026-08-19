---
date: "2026-08-19"
paper_id: "arXiv:2608.17209"
title: "Teach and Grow: An Agent-Centered Architecture for General Robot Learning"
authors: "Chang Nie, Zhe Liu, Hesheng Wang"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 机器人学习
  - VLA
  - LLM-Agent
  - 技能库
  - Teach-and-Grow
quality_score: "8.5/10"
created: "2026-08-19"
updated: "2026-08-19"
status: analyzed
---

# Teach and Grow: An Agent-Centered Architecture for General Robot Learning

## 核心信息
- **论文ID**：arXiv:2608.17209
- **作者**：Chang Nie, Zhe Liu, Hesheng Wang
- **机构**：上海交通大学（SJTU，作者所在单位）
- **发布时间**：2026-08-17
- **分类**：cs.RO / cs.AI / cs.CV / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2608.17209) | [PDF](https://arxiv.org/pdf/2608.17209)

## 摘要翻译

### 英文摘要
End-to-end vision-language-action (VLA) and world-action models offer an elegant route to general-purpose robotics, but their reliability is bounded by validated physical coverage. When an unfamiliar object, sensor, embodiment, or contact falls outside that coverage and no validated fallback exists, correcting the failure requires new robot data, a policy update, and regression testing — the "retraining tax." The paper presents Teach-and-Grow Learning (TGL), an agent-centered architecture in which a multimodal agent turns a few successful demonstrations into reusable Skill Blocks, grounds and composes them in new scenes, observes physical outcomes, and revises routes when execution departs from intent. A Skill Library stores executable behaviors, while structured Experience Memory carries forward success, failure, and repair, so new tasks are acquired without task-specific policy retraining. LIBERO evaluation attains SOTA; controlled studies expose skill induction, persistent reuse, and agent-directed adaptation; and a "Teach-and-Grow scaling-law hypothesis" proposes that future-task error and teaching demand approach irreducible floors as power laws in reusable experience.

### 中文翻译
端到端的视觉-语言-动作（VLA）与世界-动作模型为通用机器人提供了一条优雅的路线，但其可靠性受限于已验证的物理覆盖范围。当陌生物体、传感器、本体或接触超出该覆盖范围且没有经过验证的兜底策略时，纠正失败就需要新的机器人数据、策略更新和回归测试——即"重训练税"。本文提出 **Teach-and-Grow Learning（TGL）**，一种以智能体为中心的架构：多模态智能体将少量成功演示转化为可复用的 **Skill Blocks**（面向有意义子目标的闭环行为），在新场景中对其 grounding 与组合，观察物理结果，并在执行偏离意图时修正路线。**Skill Library** 存储可执行行为，结构化的 **Experience Memory** 传承成功、失败与修复经验，从而无需针对特定任务重训练策略即可习得新任务。LIBERO 评估取得 SOTA；对照实验揭示了技能归纳、持续复用与智能体导向的自适应；最后提出"Teach-and-Grow 缩放律假说"——若 X 表示有效的可复用经验，未来任务的误差与教学需求应随 X 以幂律逼近不可约下限。

### 核心要点提炼
- **研究背景**：通用机器人 VLA 模型的可靠性受限于"已验证物理覆盖"，超出覆盖就需昂贵的重训练。
- **研究动机**：把部署期从"一次性策略执行"转变为"持续学习期"，让上一个任务使下一个任务更简单。
- **核心方法**：以智能体为中心，将演示归纳为可复用 Skill Blocks，通过 Skill Library + Experience Memory 实现组合、复用与修复。
- **主要结果**：LIBERO 上取得 SOTA，且无需任务级策略重训练。
- **研究意义**：提出"部署即持续学习"的范式与缩放律假说，重新定义机器人经验累积。

## 研究背景与动机

### 领域现状
端到端 VLA（如 RT-2、OpenVLA、π0 等）和世界-动作模型把感知、语言理解和动作生成统一进单一模型，显著提升了多任务泛化能力。但这类模型本质上是"离线、静态"的策略，其可依赖的边界由训练/验证时见过的物理状态分布决定。

### 现有方法的局限性
- **重训练税（retraining tax）**：一旦遇到分布外物体、传感器、本体或接触方式，修复需要采集新数据 → 更新策略 → 回归测试，且具身数据往往需要真实机器操作才能产生，代价远高于文本数据。
- **静态策略**：传统 VLA 缺少在部署中"边用边学"的机制，经验无法沉淀为可复用的能力。

### 研究动机
作者主张把部署期当作持续学习期：让智能体把少量演示变成可组合、可复用的技能，把成功/失败/修复都记入经验，从而让"上一个任务使下一个更容易"。

## 研究问题

**核心研究问题**：如何构建一种以智能体为中心的机器人学习架构，使得新任务能够通过"复用 + 组合 + 自适应修正"习得，而无需针对每个任务重训练策略？

## 方法概述

### 核心思想
把机器人学习从"训练一个泛化策略"转向"训练一个会学习、会复用、会自我修正的智能体"。TGL 的关键在于**将演示抽象为可执行、可组合的技能块**，并**显式维护经验记忆**。

### 方法框架

#### 整体架构
TGL 包含四大组件：
1. **多模态智能体（Multimodal Agent）**：把少量成功演示转化为 Skill Blocks。
2. **Skill Blocks**：面向有意义子目标的闭环行为。
3. **Skill Library**：存储可执行行为，支持后续 grounding 与组合。
4. **Experience Memory**：结构化记录成功、失败与修复，供未来任务参考。

在新场景中，智能体 grounding 并组合这些技能块，选择已学习的或几何工具，观察物理结果，当执行偏离意图时修正路线。

![[fig1.png|600]]

> 图1：Teach-and-Grow Learning 的整体架构示意（来自论文 figures/fig1.png）。

## 实验结果

### 主要结果
- **LIBERO 评估**：达到 state-of-the-art 性能。
- **对照实验**：验证了技能归纳（skill induction）、持久复用（persistent reuse）与智能体导向的自适应（agent-directed adaptation）三项能力。

### 缩放律假说
- 若 X 表示有效可复用经验量，未来任务误差与教学需求应随 X 以幂律逼近不可约下限（irreducible floors）。

## 深度分析

### 创新点
1. **范式转移**：把"训练策略"升级为"训练会学习的智能体"，将部署定义为持续学习阶段。
2. **可复用技能抽象**：Skill Blocks + Skill Library 让经验显式沉淀，而非隐含在策略权重中。
3. **结构化经验记忆**：同时记录成功、失败与修复，区别于单纯的演示回放。
4. **可检验的缩放律假说**：为"经验累积的规模效应"给出可证伪的预测。

### 局限性
- 摘要未给出 Skill Block 归纳的自动化程度与失败时的人工介入成本。
- 主要在 LIBERO 这一仿真/受控环境验证，真实机器上的泛化与安全性尚未充分说明。
- 缩放律假说目前是理论假设，需要更大规模实验支撑。

### 未来工作
- 在更多真实机器人平台验证。
- 实证检验 Teach-and-Grow 缩放律。
- 探索 Skill Block 的跨本体、跨任务迁移。

### 与相关工作对比
- 相对端到端 VLA（RT-2、OpenVLA）等"静态策略"，TGL 补充了部署期持续学习与经验复用。
- 相对传统分层/符号技能库方法，TGL 用多模态智能体实现更柔性的 grounding 与组合。
- 属于"agentic robot learning / lifelong robot learning"路线。
