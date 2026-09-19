---
date: "2026-09-19"
paper_id: "arXiv:2609.20804"
title: "An Empirical Study of Harness Design for Coding Agents"
authors: "Run-Ze Fan, Zihao Zhang, Simin Ma, Yebowen Hu, Shouju Wang, Kaiqiang Song, Fei Liu, Hamed Zamani, Xiaoyang Wang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Coding-Agent
  - Software-Engineering
  - Context-Management
  - Agent-Harness
  - SWE-Bench
quality_score: "8.8/10"
created: "2026-09-19"
updated: "2026-09-19"
status: analyzed
---

# An Empirical Study of Harness Design for Coding Agents

## 核心信息
- **论文ID**：arXiv:2609.20804
- **作者**：Run-Ze Fan, Zihao Zhang, Simin Ma, Yebowen Hu, Shouju Wang, Kaiqiang Song, Fei Liu, Hamed Zamani, Xiaoyang Wang
- **机构**：UMass Amherst、Zoom、Emory University、UNC Charlotte
- **发布时间**：2026-09-17
- **会议/期刊**：投稿中
- **链接**：[arXiv](https://arxiv.org/abs/2609.20804) | [PDF](https://arxiv.org/pdf/2609.20804)
- **领域**：大语言模型 · 编码智能体 · 软件工程

## 摘要翻译

### 英文摘要
Coding harnesses shape how autonomous coding agents translate model capabilities into long-horizon software-engineering performance, yet existing work typically evaluates harnesses as monolithic systems, leaving the effectiveness of individual components unclear. To enable component-level comparisons, we study this question with a lightweight coding harness whose execution loop is fixed while three components are varied: planning, action space, and context management. Across four models evaluated on SWE-Bench Verified and Terminal-Bench 2.1, we evaluate 176 matched settings spanning five context-management strategies, four context-window budgets, and targeted ablations of planning and action space. We find that: (1) Context management becomes increasingly valuable as the context-window budget tightens, with most of its benefit coming from preventing context-overflow failures. (2) Staging rule-based elision before LLM-based summarization provides the strongest overall efficiency among the context-management strategies, whereas making elided content recoverable adds machinery that models rarely use and yields no accuracy gain. (3) Planning shifts from an accuracy scaffold for weaker models to a cost saver for stronger models, with little change in accuracy. (4) Predefined tools improve performance for models with weaker bash proficiency, whereas bash-capable models can operate effectively with a bash-only interface and achieve substantially lower cost, especially on command-line-centric tasks. Trajectory-level analysis explains these effects: context management extends execution trajectories without substantially altering agent behavior, planning changes where trajectories stop, and the action space changes the granularity at which code is written.

### 中文翻译
编码 harness 决定了自主编码智能体如何把模型能力转化为长程软件工程表现，但现有工作通常把 harness 当作一个整体系统来评估，各组件各自的效能并不清晰。为了支持组件级对比，作者用一个小型编码 harness 开展研究：执行循环保持固定，只变化三个组件——规划（planning）、动作空间（action space）与上下文管理（context management）。在四个模型上、于 SWE-Bench Verified 与 Terminal-Bench 2.1 两个基准评估 176 组匹配设置，覆盖五种上下文管理策略、四档上下文窗口预算，以及规划与动作空间的定向消融。研究得出四个发现：(1) 随着上下文窗口预算收紧，上下文管理越来越重要，其收益主要来自防止上下文溢出失败；(2) 在上下文管理策略中，"先用规则化省略（elision）、再用 LLM 摘要"的分级方案整体效率最强，而让被省略内容可恢复所增加的机制很少被模型使用、且无精度增益；(3) 规划的角色从较弱模型的"精度脚手架"转变为较强模型的"成本节省器"，精度几乎不变；(4) 预定义工具能提升 bash 能力较弱模型的表现，而 bash 能力强的模型用纯 bash 接口即可高效工作、成本大幅降低，尤其是在命令行中心任务上。轨迹级分析解释了这些效应：上下文管理延长执行轨迹但不大改智能体行为，规划改变轨迹在何处终止，动作空间改变代码编写的粒度。

### 核心要点提炼
- **研究背景**：编码 harness 是编码智能体的关键工程层，但此前被当作黑盒整体评估。
- **研究动机**：缺少组件级的因果归因，无法为模型与预算感知的 harness 设计提供依据。
- **核心方法**：固定执行循环，逐一变化规划/动作空间/上下文管理三组件，做 176 组匹配消融。
- **主要结果**：四项发现揭示三组件的分工——上下文管理防溢出、规划决定停在哪、动作空间决定写多细。
- **研究意义**：为"模型 + 预算感知"的 harness 设计提供可操作的组件级结论。

## 研究背景与动机

### 领域现状
编码智能体（如 SWE-agent、OpenHands）通过 harness 编排模型，把单次生成能力转化为可迭代的长程软件工程表现。harness 通常包含规划、动作空间、上下文管理、工具定义等多个组件。然而，绝大多数工作把 harness 作为一个整体评测，组件各自贡献被掩盖。

### 现有方法的局限性
1. **整体评估无法归因**：不知道是规划、动作空间还是上下文管理带来了提升。
2. **组件设计缺乏依据**：上下文管理该用摘要还是省略？规划该不该开？工具该用预定义还是纯 bash？缺乏组件级实验证据。
3. **预算维度缺失**：上下文窗口预算对组件选择的影响未被系统研究。

### 研究动机
作者希望拆解 harness，用受控实验回答"每个组件分别带来什么"，从而指导模型与预算感知的 harness 设计。

## 研究问题

### 核心研究问题
在固定执行循环的前提下，规划、动作空间、上下文管理三个组件各自如何影响编码智能体的准确率、成本与行为模式？这些影响如何随上下文窗口预算变化？

## 方法概述

### 核心思想
构建一个轻量、可拆解的编码 harness，把"执行循环"固定为常量，把规划、动作空间、上下文管理作为可切换变量，通过 176 组匹配设置做组件级因果对比。

### 方法框架

#### 整体架构
![[fig_harness_overview.png|800]]

> 图1：harness 概览。执行循环固定，三个可变组件（规划 / 动作空间 / 上下文管理）作为研究对象被逐一变化。

#### 各模块详细说明

**模块1：Planning（规划）**
- **功能**：在行动前生成计划，指导后续动作。
- **变量**：开启 / 关闭规划。
- **发现**：对弱模型是"精度脚手架"（提升准确率），对强模型是"成本节省器"（精度几乎不变但更省）。

**模块2：Action space（动作空间）**
- **功能**：定义智能体可执行的动作集合。
- **变量**：预定义结构化工具（read/write/edit） vs 纯 bash 接口。
- **发现**：预定义工具帮助 bash 弱的模型；bash 强的模型用纯 bash 接口即可，成本大幅降低。

**模块3：Context management（上下文管理）**
- **功能**：控制哪些历史信息进入上下文窗口。
- **变量**：五种策略（含规则化省略 elision、LLM 摘要、以及二者分级组合），四档窗口预算。
- **发现**：窗口越紧越重要，收益主要来自防止上下文溢出失败；"先省略后摘要"分级方案效率最强；可恢复机制无用。

### 方法架构图
![[fig_harness_overview.png|800]]

> 图1（同）：harness 设计概览与三组件分解。

## 实验结果

### 实验目标
量化规划、动作空间、上下文管理对准确率、成本与轨迹行为的独立贡献，及其对上下文窗口预算的敏感性。

### 数据集与基线
- **数据集**：SWE-Bench Verified、Terminal-Bench 2.1。
- **模型**：四个模型（覆盖不同能力层级）。
- **设置规模**：176 组匹配设置，五种上下文策略 × 四档窗口预算 + 规划/动作空间消融。

### 主要结果

#### 主实验发现

| # | 组件 | 核心发现 |
|---|------|----------|
| 1 | 上下文管理 | 窗口越紧越重要，收益主要来自防溢出 |
| 2 | 上下文策略 | "规则省略 → LLM 摘要"分级最强；可恢复机制无用 |
| 3 | 规划 | 弱模型→精度脚手架；强模型→成本节省器 |
| 4 | 动作空间 | 预定义工具帮弱模型；强模型纯 bash 更省 |

#### 结果分析
- **上下文管理**：延长执行轨迹但不改变行为本质，主要通过避免上下文溢出维持可用性。
- **规划**：改变轨迹在何处终止（影响停止时机）。
- **动作空间**：改变代码编写的粒度。

### 消融实验
- 上下文管理策略对比（五种策略 × 四档预算）→ 分级"省略 + 摘要"整体效率最优。
- 规划开关消融 → 精度影响小、成本影响大。
- 动作空间消融 → 取决于模型 bash 熟练度。

### 实验结果图
![[fig_planning_effect.png|800]]

> 图2：规划对准确率与成本的影响随模型能力的变化。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：组件级归因范式**——把 harness 从黑盒整体拆解为可独立对比的组件，建立编码智能体工程层的因果分析框架。
- **贡献2：预算感知结论**——揭示组件选择随上下文窗口预算的系统性变化。
- **贡献3：轨迹级机制解释**——用轨迹分析解释"上下文管理延长轨迹、规划决定终止、动作空间决定粒度"。

#### 实际应用价值
- **应用场景1：harness 选型**——根据模型 bash 能力与窗口预算选择工具接口与上下文策略。
- **应用场景2：成本优化**——强模型关规划、用纯 bash，可大幅降低成本且精度不降。

#### 领域影响
- **短期**：为编码智能体 harness 配置提供直接可用的设计指南。
- **中期**：推动从"整体评测"转向"组件级评测"的评测范式。
- **长期**：模块化 harness 评测框架可复用于其他智能体任务。

### 方法优势详解
1. **受控性**：固定执行循环，实现单变量因果归因。
2. **规模**：176 组匹配设置，结论统计可靠。
3. **可解释性**：轨迹级分析揭示行为机制，而非仅报分数。

### 局限性分析
1. **单 harness 架构**：结论可能受特定执行循环设计影响。
2. **模型覆盖有限**：四个模型的结论外推需谨慎。
3. **任务范围**：SWE-Bench 与 Terminal-Bench 偏代码修复/命令行，其他任务未覆盖。

### 适用性与场景分析
- **适用**：编码智能体 harness 选型与成本优化、模型-预算感知的系统设计。
- **不适用**：非编码类智能体、需要完全不同动作空间的任务。

## 我的综合评价

### 价值评分

#### 总体评分
**8.8/10** — 实证扎实、结论可操作，是编码智能体工程层稀缺的组件级研究，为 harness 设计提供了少有的因果证据。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 组件级拆解 + 预算感知视角新颖，但方法论偏实证 |
| 技术质量 | 9/10 | 176 组匹配设置、受控消融严谨 |
| 实验充分性 | 9/10 | 双基准、四模型、多维度消融 |
| 写作质量 | 9/10 | 发现逐条清晰，轨迹分析到位 |
| 实用性 | 9/10 | 结论可直接指导 harness 选型 |

### 重点关注
- **值得关注的技术点**：分级"省略 + 摘要"策略为何效率最强；可恢复机制为何无用。
- **需要深入理解的部分**：轨迹级分析如何把宏观指标差异归因到具体行为。

## 相关论文

### 直接相关
- SWE-agent、OpenHands 等编码智能体 harness
- 长上下文管理与 RAG 的上下文工程

### 后续工作
- 跨 harness 架构的组件级评测泛化

> [!tip] 关键启示
> harness 不是黑盒——拆开规划、动作空间、上下文管理，才能做出模型与预算感知的工程决策。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐——做编码智能体工程的同学必读，组件级结论可直接落地。
