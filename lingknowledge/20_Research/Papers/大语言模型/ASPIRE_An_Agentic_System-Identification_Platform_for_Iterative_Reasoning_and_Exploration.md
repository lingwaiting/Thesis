---
date: "2026-11-11"
paper_id: "arXiv:2611.13114"
title: "ASPIRE: An Agentic System-Identification Platform for Iterative Reasoning and Exploration"
authors: "Erik Derner, Jiří Kubalík, Robert Babuška"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM-Agent
  - System-Identification
  - Equation-Discovery
  - Scientific-Agent
  - 系统辨识
quality_score: "8.0/10"
created: "2026-11-11"
updated: "2026-11-11"
status: analyzed
---

# ASPIRE: An Agentic System-Identification Platform for Iterative Reasoning and Exploration

## 核心信息
- **论文ID**：arXiv:2611.13114
- **作者**：Erik Derner, Jiří Kubalík, Robert Babuška
- **机构**：--
- **发布时间**：2026-11-09
- **类别**：cs.RO, cs.AI, cs.CL, cs.LG, eess.SY
- **链接**：[arXiv](https://arxiv.org/abs/2611.13114) | [PDF](https://arxiv.org/pdf/2611.13114)

## 摘要翻译

### 英文摘要
System identification requires expert decisions on the identification method, incorporation of prior physical knowledge, and model validation. We propose ASPIRE, a framework for nonlinear system identification in which a large language model agent selects and executes the identification method. ASPIRE constructs closed-form, nonlinear static or dynamic models from measured data, optionally supplemented by a system description, the intended use of the model, and physical requirements expressed formally or in natural language. Each model includes its parameters, an estimation record, an interpretation of its terms, and an error evaluated on test data inaccessible to the agent. Formal physical requirements are verified independently.

### 中文翻译
系统辨识需要专家对辨识方法、先验物理知识的融入以及模型验证作出决策。本文提出 ASPIRE，一个非线性系统辨识框架，由大语言模型智能体选择并执行辨识方法。ASPIRE 从测量数据中构建闭式、非线性的静态或动态模型，可选地辅以系统描述、模型预期用途以及以形式化或自然语言表达的物理需求。每个模型都包含参数、估计记录、术语解释，以及在智能体不可见的测试数据上评估的误差。形式化物理需求会被独立验证。

### 核心要点提炼
- **研究背景**：系统辨识依赖专家经验，难以自动化。
- **研究动机**：把 LLM 智能体引入系统辨识，统一「方法选择、先验融入、验证」全流程。
- **核心方法**：LLM 智能体迭代选择/执行辨识方法，构建闭式非线性模型，形式化物理需求独立验证。
- **主要结果**：3 个实测系统 30 次运行全部产出有效模型，单次成本 $0.96–3.38；150 项评估中 132 项完全满足。
- **研究意义**：为科学发现/工程建模提供可解释、可验证的智能体化系统辨识范式。

## 研究背景与动机

### 领域现状
系统辨识（System Identification）是从测量数据中建立动力学模型的过程，广泛应用于控制、机器人、生物过程等。它要求专家在辨识方法选择、先验物理知识融入、模型验证等环节做出判断，门槛高、可重复性差。近年来 LLM 智能体与方程发现（equation discovery）的结合开始涌现（如 SR-Scientist、KeplerAgent 等）。

### 现有方法的局限性
- 传统方法（如 PySINDy、PySR）需人工指定候选基函数与先验。
- 已有智能体式方程发现系统在「物理约束的独立验证」「可解释性」「与人类交互」上仍不足。
- 缺乏统一的评估约定（evaluation conventions）。

### 研究动机
作者希望构建一个端到端的智能体平台，让 LLM 智能体自主完成「选方法 → 构建候选模型 → 参数估计 → 独立验证 → 可交互迭代」的完整闭环，并产出**可解释的闭式模型**。

## 研究问题

### 核心研究问题
如何让 LLM 智能体在非线性系统辨识中自主选择与执行辨识方法，并在引入形式化物理需求的前提下，产出**可解释、可独立验证**的闭式模型？

## 方法概述

### 核心思想
ASPIRE 把系统辨识形式化为一个智能体循环：harness（脚手架）负责执行、验证与记录，agent（LLM）负责推理、选择方法与解读结果。二者配合，把专家决策自动化，同时用「形式化物理需求的独立验证」保证可靠性。

### 方法框架

#### 整体架构

![[aspire-overview_page1.png|700]]

> 图1：ASPIRE 概览——由 harness（灰色）与 agent（蓝色）组成，实线箭头/方框恒存在，虚线为可选（任务描述、物理需求等）。

关键组件：

#### 各模块详细说明

**harness（脚手架）**
- **功能**：执行辨识、评估候选模型、独立验证物理需求、记录模型记录（model record）
- **产出**：每个模型含参数、估计记录、术语解释、测试误差

**agent（LLM 智能体）**
- **功能**：选择辨识方法、解读结果、决定是否继续迭代、必要时向用户提问

**task specification（任务描述）**
- **功能**：系统描述、预期用途、物理需求（形式化或自然语言）

**Evaluation（评估）**
- **功能**：在智能体不可见的测试数据上评估误差，形式化物理需求独立验证

### 方法架构图

![[report-run.png|700]]

> 图2：浏览器界面展示的一次运行报告——候选池、参数表、模型记录。

## 实验结果

### 数据集（三个实测系统）
- 移动机器人（mobile robot）
- 干摩擦旋转系统（rotational motion under dry friction）
- 实验室发酵罐（pressure dynamics of a fermenter）

### 主要结果

- **有效性**：30 次运行（3 系统 × 有无描述 × 5 次）全部产出有效模型。
- **成本**：单次运行 $0.96–3.38。
- **可解释性评估**：30 个推荐模型中，150 项评估条目有 132 项完全满足、18 项部分满足；最常见偏差（9 个模型）是模型结构选择的描述不准确/缺乏支撑。

![[test-turtlebot_page1.png|700]]

> 图3：移动机器人（TurtleBot）实测数据与推荐模型的拟合。

![[test-friction-driven_page1.png|700]]

> 图4：干摩擦旋转系统的实测与仿真输出对比。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出「形式化物理需求独立验证」这一可靠性机制，把先验知识从「软约束」升级为「硬校验」。
- 建立统一的智能体系统辨识评估约定。

#### 实际应用价值
- 降低系统辨识的专家门槛，使工程师/科学家能快速获得可解释闭式模型。
- 成本极低（单次 < $4），具备规模化应用潜力。

### 方法优势详解

- **可解释性**：闭式模型 + 术语解释 + 估计记录，模型可审计。
- **可靠性**：形式化物理需求独立验证，测试误差在智能体不可见数据上评估。
- **可交互**：支持自主与交互两种模式，可向用户提问、接受进一步分析。

### 局限性分析

- 模型结构选择描述存在不准确/无支撑的情况（9/30 模型）。
- 物理需求的形式化仍需人工撰写。
- 实验规模有限（3 个系统），复杂多物理场系统有待验证。

## 技术路线定位

本文属于**LLM 智能体驱动的科学发现 / 方程发现（equation discovery）**路线，聚焦系统辨识这一垂直场景，强调「可解释 + 可独立验证」。

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 智能体化系统辨识的扎实落地，独立验证与可解释性是亮点。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 智能体系统辨识非全新，独立验证机制有增量 |
| 技术质量 | 8/10 | 形式化验证 + 评估约定严谨 |
| 实验充分性 | 7/10 | 3 个真实系统 + 30 次运行，规模适中 |
| 写作质量 | 8/10 | 结构完整，评估透明 |
| 实用性 | 8/10 | 低门槛、低成本、可解释，实用价值高 |

> [!tip] 关键启示
> 让 LLM 智能体做科学建模的关键不是「会算」，而是「可解释 + 可独立验证」——形式化物理约束的硬校验把 LLM 的直觉变成了可信的工程产出。

> [!warning] 注意事项
> - 模型结构选择的文字描述可能不准确，需人工复核。
> - 物理需求需专家形式化，智能体本身不产生先验。
