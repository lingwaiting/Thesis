---
date: "2026-09-02"
paper_id: "arXiv:2609.00237"
title: "Learning What to Retain: Gated-Memory Routing for Efficient Collaboration in Multi-Agent LLM Systems"
authors: "Rakibul Hasan Rajib, Mengxing Zheng, Qian Lou"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 多智能体
  - 记忆路由
  - 门控机制
  - 推理成本
quality_score: "8.2/10"
created: "2026-09-02"
updated: "2026-09-02"
status: analyzed
---

# Learning What to Retain: Gated-Memory Routing for Efficient Collaboration in Multi-Agent LLM Systems

## 核心信息
- **论文ID**：arXiv:2609.00237
- **作者**：Rakibul Hasan Rajib, Mengxing Zheng, Qian Lou
- **机构**：--
- **发布时间**：2026-08-31
- **会议/期刊**：arXiv 预印本（cs.AI / cs.CL）
- **链接**：[arXiv](http://arxiv.org/abs/2609.00237v1) | [PDF](https://arxiv.org/pdf/2609.00237v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language model (LLM)-based multi-agent systems tackle complex reasoning by orchestrating how multiple agents are configured and how they collaborate. A central challenge is to adapt orchestration to the evolving collaboration state. Routing from the query alone cannot adapt to intermediate progress or errors, which hurts accuracy. Routing from the complete execution history supplies this missing context, but forces later decisions to process every prior step, including redundant or low-utility ones. This creates an execution-history overload that inflates cost. Effective orchestration instead requires a compact state that captures useful progress without accumulating redundant context. We propose Gated-Memory Routing, which conditions each decision on the query and a learned execution memory. A learned Memory Write Gate commits only non-redundant reasoning steps, and a learned Retrieval Gate supplies each agent a compact, relevant subset, so every decision conditions on a clean, informative state. At each step, the system selects the next role and backbone from this memory, while an Adaptive Halting Controller stops execution once the memory contains sufficient evidence for answering. Across five reasoning and code-generation benchmarks, our framework is both effective and efficient: it attains the best average accuracy, exceeding the strongest baseline by 2.44 points, while reducing HumanEval inference cost by 31.9% relative to that baseline. Code is available at https://github.com/rajibrhasan/gated-memory-routing

### 中文翻译
基于大语言模型（LLM）的多智能体系统通过编排多个智能体的配置方式与协作方式来应对复杂推理。一个核心挑战在于：让编排适应不断演化的协作状态。仅从查询（query）出发的路由无法适应中间进展或错误，从而损害准确率；而从完整执行历史出发的路由虽然补上了这一缺失的上下文，却迫使后续决策处理每一个先前步骤，包括冗余或低价值的步骤，造成"执行历史过载"，抬高成本。有效的编排需要一个紧凑的状态：既能捕捉有用的进展，又不累积冗余上下文。我们提出 Gated-Memory Routing（门控记忆路由），让每个决策同时以查询和一个"学习到的执行记忆"为条件。一个学习到的**记忆写入门（Memory Write Gate）**只提交非冗余的推理步骤；一个学习到的**检索门（Retrieval Gate）**为每个智能体提供紧凑且相关的子集，使得每个决策都基于干净、信息丰富的状态。每一步，系统都从这一记忆中选出下一个角色与骨干模型；而一个**自适应停止控制器（Adaptive Halting Controller）**一旦判定记忆已包含足以作答的证据就停止执行。在五个推理与代码生成基准上，我们的框架兼顾有效与高效：取得最佳平均准确率，比最强基线高出 2.44 分，同时在 HumanEval 上将推理成本相对该基线降低 31.9%。

### 核心要点提炼
- **研究背景**：LLM 多智能体系统的编排需要适应不断演化的协作状态。
- **研究动机**：仅凭查询路由无法适应中间进展，凭完整历史又造成执行历史过载、成本膨胀。
- **核心方法**：门控记忆路由——用学习到的写入门/检索门构建紧凑执行记忆，配合自适应停止。
- **主要结果**：五个基准最佳平均准确率（超最强基线 2.44 分），HumanEval 推理成本降 31.9%。
- **研究意义**：在准确率与成本之间取得双赢，为多智能体编排提供高效状态表征。

## 研究背景与动机

### 领域现状
LLM 多智能体系统通过编排多个智能体的角色与协作来应对复杂推理与代码生成任务。编排决策（选谁、用哪个骨干、何时停）的质量直接决定系统表现。

### 现有方法的局限性
- **仅凭查询路由**：看不到中间进展与错误，无法动态适应，准确率受损。
- **凭完整历史路由**：上下文冗余累积，后续决策被迫处理大量低价值步骤，成本膨胀（执行历史过载）。
- **缺乏紧凑状态**：缺少一种既能捕捉进展又不累积冗余的中间表征。

### 研究动机
需要一个**紧凑的执行状态**，让每个编排决策都基于干净、信息丰富的上下文，从而兼顾准确率与成本。

## 研究问题

### 核心研究问题
如何为 LLM 多智能体系统学习一个紧凑的"执行记忆"，使角色选择、骨干选择与停止判断都能在低冗余、低成本的前提下做出高质量决策？

## 方法概述

### 核心思想
Gated-Memory Routing 用**学习到的执行记忆**取代"完整执行历史"作为决策条件：写入门只保留非冗余步骤，检索门按需提供紧凑子集，停止控制器在证据充分时提前终止。

### 方法框架

#### 整体架构
![[gated_memory_routing_page1.png|800]]

> 图1：Gated-Memory Routing 框架。查询与学习到的执行记忆共同决定每个步骤的角色/骨干选择，写入门与检索门控制记忆的写入与读取。

#### 各模块详细说明

**模块1：记忆写入门（Memory Write Gate）**
- **功能**：判断每个推理步骤是否"非冗余"，只将有用步骤提交到执行记忆。
- **输出**：去冗余后的紧凑记忆。
- **关键技术**：学习到的写入门控。

**模块2：检索门（Retrieval Gate）**
- **功能**：为每个智能体从记忆中检索紧凑且相关的子集。
- **输出**：每个决策可用的干净、信息丰富的状态。
- **关键技术**：学习到的检索门控。

**模块3：决策路由（Role & Backbone Selection）**
- **功能**：基于查询与执行记忆，选择下一步的角色与骨干模型。
- **关键技术**：以记忆为条件的策略。

**模块4：自适应停止控制器（Adaptive Halting Controller）**
- **功能**：判断记忆是否已包含足以作答的证据，是则提前停止执行。
- **关键技术**：基于证据充分性的停止判断。

### 方法架构图
Gated-Memory Routing 的核心架构见 `gated_memory_routing_page1.png`（图1）：写入门/检索门维护一个紧凑执行记忆，角色与骨干选择、停止判断都以此记忆为条件，形成"记忆-决策-停止"闭环。

## 实验结果

### 实验目标
验证 Gated-Memory Routing 能否在多个推理与代码生成基准上同时提升准确率并降低推理成本。

### 数据集

| 数据集 | 类型 | 说明 |
|--------|------|------|
| 五个推理/代码生成基准 | 综合 | 覆盖推理与代码生成任务 |
| HumanEval | 代码生成 | 用于成本评测 |

### 实验设置

#### 基线方法
- 最强基线（best strongest baseline）作为对照。
- 完整执行历史路由、仅查询路由等作为消融/对照。

#### 评估指标
- 平均准确率（accuracy）。
- 推理成本（inference cost，以 HumanEval 为例）。

### 主要结果

| 指标 | 结果 |
|------|------|
| 平均准确率（5 基准） | 最佳，超最强基线 +2.44 分 |
| HumanEval 推理成本 | 相对最强基线降低 31.9% |

#### 结果分析
Gated-Memory Routing 在取得最佳平均准确率的同时显著降低了推理成本，说明紧凑执行记忆不仅能保持信息完整性（准确率不降反升），还能有效削减冗余上下文带来的成本，实现准确率与效率的双赢。

### 实验结果图
论文配图 `pareto_gsmhard.pdf`、`pareto_humaneval.pdf`（准确率-成本 Pareto 曲线）、`depth_ablation.pdf`（深度消融）、`framework_v2.pdf`、`full_history_routing.pdf` 等覆盖框架对比、Pareto 前沿与消融分析，详见 `images/index.md`。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出"执行记忆"作为多智能体编排的紧凑中间表征，显式区分"有用进展"与"冗余上下文"。
- 用学习到的写入门/检索门替代启发式记忆管理，是可学习记忆在多智能体路由上的有效应用。

#### 实际应用价值
- 可直接降低多智能体系统的推理成本（-31.9%），同时不牺牲甚至提升准确率。
- 对长程推理、代码生成等成本敏感场景具有直接工程价值。

#### 领域影响
- 短期：为多智能体编排提供一个高效、可训练的状态管理组件。
- 长期：推动多智能体系统从"堆历史"走向"学记忆"，向更可扩展的协作范式演进。

### 方法优势详解
- **双赢**：准确率与成本同时改善，而非此消彼长。
- **可学习**：写入门/检索门可端到端训练，适应性优于人工规则。
- **自适应停止**：证据充分即停，进一步节约成本。

### 局限性分析
- **基准覆盖**：五个基准规模有限，更广泛任务上的稳健性待验证。
- **训练成本**：门控机制本身需要额外训练信号。
- **记忆语义**：记忆的语义可解释性、跨任务迁移能力尚未深入探讨。

## 技术路线定位

### 所属技术路线
本文属于"LLM 多智能体编排 + 记忆管理"路线，核心创新在于用可学习的门控记忆取代完整历史作为路由条件。

### 技术路线发展历程
```
固定编排 → 查询路由 → 完整历史路由 → 紧凑记忆路由（本文）→ 可学习记忆编排
```

### 本文在技术路线中的位置
- **承上**：解决"仅查询路由"与"完整历史路由"各自的缺陷。
- **启下**：为多智能体的记忆学习、自适应停止等方向提供了具体实现。

## 未来工作建议
1. **更大规模基准**：在更多样任务与更长程场景上验证。
2. **记忆迁移**：研究执行记忆能否跨任务复用。
3. **可解释性**：提升记忆内容的语义可解释性与可审计性。

## 我的综合评价

### 价值评分

#### 总体评分
**8.2/10** — 问题定位精准、方法简洁有效，在准确率与成本上同时取得提升，具有明确的工程与理论价值。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 可学习门控记忆用于多智能体路由，切入点清晰 |
| 技术质量 | 8/10 | 写入门/检索门/停止控制器设计完整 |
| 实验充分性 | 8/10 | 五基准 + 成本评测 + Pareto/消融 |
| 写作质量 | 8/10 | 问题与动机表述清晰 |
| 实用性 | 9/10 | 降本增效，工程价值直接 |

## 相关论文

### 直接相关
- [[20_Research/Papers/大语言模型/Learning_What_to_Retain_Gated-Memory_Routing_for_Efficient_Collaboration_in_Multi-Agent_LLM_Systems|Learning What to Retain: Gated-Memory Routing]] - 本文

### 背景相关
- 多智能体编排框架 - 本文改进的对象
- LLM 记忆机制 / RAG - 记忆与检索相关方向
- 自适应计算 / 提前退出 - 自适应停止相关方向

> [!tip] 关键启示
> 多智能体系统的瓶颈往往不在"能不能算"，而在"给决策喂了什么上下文"；用可学习的门控记忆替代冗长的执行历史，是同时提升准确率与降低成本的有效杠杆。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 值得一读：适合关注多智能体编排、LLM 记忆机制与推理成本优化的读者。
