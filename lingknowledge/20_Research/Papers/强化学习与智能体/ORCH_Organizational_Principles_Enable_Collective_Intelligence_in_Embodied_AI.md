---
date: "2026-09-12"
paper_id: "arXiv:2609.11737"
title: "ORCH: Organizational Principles Enable Collective Intelligence in Embodied AI"
authors: "Zhengran Ji, Jonathan Hyun, Boyuan Chen"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Multi-Agent
  - Embodied-AI
  - Collective-Intelligence
  - Organization-Theory
  - LLM-Agent
quality_score: "8.5/10"
created: "2026-09-12"
updated: "2026-09-12"
status: analyzed
---

# ORCH: Organizational Principles Enable Collective Intelligence in Embodied AI

## 核心信息
- **论文ID**：arXiv:2609.11737
- **作者**：Zhengran Ji, Jonathan Hyun, Boyuan Chen
- **机构**：--
- **发布时间**：2026-09-10
- **会议/期刊**：cs.MA / cs.AI / cs.LG / cs.RO（多智能体 + 具身 AI）
- **链接**：[arXiv](http://arxiv.org/abs/2609.11737) | [PDF](https://arxiv.org/pdf/2609.11737v1)
- **推荐评分**：10.53 / 10（相关性 4.6、新近性 3.0、热门度 2.0、质量 1.2）

## 摘要翻译

### 英文摘要
Collective intelligence depends not only on the capabilities of individual members, but also on how those members are organized. Yet artificial multi-agent systems are typically assembled using fixed organizational structures, even when the physical tasks they perform impose fundamentally different coordination requirements. Here we show that principles from human organization theory can be operationalized to organize large, heterogeneous collectives of embodied artificial agents. We introduce ORCH (Organizing Roles and Coordination Hierarchies), which constructs task-specific hierarchical organizations by combining pooled interdependence for work that can proceed concurrently with sequential interdependence for work governed by prerequisite relationships. Across 25 wildfire-response missions spanning reconnaissance, rescue, transportation, resource management, containment and suppression, we evaluated teams of up to 50 heterogeneous agents using eight large language models. Organizations constructed using these principles consistently outperformed four representative embodied multi-agent approaches across mission outcome, execution efficiency, exploration and computational resource use. Human-designed ORCH organizations improved final score by 63.97% and execution efficiency by 74.29% on average relative to the four prior frameworks. Organizations generated automatically by language models improved these measures by 43.63% and 52.53%, respectively.

### 中文翻译
集体智能不仅取决于个体成员的能力，还取决于这些成员是如何被组织起来的。然而，人工多智能体系统通常采用固定的组织结构来组装，即便它们执行的物理任务对协调提出了根本不同的要求。本文展示了人类组织理论中的原则可以被可操作化，用于组织大规模、异构的具身人工智能体群体。作者提出 ORCH（Organizing Roles and Coordination Hierarchies），通过将「可并行工作的联合互依（pooled interdependence）」与「受前置关系约束的序列互依（sequential interdependence）」相结合，构建任务特定的层级化组织。在涵盖侦察、救援、运输、资源管理、遏制与扑灭的 25 个山火响应任务中，作者评估了多达 50 个异构智能体、使用 8 种大语言模型的团队。基于这些原则构建的组织在任务结果、执行效率、探索和计算资源使用上，一致地优于四种代表性的具身多智能体方法。相比四种先前框架，人工设计的 ORCH 组织平均将最终得分提升 63.97%、执行效率提升 74.29%；由语言模型自动生成的组织则将这两项指标分别提升 43.63% 和 52.53%。

### 核心要点提炼
- **研究背景**：具身多智能体系统普遍采用固定组织结构，忽视了不同物理任务对协调方式的差异化需求。
- **研究动机**：如何将人类组织理论（联合/序列互依）落地为可计算的智能体组织方法。
- **核心方法**：ORCH 框架，按任务构造层级化角色与协调结构，混合联合互依与序列互依。
- **主要结果**：在 25 个山火任务中，ORCH 相比 4 种基线，人工设计组织提升最终得分 63.97%、效率 74.29%；LLM 自动生成组织提升 43.63% / 52.53%。
- **研究意义**：证明「如何组织」与「个体能力」同等重要，且集体表现不随模型规模单调增长。

## 研究背景与动机

### 领域现状
多智能体系统（Multi-Agent Systems）与 LLM-based Agent 研究近年快速发展，但绝大多数工作聚焦于「让单个智能体更强」，或在固定拓扑（如全连接、星型、流水线）下组装智能体。组织结构的确定通常是经验性的、静态的，而非由任务推导而来。

### 现有方法的局限性
- **固定结构**：无论任务是否可并行、是否存在依赖，都套用同一套通信/协作拓扑。
- **忽视任务结构**：物理任务（如山火救援）天然包含可并行的侦察与存在先后依赖的运输/扑灭，固定结构无法适配。
- **缺乏组织视角**：人类组织理论（如 pooled/sequential/reciprocal interdependence）在人工智能体系统中少有可操作化的落地。

### 研究动机
作者提出核心洞察：集体智能的瓶颈往往不在个体能力，而在「组织方式」。若能将组织理论的抽象原则转化为可计算的角色分配与层级构造，就能在不改变底层模型的前提下显著提升群体性能。

## 研究问题

**核心研究问题**：能否把人类组织理论中的「互依关系」原则可操作化，用于为异构具身智能体群体自动构建任务特定的层级组织，从而系统性地提升集体智能？

## 方法概述

### 核心思想
ORCH 的核心是「任务决定组织」：识别任务中哪些子任务可以**并发**执行（联合互依，pooled interdependence），哪些存在**前置依赖**必须**顺序**执行（序列互依，sequential interdependence），据此生成角色（Role）与协调层级（Coordination Hierarchy）。

![[2609.11737_Teaser_Figure_p1.png|600]]

> 图1：ORCH 方法概览（Teaser Figure）。从左到右展示了从任务分解到层级组织构建、再到多智能体执行与评估的完整流程。

### 方法框架

#### 整体架构
1. **任务分解**：将复杂具身任务（如山火响应）拆解为子任务，识别它们之间的依赖关系。
2. **互依分类**：将子任务关系划分为联合互依（可并行）与序列互依（有前置约束）。
3. **层级构造**：依据分类构建分层组织——同层级内用联合互依并行推进，跨层级用序列互依衔接阶段。
4. **角色分配**：把不同角色分配给异构智能体，形成专门的子群体（specialized groups）。
5. **执行与协调**：组织内的智能体在各自角色下行动，同时在阶段转换时按层级协调。

![[2609.11737_Team_Hierarchy_Generation_p1.png|600]]

> 图2：团队层级生成示意，展示如何从任务依赖关系生成角色与协调层级。

#### 各模块详细说明

**模块1：任务依赖建模**
- **功能**：分析任务图，标注子任务间的并发/依赖关系。
- **输出**：带互依标签的任务依赖图。

**模块2：组织结构构造**
- **功能**：将任务依赖图映射为分层组织。
- **关键技术**：混合 pooled（并行）与 sequential（顺序）互依；可由人设计，也可由 LLM 自动生成组织。

**模块3：异构智能体执行**
- **功能**：将角色分配到异构智能体（最多 50 个、8 种 LLM）。
- **关键技术**：专门的子群体在组织内保持并发活动，跨阶段有序衔接。

### 关键结果
- 相比四种基线（4 个代表性具身多智能体框架），ORCH 在**任务结果、执行效率、探索、计算资源**四个维度全面领先。
- 人工设计组织：最终得分 +63.97%，执行效率 +74.29%。
- LLM 自动生成组织：+43.63% / +52.53%。
- 优势跨任务、跨底层模型稳定存在；**集体表现不随模型规模单调增长**（更大的模型未必带来更好团队表现）。

## 实验与结果

### 实验设置
- **任务**：25 个山火响应任务，覆盖侦察、救援、运输、资源管理、遏制、扑灭六类。
- **规模**：最多 50 个异构智能体。
- **模型**：8 种大语言模型。
- **基线**：4 种代表性具身多智能体方法。

### 结果概览
![[2609.11737_Aggregated_Result_by_Algorithm_p1.png|600]]

> 图3：按算法聚合的结果对比，ORCH 组织在各项指标上优于基线。

![[2609.11737_Aggregated_Result_by_Model_p1.png|600]]

> 图4：按底层模型聚合的结果，显示 ORCH 的优势跨模型稳定。

### 消融与分析
![[2609.11737_Abalation_and_analysis_p1.png|600]]

> 图5：消融与长时程任务分析，显示层级组织使团队在专门子群体内保持并发活动，同时协调阶段有序转换。

## 深度分析

### 研究价值
- **理论贡献**：首次系统地把组织理论（互依关系）可操作化到具身多智能体系统，提供了「组织即算法」的新视角。
- **实践价值**：在不更换底层模型的前提下，仅通过组织结构设计即可显著提升群体性能，成本低、可迁移。
- **反直觉发现**：集体表现不随模型规模单调增长，提示「组织」是独立于「模型能力」的关键杠杆。

### 局限性
- 组织构造在部分场景仍需人工设计（LLM 自动生成仍弱于人工设计）。
- 任务类型限于山火响应这一具身场景，向其他领域的泛化尚待验证。
- 大规模团队（>50）的可扩展性与通信开销未充分评估。

### 未来工作
- 强化 LLM 自动生成组织的能力，缩小与人工设计的差距。
- 在更广泛的任务类别（制造、物流、灾后重建等）上验证组织原则的通用性。
- 探索组织结构的在线自适应演化（任务执行中动态调整层级）。

### 相关论文对比
- 相较于 [[PlannerForge_LLM_Agents_for_Scenario-Based_Testing_of_Motion_Planners_in_Autonomous_Driving|PlannerForge]] 等聚焦单任务规划的智能体工作，ORCH 的独特之处在于以「组织层级」而非「个体策略」为核心改进维度。
- 与 [[Procedural_Graphs_Self-Evolving_Execution_Structures_for_LLM_Agents|Procedural Graphs]] 的自演化执行结构形成互补：后者强调过程结构的自演化，ORCH 强调组织互依关系的静态构造。
