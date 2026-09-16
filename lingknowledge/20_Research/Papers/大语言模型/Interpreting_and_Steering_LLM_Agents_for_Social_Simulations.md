---
date: "2026-09-16"
paper_id: "arXiv:2609.16436"
title: "Interpreting and Steering LLM Agents for Social Simulations"
authors: "Jiayue Gaveal Fan, Arul Murugan, Shreyas Krishnan, Abhishek Nagaraj"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM智能体
  - 可解释性
  - 稀疏自编码器
  - 社会仿真
  - 行为干预
quality_score: "8.2/10"
created: "2026-09-16"
updated: "2026-09-16"
status: analyzed
---

# Interpreting and Steering LLM Agents for Social Simulations

## 核心信息
- **论文ID**：arXiv:2609.16436
- **作者**：Jiayue Gaveal Fan, Arul Murugan, Shreyas Krishnan, Abhishek Nagaraj
- **机构**：--
- **发布时间**：2026-09-14
- **会议/期刊**：cs.LG / cs.AI / cs.CL
- **链接**：[arXiv](https://arxiv.org/abs/2609.16436) | [PDF](https://arxiv.org/pdf/2609.16436)

## 摘要翻译

### 英文摘要
Simulations based on large language models (LLMs) have proven to be powerful for understanding human behavior, making them valuable additions to the social scientific toolkit. However, LLMs are ultimately black boxes based on deep neural networks which limits their value for social science. This is because of a lack of (i) interpretability: i.e. the ability to assign clear mechanisms driving observed behavior; and a lack of (ii) steerability: i.e. the ability to mute or amplify specific theoretically meaningful mechanisms of action to drive specific model behavior. Here, we demonstrate how the black box could be opened up to further enrich LLM-based simulations. Specifically, we compare three types of methods: (1) prompt-based manipulation, (2) SAE-derived feature steering, and (3) probe-based direction steering and examine their utility for LLM-based social scientific simulations. We do so by interpreting and steering two foundational components of human behaviors, namely preferences (risk attitudes, altruism) and capabilities (divergent creativity, product innovation), operationalized using four classic economic and creative tasks implemented as natural-language interactions. Overall, our results show that SAE- and probe-based techniques often outperform basic prompt-based methods for steering LLM agents, although this advantage depends on the specific prompting strategy involved. Together, SAEs and probes constitute an effective pipeline for social scientists seeking to interpret and steer agents in social simulations: SAEs decompose agents' internal representations into human-readable features, after which probes can reliably shift agents' behaviors in specified directions.

### 中文翻译
基于大语言模型（LLM）的仿真已被证明在理解人类行为方面十分强大，成为社会科学工具箱的重要补充。然而，LLM 本质上是基于深度神经网络的黑箱，这限制了其在社会科学中的价值：一是缺乏**可解释性**（无法为观察到的行为指定清晰的驱动机制），二是缺乏**可操控性**（无法削弱或放大特定具有理论意义的机制来驱动特定行为）。作者展示了如何打开黑箱以丰富 LLM 仿真，具体比较了三类方法：（1）基于提示的操控，（2）SAE 导出的特征操控，（3）基于探针的方向操控，并考察它们在 LLM 社会科学仿真中的效用。作者通过四个经典经济学与创造力任务（以自然语言交互实现），对两个人类行为的基础成分——偏好（风险态度、利他主义）与能力（发散创造力、产品创新）进行解释与操控。总体而言，SAE 与探针方法在操控 LLM 智能体时往往优于基础提示方法，尽管这一优势取决于具体的提示策略。SAE 与探针共同构成了一条面向社会科学家的有效流水线：SAE 将智能体的内部表征分解为人类可读的特征，随后探针能够可靠地将智能体行为向指定方向偏移。

### 核心要点提炼
- **研究背景**：LLM 仿真在社会科学中日益流行，但黑箱特性阻碍其价值。
- **研究动机**：社会科学需要「可解释性」（机制归因）与「可操控性」（机制干预）。
- **核心方法**：对比提示操控 / SAE 特征操控 / 探针方向操控三类方法。
- **主要结果**：SAE + 探针方法常优于提示方法；二者构成「解释 → 操控」流水线。
- **研究意义**：为社会科学仿真提供了打开 LLM 黑箱的通用方法学。

## 研究背景与动机

### 领域现状
LLM 智能体在社会仿真中被广泛用于模拟人类行为（风险偏好、利他、创造力、创新等），为经济学、社会学、心理学提供了可控的「虚拟被试」。

### 现有方法的局限性
1. **不可解释**：LLM 是黑箱，无法为观察到的行为指定清晰机制。
2. **不可操控**：难以在理论上精确地削弱/放大某机制，只能靠粗粒度的提示改写。
3. **提示方法的局限**：提示操控效果不稳定、依赖措辞、缺乏对内部表征的直接干预。

### 研究动机
社会科学仿真既需要「读懂」智能体（解释行为机制），也需要「干预」智能体（操控行为方向）。需要一套超越提示工程的方法。

## 研究问题

### 核心研究问题
如何对用于社会仿真的 LLM 智能体同时实现**可解释性**（机制归因）与**可操控性**（机制干预）？

## 方法概述

### 核心思想
引入机械可解释性（mechanistic interpretability）工具——稀疏自编码器（SAE）与线性探针（probe）——将 LLM 智能体的内部表征「打开」，先解释后操控。

### 方法框架

#### 整体架构
比较三类方法：
1. **提示操控（prompt-based manipulation）**：直接改写提示词干预行为。
2. **SAE 特征操控（SAE-derived feature steering）**：用稀疏自编码器将内部激活分解为可读特征，再操控特定特征。
3. **探针方向操控（probe-based direction steering）**：训练线性探针定位某行为方向，再沿该方向偏移激活。

![[sae.jpg|800]]

> 图1：SAE 方法示意——将智能体内部表征分解为人类可读的稀疏特征。

![[probe.jpg|800]]

> 图2：探针方法示意——沿特定行为方向操控智能体激活。

#### 各模块详细说明

**模块1：行为任务设计**
- **功能**：用四个经典任务（风险态度、利他主义、发散创造力、产品创新）以自然语言交互实现。
- **输出**：可量化的行为指标。

**模块2：SAE 特征分解（解释）**
- **功能**：把智能体内部表征分解为人类可读特征，实现机制归因。
- **关键技术**：稀疏自编码器（Sparse Autoencoder）。

**模块3：探针方向操控（干预）**
- **功能**：训练线性探针定位行为方向，并沿该方向偏移激活，实现精确行为操控。
- **关键技术**：direction steering。

### 方法架构图

![[figure5.png|800]]

> 图3：三类方法的整体流程与对比。

## 实验结果

### 实验目标
比较三类方法（提示 / SAE / 探针）在解释与操控智能体行为上的效用。

### 主要结果

#### 主实验结果
- SAE 与探针方法在操控智能体行为上**往往优于**基础提示方法。
- 优势大小取决于具体提示策略。

#### 结果分析
- SAE 负责「解释」：将内部表征分解为可读特征。
- 探针负责「操控」：沿特征方向可靠偏移行为。
- 二者结合构成「解释 → 操控」的完整流水线，为社会科学家提供了比提示工程更精确、更可控的干预手段。

### 实验结果图

![[dose_response_lambda.png|800]]

> 图4：剂量-响应曲线——随操控强度（λ）变化的行为偏移。

## 深度分析

### 研究价值评估

#### 理论贡献
- 将机械可解释性（SAE/探针）引入社会科学仿真，桥接「LLM 可解释性」与「社会科学方法学」两个领域。
- 提出「解释 → 操控」流水线，为 LLM 仿真提供严谨的机制归因与因果干预手段。

#### 实际应用价值
- **社会仿真**：更可信、更可控的虚拟被试。
- **行为科学**：精确干预特定行为机制。
- **政策模拟**：评估政策干预的机制效应。

### 方法优势详解

#### 优势1：机制级干预
- **描述**：探针方向操控可在表征层面精确偏移行为，优于粗粒度的提示改写。
- **实验验证**：SAE/探针常优于提示方法。

#### 优势2：可解释与可操控的统一
- **描述**：SAE（解释）+ 探针（操控）构成闭环，契合社会科学的因果推断需求。

### 局限性分析

#### 局限1：提示策略依赖
- **描述**：SAE/探针优势取决于具体提示策略，说明提示仍是不可忽视的基线。

#### 局限2：任务覆盖
- **描述**：四个任务覆盖偏好与能力，但人类行为的其他维度（情感、规范、社会偏好）待扩展。

## 我的综合评价

### 价值评分

#### 总体评分
**8.2/10** - 将机械可解释性引入社会科学仿真是有前景的交叉方向，方法学清晰、实证扎实。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 交叉领域（可解释性 × 社会科学）新结合 |
| 技术质量 | 8/10 | SAE/探针方法运用规范 |
| 实验充分性 | 7/10 | 四任务实证，但依赖提示策略 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 8/10 | 社会仿真/行为科学场景明确 |

## 相关论文

### 直接相关
- [[Interpreting_and_Steering_LLM_Agents_for_Social_Simulations|本文]]

> [!tip] 关键启示
> 打开 LLM 黑箱的方法学价值不只在「可解释性研究」本身，更在于赋能下游科学——让社会科学家能像做因果实验一样，解释并操控智能体的行为机制。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 对 LLM 可解释性、社会仿真、计算社会科学感兴趣的读者推荐阅读。
