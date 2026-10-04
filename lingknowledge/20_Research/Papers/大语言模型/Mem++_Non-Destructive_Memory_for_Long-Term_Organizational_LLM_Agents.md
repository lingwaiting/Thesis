---
date: "2026-10-04"
paper_id: "arXiv:2610.02002"
title: "Mem++: Non-Destructive Memory for Long-Term Organizational LLM Agents"
authors: "Ahmad Yehia, Aly O. Abdelkareem, Islam Ahmed, Hesham Omran, Khaled Alashmouny, Christian Claudel, Abduallah Mohamed"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM-Agent
  - 记忆系统
  - RAG
  - 组织知识
quality_score: "8.5/10"
related_papers: []
created: "2026-10-04"
updated: "2026-10-04"
status: analyzed
---

# Mem++: Non-Destructive Memory for Long-Term Organizational LLM Agents

## 核心信息
- **论文ID**：arXiv:2610.02002
- **作者**：Ahmad Yehia, Aly O. Abdelkareem, Islam Ahmed, Hesham Omran, Khaled Alashmouny, Christian Claudel, Abduallah Mohamed
- **机构**：--
- **发布时间**：2026-10-01
- **会议/期刊**：分类 cs.CL, cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2610.02002) | [PDF](https://arxiv.org/pdf/2610.02002)

## 摘要翻译

### 英文摘要
Large Language Model (LLM) agents now take part in organizational work, where many authors record decisions across documents over months. Because a revised decision arrives as a new document rather than an edit, answering a question requires knowing which version held at a given time. However, most memory systems compress the record at write time. By distilling each document into facts, notes or graph edges, these methods fix what can be answered before any question is asked. To address this, we propose Mem++, a non-destructive memory framework shifting from write-time distillation to read-time selection.

### 中文翻译
大语言模型（LLM）Agent 正逐步参与组织化工作，多名作者会在数月间通过文档记录决策。由于修订后的决策是以"新文档"而非"编辑"的形式到来，要回答一个问题，就必须知道在某一时间点上哪个版本生效。然而，大多数记忆系统在**写入时**就对记录进行压缩——把每份文档蒸馏为事实、笔记或图谱边。这样做在问题提出之前就固定了"能回答什么"。为此，本文提出 Mem++，一个**非破坏性**记忆框架，把范式从"写入时蒸馏"转向"读取时选择"。

### 核心要点提炼
- **研究背景**：LLM Agent 参与组织化工作，决策以不断新增的文档版本形式记录，回答需知道"某时刻哪个版本生效"。
- **研究动机**：现有记忆系统在写入时压缩记录，提前固化了可回答的内容，无法回溯历史版本。
- **核心方法**：Mem++ —— 非破坏性记忆，写入时完整存储每份文档（含日期与作者），读取时按问题时间点检索并融合词汇与语义排序。
- **主要结果**：在 OrgMemBench 上超最强记忆基线 8.0–13.1 分；gpt-4.1-mini 下总分最高，超 RAG 2.6 分。
- **研究意义**：提出"读取时选择而非写入时蒸馏"的记忆新范式，保留历史版本并把选择权交还给回答模型。

## 研究背景与动机

### 领域现状
- LLM Agent 被用于组织工作，多名作者跨数月通过文档记录决策。
- 决策修订以**新文档**形式追加，因此"某一时刻哪个版本生效"成为关键问题。

### 现有方法的局限性
- 大多数记忆系统（事实抽取、笔记、图谱边）在**写入时压缩**记录。
- 写入时蒸馏会**提前固定**可回答的内容，丢失历史版本信息，无法回答"当时"的问题。

### 研究动机
能否设计一种在写入时不破坏原始记录、把"选哪个版本"推迟到读取时的记忆框架？

## 研究问题

### 核心研究问题
如何构建一种面向组织的长期记忆系统，使其能够回答"某一时间点上哪个文档版本生效"，而不在写入时就丢失历史信息？

## 方法概述

### 核心思想
Mem++ 的核心是"**非破坏性**"：写入时不调用任何生成模型，只完整存储每份文档（连同日期与作者）；读取时，仅检索"截至问题所问时间点"的文档，并融合词汇排序与语义排序。与覆盖旧版本的系统不同，Mem++ 保留所有版本，把"选哪个"的决策交给回答模型。

### 方法框架

#### 整体架构
![[2610.02002_page1.png|600]]

> 图1：Mem++ 整体示意 —— 写入时全量存储、读取时按时间点检索。

![[mempp_architecture.png|600]]

> 图2：Mem++ 架构图，对比"写入时蒸馏"与"读取时选择"两条路径。

- **非破坏性写入**：完整存储每份文档（含日期、作者），写入时不调用生成模型。
- **读取时选择**：按问题时间点检索"截至该时间"的文档，融合词汇与语义双排序。
- **版本保留**：不覆盖旧版本，将版本选择权交还回答模型。

### 关键创新
1. **范式转变** —— 从"写入时蒸馏"到"读取时选择"，保留原始记录与历史版本。
2. **时间感知检索** —— 只检索问题所问时间点之前的文档，支持时间一致性问答。
3. **非破坏性设计** —— 写入零生成成本，读时融合词汇+语义排序。

## 实验结果

### 数据集
- **组织基准**：OrgMemBench（两个回答模型）
- **长程对话/记忆**：LoCoMo、LongMemEval-S

### 主要结果
- OrgMemBench：Mem++ 超最强记忆系统基线 8.0–13.1 分；gpt-4.1-mini 下总分最高，超 RAG 2.6 分。
- LoCoMo：平均 LLM-judge 分数最高；LongMemEval-S：排名第二（仅次其自身 entity-graph 变体）。

## 深度分析

### 研究价值
- **理论贡献**：点明"写入时压缩会提前固定可回答内容"这一被忽视的问题，提出非破坏性记忆范式。
- **实际应用**：适合组织化、多作者、跨时间的知识管理场景（决策记录、制度演进的问答）。
- **领域影响**：为 Agent 长期记忆提供了一条与主流"蒸馏式"记忆互补的路径。

### 优势
- 写入零生成成本，扩展性好。
- 保留版本历史，支持时间一致性问答。
- 在组织基准上显著超越最强基线。

### 局限性
- 全量存储带来更大的存储与检索开销，长文档规模下的检索效率需进一步优化。
- 检索质量依赖词汇/语义排序的融合策略，缺乏对多跳推理式问题的显式建模。

### 未来工作
- 优化大规模文档的检索效率（如分层索引、向量压缩）。
- 结合 entity-graph 变体，探索非破坏性与结构化蒸馏的混合方案。
