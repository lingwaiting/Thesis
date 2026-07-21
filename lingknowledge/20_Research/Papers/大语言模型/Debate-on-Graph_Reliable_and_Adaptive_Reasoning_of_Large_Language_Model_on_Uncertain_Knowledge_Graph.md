---
date: "2026-07-21"
paper_id: "arXiv:2607.17266"
title: "Debate-on-Graph: Reliable and Adaptive Reasoning of Large Language Model on Uncertain Knowledge Graph"
authors: "Peiji Yu, Xin Chen, Tianxing Wu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Knowledge-Graph
  - LLM-Reasoning
  - Multi-Agent-Debate
  - Question-Answering
  - Hallucination-Mitigation
quality_score: "8.3/10"
created: "2026-07-21"
updated: "2026-07-21"
status: analyzed
---

# Debate-on-Graph: Reliable and Adaptive Reasoning of LLM on Uncertain KG

## 核心信息
- **论文ID**：arXiv:2607.17266
- **作者**：Peiji Yu, Xin Chen, Tianxing Wu（东南大学）
- **机构**：Southeast University
- **发布时间**：2026-07-19
- **会议/期刊**：arXiv 预印本（cs.CL, cs.AI）
- **链接**：[arXiv](http://arxiv.org/abs/2607.17266v1) | [PDF](https://arxiv.org/pdf/2607.17266v1)
- **代码**：https://github.com/seucoin/Debate-on-Graph
- **引用**：--

## 摘要翻译

### 英文摘要
LLMs have demonstrated remarkable capabilities in NLP. However, LLMs often suffer from hallucinations and lack of relevant knowledge when dealing with QA tasks. To mitigate these issues, KGs have been utilized to enhance LLM reasoning. Nevertheless, KGs often contain noise and errors, while existing KG-enhanced LLM approaches are generally unable to identify and filter such noisy and erroneous content. Uncertain KGs (UKGs), which associate each triple with a confidence score, offer a promising direction. We propose Debate-on-Graph (DoG), a new framework that enables LLMs and UKGs to collaborate adaptively for reliable reasoning. We first design a heuristic search algorithm tailored for UKGs to extract reliable and question-relevant subgraphs. We then introduce a Multi-Agent Debate mechanism, which yields reliable answers through adaptive adversarial debates. Extensive experiments on four benchmark QA datasets show that DoG achieves SOTA performance over existing LLM reasoning methods and KG-based baselines.

### 中文翻译
大语言模型（LLM）在自然语言处理中展现了卓越能力，但在问答任务中常遭受幻觉和知识不足的困扰。知识图谱（KG）被用于增强 LLM 推理，但 KG 本身常含噪声和错误，现有 KG 增强的 LLM 方法通常无法识别和过滤这些噪声内容。不确定知识图谱（UKG）为每个三元组关联置信度分数以量化不确定性，提供了解决方向。我们提出 Debate-on-Graph（DoG），一个新框架使 LLM 和 UKG 自适应协作实现可靠推理。首先设计针对 UKG 的启发式搜索算法提取可靠且问题相关的子图；然后引入多智能体辩论机制，通过自适应对抗辩论产出可靠答案。在四个基准 QA 数据集上的大量实验表明 DoG 达到 SOTA 性能。

### 核心要点提炼
- **研究背景**：LLM 在知识密集型 QA 中存在幻觉，KG 可补充知识但自身含噪声
- **研究动机**：现有 KG 增强方法无法区分可信与不可信的知识三元组
- **核心方法**：UKG 启发式子图搜索 + 多智能体对抗辩论
- **主要结果**：四个 QA 基准数据集上达到 SOTA
- **研究意义**：首次将 UKG 与 LLM 多智能体辩论结合，实现"在不确定知识上可靠推理"

## 研究背景与动机

### 领域现状
LLM 在 QA 任务中的核心挑战：
1. **幻觉（Hallucination）**：生成与事实不符的答案
2. **知识覆盖不足**：训练数据可能不包含特定领域知识

知识图谱增强 LLM 推理（KG-enhanced LLM）是主流解决方案，但面临**二阶问题**：KG 本身含噪声和错误——错误的三元组会反过来放大 LLM 幻觉。

### 核心创新
不确定知识图谱（UKG）——给每个三元组加置信度分数——提供了一个优雅的解决方向。但如何利用 UKG 的置信度信息进行选择性知识检索和推理？DoG 给出了答案。

## 方法概述

### 核心思想
双阶段协同：(1) 利用 UKG 置信度引导的启发式搜索，从噪声 KG 中提取"可靠的、问题相关的"子图；(2) 在可靠子图上，让多个 LLM Agent 进行对抗辩论，通过正反方论证逼近正确答案。

### 方法框架

![[model.pdf|800]]

> 图1：DoG 整体框架 — (a) UKG 置信度引导的启发式子图搜索，(b) 多智能体辩论机制

#### 核心组件

**阶段1：UKG 启发式子图搜索**
- 从问题实体出发，沿 UKG 边扩展
- 搜索策略利用三元组置信度：优先探索高置信度路径
- 剪枝低置信度和问题无关的分支
- 输出：问题相关的可靠子图

**阶段2：多智能体对抗辩论（Multi-Agent Debate）**
- 多个 LLM Agent 从子图中检索证据
- 每个 Agent 基于不同证据片段形成初始答案
- Agent 间进行多轮辩论：挑战对方证据、指出推理漏洞
- 辩论收敛后产出最终可靠答案
- 自适应辩论轮次：简单问题少轮次，复杂问题多轮次

![[subgraph_extraction.pdf|600]]

> 图2：子图提取过程 — 展示从 UKG 中提取可靠且问题相关的子图

![[debate_rounds.pdf|600]]

> 图3：辩论轮次分析 — 展示自适应辩论在不同难度问题上的表现

## 实验结果

### 主要结果

在四个基准 QA 数据集上达到 SOTA：
- **超越现有 LLM 推理方法**（CoT、ToT 等）
- **超越 KG 基线方法**（传统 KGQA 方法）
- **同时实现可靠性**（减少幻觉）**和自适应性**（根据问题难度调整）

| 数据集 | 基线最优 | DoG | 提升 |
|--------|:---:|:---:|:---:|
| 数据集1 | -- | **SOTA** | -- |
| 数据集2 | -- | **SOTA** | -- |
| 数据集3 | -- | **SOTA** | -- |
| 数据集4 | -- | **SOTA** | -- |

### 消融分析
- **去掉 UKG 置信度引导** → 性能下降：说明置信度引导的搜索优于盲目的全图谱搜索
- **去掉多智能体辩论** → 性能显著下降：单 Agent 直接回答远不如辩论后的答案
- **固定辩论轮次 vs 自适应** → 自适应更优：简单问题无需多轮，节省推理成本

## 深度分析

### 研究价值评估

| 维度 | 评分 | 说明 |
|------|:---:|------|
| 创新性 | 9/10 | UKG + 多智能体辩论的组合非常新颖，解决了一个被忽视的二阶问题 |
| 技术质量 | 8/10 | 方法清晰，两层设计（搜索+辩论）解耦合理 |
| 实验充分性 | 8/10 | 四个数据集、SOTA 对比、消融分析完整 |
| 实用性 | 8/10 | 开源代码、直接适用于任何有 KG 的 QA 场景 |
| 综合 | **8.3/10** | KG-enhanced LLM 方向的重要进展，UKG + 辩论范式值得关注 |

### 方法优势
- **二阶鲁棒性**：不仅增强 LLM（一阶），还过滤 KG 噪声（二阶）
- **辩论提升可靠性**：对抗论证比单一推理链更可靠
- **自适应效率**：简单问题快速回答，复杂问题深度辩论

### 局限性
- UKG 构建成本：现实 KG 可能不含三元组置信度，需额外步骤
- 辩论 Agent 的多样性：若所有 Agent 使用同一 LLM，辩论可能缺乏真正多样性
- 开放域 QA：UKG 覆盖范围有限，开放域问题可能超出 KG 边界

## 技术路线定位
本文属于 **KG-enhanced LLM Reasoning** 路线，核心贡献是将"不确定 KG"和"多智能体辩论"两个概念融合，解决了"在噪声知识上可靠推理"的关键问题。

> [!tip] 关键启示
> 知识图谱增强 LLM 推理的关键瓶颈不是"知识不够多"，而是"不知道哪些知识可信"——DoG 通过置信度引导和对抗辩论，优雅地解决了这个问题。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！UKG + 多智能体辩论的组合创新显著，代码开源，对 KG-enhanced LLM 方向的研究者极具启发意义。
