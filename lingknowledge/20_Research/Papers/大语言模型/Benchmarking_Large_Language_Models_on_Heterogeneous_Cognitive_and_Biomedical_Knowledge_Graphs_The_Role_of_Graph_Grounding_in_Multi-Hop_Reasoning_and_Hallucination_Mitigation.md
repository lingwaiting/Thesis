---
date: "2026-11-07"
paper_id: "arXiv:2611.08315"
title: "Benchmarking Large Language Models on Heterogeneous Cognitive and Biomedical Knowledge Graphs: The Role of Graph Grounding in Multi-Hop Reasoning and Hallucination Mitigation"
authors: "Ali Sarabadani, Kheirolah Rahsepar Fard, Tahereh Jeirani"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 知识图谱
  - 图接地
  - 多跳推理
  - 幻觉缓解
quality_score: "7.8/10"
related_papers: []
created: "2026-11-07"
updated: "2026-11-07"
status: analyzed
---

# Benchmarking Large Language Models on Heterogeneous Cognitive and Biomedical Knowledge Graphs: The Role of Graph Grounding in Multi-Hop Reasoning and Hallucination Mitigation

## 核心信息
- **论文ID**：arXiv:2611.08315
- **作者**：Ali Sarabadani, Kheirolah Rahsepar Fard, Tahereh Jeirani
- **机构**：--
- **发布时间**：2026-11-05
- **会议/期刊**：arXiv 预印本（cs.AI / cs.CL / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.08315) | [PDF](https://arxiv.org/pdf/2611.08315)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models (LLMs) are increasingly used in biomedical and cognitive neuroscience, but their ability to reason reliably over heterogeneous knowledge graphs (KGs) remains unclear. We benchmark five LLMs (GPT-5.1, Gemini 2.0 Pro, Llama 3, Qwen, and BioGPT) across five KGs: DrugBank, NeuroSynth, Cognitive Atlas, Brain Map, and GDPKG. We introduce a multitier query dataset to evaluate reasoning depth and factual accuracy under KG-aware (graph-grounded) and KG-agnostic conditions.

### 中文翻译
大语言模型（LLM）正越来越多地被用于生物医学和认知神经科学领域，但它们能否在异构知识图谱（KG）上进行可靠推理仍不明确。本文对五个 LLM（GPT-5.1、Gemini 2.0 Pro、Llama 3、Qwen 和 BioGPT）在五个知识图谱（DrugBank、NeuroSynth、Cognitive Atlas、Brain Map 和 GDPKG）上进行了基准测试，并引入了一个多层查询数据集，用于在「图感知（图接地）」与「图无关」两种条件下评估推理深度与事实准确性。

### 核心要点提炼
- **研究背景**：LLM 日益应用于生物医学与认知神经科学，但其在异构知识图谱上的推理可靠性缺乏系统评估。
- **研究动机**：图结构化知识是否能真正降低 LLM 幻觉、提升多跳推理，缺乏定量证据。
- **核心方法**：构建多层查询数据集，在 KG-aware / KG-agnostic 两种条件下对比五类 LLM。
- **主要结果**：通用大模型（GPT-5.1、Gemini 2.0 Pro）总体优于领域专用模型；图接地普遍降低幻觉率。
- **研究意义**：为「LLM + 结构化领域知识」的集成策略提供实证指导。

## 研究背景与动机

### 领域现状
大语言模型在医疗问答、药物发现、认知诊断等场景被寄予厚望，但 LLM 的「黑箱」特性使其在多跳推理和事实一致性上存在风险。知识图谱作为一种结构化的领域知识表示，被认为是缓解幻觉、增强可解释性的重要手段。

### 现有方法的局限性
- 已有评测多集中于单一 KG 或单一模型，缺乏跨异构 KG 的系统对比。
- 图接地（graph grounding）究竟在多跳推理中起多大作用，缺乏受控实验支撑。
- 「领域专用模型优于通用模型」这一直觉假设未被严格检验。

### 研究动机
需要一个跨模型、跨知识图谱、跨任务难度的统一基准，以回答：图接地能否稳定降低幻觉？通用模型与领域专用模型在结构化知识上的真实差距如何？

## 研究问题

### 核心研究问题
LLM 在异构认知与生物医学知识图谱上的多跳推理能力与幻觉行为，是否因「图接地」而得到一致改善？通用大模型是否普遍优于领域专用模型？

## 方法概述

### 核心思想
把知识图谱作为 LLM 推理的「接地」上下文，通过受控的多层查询（multi-tier queries）分离「推理深度」与「事实准确性」两个维度，从而量化图接地的净收益。

### 方法框架

#### 整体架构

![[2611.08315_fig1.png|800]]

> 图1：论文的评测框架示意——将五类 LLM 置于五个异构知识图谱之上，通过多层查询数据集在 KG-aware / KG-agnostic 两种条件下评估推理深度与事实准确性。

#### 各模块详细说明

**模块1：异构知识图谱集合**
- **功能**：提供跨领域的结构化知识源
- **输入**：DrugBank、NeuroSynth、Cognitive Atlas、Brain Map、GDPKG 五个 KG
- **输出**：用于图接地的子图上下文
- **关键点**：五个 KG 在规模、schema、领域上高度异构，检验方法泛化性

**模块2：多层查询数据集**
- **功能**：评估推理深度与事实准确性的分级基准
- **处理流程**：
  1. 设计从浅层到深层的多跳查询
  2. 区分 KG-aware（提供子图）与 KG-agnostic（无图）条件
  3. 标注参考答案并计算一致性

**模块3：五类 LLM 评测**
- **功能**：跨模型对比
- **关键点**：覆盖通用大模型（GPT-5.1、Gemini 2.0 Pro、Llama 3、Qwen）与领域专用模型（BioGPT）

## 实验结果

### 主要结果
- **通用模型占优**：GPT-5.1 与 Gemini 2.0 Pro 在多跳和高层推理任务上总体优于领域专用模型，但性能随查询复杂度上升而下降。
- **图接地降低幻觉**：在所有评测模型上，KG grounding 均降低了幻觉率。
- **标注一致性**：Cohen's kappa 达 0.781（范围 0.753–0.807），Fleiss' kappa 为 0.77，标注质量substantial。
- **反直觉发现**：与初始假设相反，领域专用模型在无图接地时并未一致优于通用模型。

### 结果分析
论文的核心价值在于用受控实验推翻了「领域专用模型天然更适合专业任务」的直觉，同时确认了图接地在幻觉缓解上的稳健收益。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提供了一个跨模型、跨 KG、跨难度的统一评测框架，填补了异构 KG 推理系统评测的空白。
- 实证区分了「推理深度」与「事实准确性」，并将两者与图接地关联。

#### 实际应用价值
- 为个性化医疗、认知障碍诊断、可解释 AI 中的 LLM-KG 集成策略提供选型指导。
- 论文讨论了神经符号混合架构（hybrid neurosymbolic architectures）的落地路径。

### 方法优势详解
- **受控设计**：KG-aware / KG-agnostic 的对照消融清晰分离了图接地的贡献。
- **多维标注**：多标注者 + kappa 系数保证了评测可靠性。

### 局限性分析
- **结论可推广性**：仅覆盖生物医学/认知领域，向其他领域的泛化未知。
- **模型版本**：涉及 GPT-5.1、Gemini 2.0 Pro 等闭源模型，复现受限。
- **图接地方式**：仅验证了「子图作为上下文」这一种接地策略，其他融合方式（如 GNN 嵌入、检索式）未覆盖。

## 与相关论文对比

本文属于「LLM × 知识图谱」评测路线，与 GraphRAG、KG-enhanced LLM 推理等工作互补：后者侧重方法设计，本文侧重系统性评测与幻觉归因。

## 技术路线定位

### 所属技术路线
属于「知识图谱增强的大语言模型推理」路线，核心特点是：用结构化知识缓解幻觉、提升多跳推理与可解释性。本文的独特定位在于**评测侧**，而非方法侧。

## 我的综合评价

### 价值评分

#### 总体评分
**7.8/10** - 一个设计扎实的评测基准，反直觉结论有启发意义，但方法与模型版本的复现性受限。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 统一评测框架 + 反直觉发现，但非新范式 |
| 技术质量 | 8/10 | 受控消融、多标注者一致性检验严谨 |
| 实验充分性 | 8/10 | 5 模型 × 5 KG × 多层查询覆盖充分 |
| 写作质量 | 8/10 | 结构清晰、结论明确 |
| 实用性 | 7/10 | 对领域选型有指导，但泛化与落地仍有距离 |

## 相关论文

### 直接相关
- [[GraphRAG|GraphRAG]] - 图检索增强生成，本文的接地策略之一可与之对照

> [!tip] 关键启示
> 结构化知识（图接地）能稳健地降低 LLM 幻觉，但「领域专用模型更强」是一个需要被检验的假设——通用模型的泛化能力往往更关键。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读：对 LLM+KG 集成与幻觉缓解的实证研究感兴趣者值得一读。
