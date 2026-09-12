---
date: "2026-09-12"
paper_id: "arXiv:2609.11390"
title: "VikingRAG: Accurate and Token-efficient Retrieval-augmented Generation over Structured Documents"
authors: "Peiyuan Gao, Gaoyuan Zhang, Haojie Qin, Yahui Sun, Qianyi Zhang, Yunhao Zhang, Zeyu Wang, Wei Lu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - RAG
  - Retrieval-Augmented-Generation
  - Structured-Documents
  - Token-Efficiency
  - Semantic-Data-Management
quality_score: "8.0/10"
created: "2026-09-12"
updated: "2026-09-12"
status: analyzed
---

# VikingRAG: Accurate and Token-efficient Retrieval-augmented Generation over Structured Documents

## 核心信息
- **论文ID**：arXiv:2609.11390
- **作者**：Peiyuan Gao, Gaoyuan Zhang, Haojie Qin, Yahui Sun, Qianyi Zhang, Yunhao Zhang, Zeyu Wang, Wei Lu
- **机构**：--
- **发布时间**：2026-09-10
- **会议/期刊**：cs.IR / cs.AI / cs.CL / cs.DB / cs.LG
- **链接**：[arXiv](http://arxiv.org/abs/2609.11390) | [PDF](https://arxiv.org/pdf/2609.11390v1)
- **推荐评分**：9.83 / 10（相关性 4.0、新近性 3.0、热门度 2.0、质量 1.5）

## 摘要翻译

### 英文摘要
State-of-the-art retrieval-augmented generation (RAG) methods exploit document structures to acquire sufficient evidence, but often incur substantial token costs. To reduce structural-context tokens without compromising high RAG accuracy, we present VikingRAG, a directory-aware semantic data management system that tightly integrates semantic and structural access to support structural-context-efficient, evidence-gap-driven multi-round retrieval. To further reduce token overhead of multi-round interaction, we materialize agentic multi-round retrieval traces as experience edges, and reuse these edges for similar queries, avoiding repeated multi-round exploration. To additionally reduce token costs when agentic multi-round retrieval is unnecessary, we introduce an adaptive escalation strategy that answers from one-round experience-augmented retrieval when the evidence is sufficient, and invokes agentic multi-round retrieval only otherwise. Experiments on real datasets show that the base system VikingRAG matches high accuracy of state-of-the-art methods while consuming only 11.6%--51.9% of their tokens. With retrieval-trace reuse and adaptive escalation, token costs drop to 5.1%--32.5% while maintaining competitive accuracy and practical document-storage performance, showing the utility of this work for emerging AI knowledge bases.

### 中文翻译
最先进的检索增强生成（RAG）方法利用文档结构来获取充分证据，但往往带来巨大的 token 成本。为在不牺牲高 RAG 准确率的前提下减少结构上下文 token，作者提出 VikingRAG，一个目录感知的语义数据管理系统，紧密整合语义访问与结构访问，以支持结构上下文高效、证据缺口驱动的多轮检索。为进一步降低多轮交互的 token 开销，作者将智能体多轮检索轨迹物化为「经验边（experience edges）」，并在相似查询中复用这些边，避免重复的多轮探索。为在多轮检索不必要时进一步降低 token 成本，作者引入自适应升级策略：当单轮经验增强检索证据充分时直接回答，仅在必要时调用智能体多轮检索。真实数据集上的实验表明，基础系统 VikingRAG 达到与 SOTA 方法相当的准确率，但仅消耗其 11.6%--51.9% 的 token；叠加轨迹复用与自适应升级后，token 成本降至 5.1%--32.5%，同时保持有竞争力的准确率与实用的文档存储性能，展示了该方法在新兴 AI 知识库中的实用价值。

### 核心要点提炼
- **研究背景**：结构化文档 RAG 精度高但 token 开销大。
- **研究动机**：在保持准确率的同时大幅降低结构上下文 token 与多轮交互成本。
- **核心方法**：目录感知语义数据管理 + 经验边复用 + 自适应升级（单轮/多轮）。
- **主要结果**：基础系统 token 仅 11.6%--51.9%；叠加优化后降至 5.1%--32.5%，准确率有竞争力。
- **研究意义**：面向 AI 知识库的 token 高效结构化 RAG 系统，兼具准确率、成本与存储性能。

## 研究背景与动机

### 领域现状
结构化文档（树形目录、表格、跨章节引用）承载大量企业知识。前沿 RAG 方法利用文档结构（如递归摘要、树索引）获取充分证据，显著提升回答质量，但代价是极高的 token 消耗——既要注入结构上下文，又要多轮探索定位证据。

### 现有方法的局限性
- **结构上下文昂贵**：把目录/层级结构整体注入会消耗大量 token。
- **多轮探索冗余**：智能体式多轮检索每次查询都重新探索，相同/相似查询重复劳动。
- **一刀切策略**：无论问题难易都走完整多轮流程，简单问题也付出高成本。

### 研究动机
作者的目标是「三者兼得」：高准确率、低 token、可实用的文档存储性能。核心思路是把「语义访问」与「结构访问」紧密整合，并用「经验复用 + 自适应升级」削减多轮交互的冗余成本。

## 研究问题

**核心研究问题**：如何在结构化文档上，以显著更低的 token 成本，达到与 SOTA 相当的 RAG 准确率？

## 方法概述

### 核心思想
VikingRAG 通过「目录感知的语义数据管理」在结构与语义间建立统一访问层；用「经验边」把多轮检索轨迹沉淀为可复用的结构，避免重复探索；用「自适应升级」让简单查询走单轮、复杂查询才走多轮，按需分配 token。

![[2609.11390_1.png|600]]

> 图1：VikingRAG 系统架构，展示语义访问与结构访问的整合、经验边复用与自适应升级机制。

### 方法框架

#### 整体架构
1. **目录感知语义管理**：统一组织文档的语义内容与目录结构，支持结构上下文高效检索。
2. **证据缺口驱动多轮检索**：按「证据缺口」定位不足处，进行针对性多轮补证。
3. **经验边物化**：将多轮检索轨迹固化为图上的边，相似查询直接复用。
4. **自适应升级**：单轮经验增强检索证据充足即作答，否则升级到智能体多轮检索。

#### 各模块详细说明

**模块1：目录感知语义数据管理系统**
- **功能**：整合语义与结构访问，降低结构上下文 token。
- **效果**：基础系统以 11.6%--51.9% 的 token 达到 SOTA 相当准确率。

**模块2：经验边（Experience Edges）**
- **功能**：物化智能体多轮检索轨迹为可复用边。
- **效果**：相似查询免重复探索，显著降低多轮开销。

**模块3：自适应升级（Adaptive Escalation）**
- **功能**：证据充分时单轮回答，不足时才升级多轮。
- **效果**：叠加后 token 降至 5.1%--32.5%，且保持准确率。

### 关键结果
- 基础系统：准确率匹配 SOTA，token 仅 11.6%--51.9%。
- 叠加复用 + 升级：token 5.1%--32.5%，准确率有竞争力。
- 文档存储性能实用，适合 AI 知识库场景。

## 实验与结果

### 实验设置
- **数据**：真实结构化文档数据集。
- **基线**：利用文档结构的 SOTA RAG 方法。
- **指标**：准确率、token 成本、文档存储性能。

### 结果概览
![[2609.11390_tree_document_qa_method.png|600]]

> 图2：树形文档 QA 方法对比，展示 VikingRAG 在准确率与 token 成本间的权衡优势。

![[2609.11390_retrieval_performance_deeepseekv4.png|600]]

> 图3：检索性能对比，展示经验边复用与自适应升级对 token 成本的削减。

## 深度分析

### 研究价值
- **系统贡献**：首个将「语义 + 结构」统一管理并针对 token 成本系统优化的结构化 RAG 系统。
- **工程洞察**：经验边复用与自适应升级是「按需分配计算」的有效范式，可迁移到其他智能体检索系统。
- **落地价值**：面向企业知识库/文档存储场景，准确率、成本、存储三者平衡。

### 局限性
- 依赖真实数据集的特定结构，对高度非结构化文档的泛化待验证。
- 「经验边」随规模增长可能引入存储/维护开销，长期去重与失效机制未充分讨论。
- 未在大规模在线部署场景下评估端到端延迟。

### 未来工作
- 扩展到更多样化的文档结构与多模态知识库。
- 研究经验边的生命周期管理与失效检测。
- 端到端在线系统的延迟与吞吐优化。

### 相关论文对比
- 相比 [[LiteRAG_Cost-Efficient_Graph-Based_Retrieval-Augmented_Generation|LiteRAG]] 的图式低成本 RAG，VikingRAG 更侧重「结构化文档 + 目录感知」与「多轮轨迹复用」，两者在低成本 RAG 路线上互为参照。
