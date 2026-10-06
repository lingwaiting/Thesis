---
date: "2026-10-06"
paper_id: "arXiv:2610.05387"
title: "GNN-CB: A Graph Neural Network Competition Benchmark for Human and LLM Evaluation"
authors: "Murad Hossen, Tasneem Selim, Gurur Gamgam, Tuga Yousif, Abderrahmane Kasmi, Ikram Aissiou, Mubaraq Onipede, Faran Taimoor Butt, Sanae Zrigui, Rosa Y. G. Paccotacya-Yanque, Ignatius Balayo, Ikram Elhouiti, Hadil Affes, Bijay Adhikari, Sargam Goyal, Muhammad Ibrahim Isah, Mohammad Idrees Bhat, Samuel Kangoni Matia, Peguy Kem-Meka Tiotsop Kadzue, Maha Trabelsi, Emmanuel Owusu, Vinit, Nour Majdoub, Tamiru Alemnew, Islem Rekik"
domain: "大语言模型"
tags:
  - 论文笔记
  - Graph-Neural-Network
  - Benchmark
  - LLM-Evaluation
  - Code-Generation
quality_score: "8.8/10"
created: "2026-10-06"
updated: "2026-10-06"
status: analyzed
---

# GNN-CB: A Graph Neural Network Competition Benchmark for Human and LLM Evaluation

## 核心信息
- **论文ID**：arXiv:2610.05387
- **作者**：Murad Hossen, Tasneem Selim, …, Islem Rekik（共 25 位）
- **机构**：BASIRA Lab, Imperial College London（据 `basiralab.github.io` 推断，末位作者 Islem Rekik 为该实验室负责人）
- **发布时间**：2026-10-04
- **会议/期刊**：arXiv（cs.LG / cs.AI / cs.CL / cs.SE）
- **链接**：[arXiv](http://arxiv.org/abs/2610.05387) | [PDF](https://arxiv.org/pdf/2610.05387)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models (LLMs) have demonstrated strong performance on coding and reasoning benchmarks; however, their ability to solve graph-structured machine learning problems remains largely unexplored. In particular, no benchmark currently evaluates whether LLMs can autonomously solve end-to-end Graph Neural Network (GNN) coding tasks under realistic competition settings. To address this gap, this paper introduces GNN-CB, the first competition-based benchmark for evaluating both humans and LLMs on GNN coding tasks. GNN-CB consists of 18 curated competitions spanning node-, edge-, and graph-level prediction across diverse graph categories, domains, and difficulty tiers. All submissions are evaluated through a unified automated pipeline with hidden test sets and standardized scoring. Human participants solve tasks under controlled competition constraints, while LLMs are evaluated using a frozen zero-shot prompting protocol based on a plan-then-code paradigm with bounded execute-and-repair loops. The benchmark additionally supports both non-agent and autonomous agent-based evaluation within the same protocol. Under our evaluated protocol, LLMs rarely match Human Top performance and show less stable performance across competitions. No single model dominates: a few competitions are won by LLMs, yet humans still hold the top score on most tasks. We release GNN-CB as a living benchmark with automated evaluation infrastructure, dynamic leaderboards, and reproducible execution pipelines.

### 中文翻译
大语言模型（LLM）在编程和推理基准上已展现出强大性能，但其解决**图结构机器学习问题**的能力在很大程度上仍未被探索。特别是，目前尚无基准能评估 LLM 能否在真实竞赛环境下**端到端自主解决图神经网络（GNN）编程任务**。为此，本文提出 GNN-CB——首个面向 GNN 编程任务的、同时评估人类与 LLM 的竞赛式基准。GNN-CB 包含 18 个精心策划的竞赛，覆盖节点级、边级、图级预测，横跨多样化的图类别、领域和难度层级。所有提交都通过统一的自动化流水线进行评估，采用隐藏测试集和标准化评分。人类参与者在受控竞赛约束下解题，而 LLM 则通过基于"先规划后编码"范式、带限定"执行-修复"循环的冻结零样本提示协议进行评估。该基准在同一协议下同时支持非智能体和自主智能体两种评估方式。在所评估的协议下，LLM 很少能达到人类顶尖水平，且在跨竞赛上表现更不稳定；没有单一模型全面占优——少数竞赛由 LLM 胜出，但大多数任务的人类顶尖分数仍由人类保持。作者以"活基准"形式开源 GNN-CB，附带自动化评估基础设施、动态排行榜和可复现的执行流水线。

### 核心要点提炼
- **研究背景**：LLM 在通用编程与推理基准上进步显著，但面向 GNN/图学习的编程能力缺乏系统性评测。
- **研究动机**：现有基准多聚焦算法/通用编程，缺少"端到端解决 GNN 任务"的竞赛式评估。
- **核心方法**：18 个竞赛 + 统一自动化评测流水线 + 隐藏测试集 + 标准化评分；LLM 采用 plan-then-code 零样本协议 + 有界 execute-and-repair。
- **主要结果**：LLM 普遍落后于人类顶尖，跨竞赛表现不稳定，无单一模型全面领先。
- **研究意义**：填补 GNN 编程能力基准的空白，提供人机对比的实践导向资源。

## 研究背景与动机

### 领域现状
图神经网络（GNN）已成为处理图结构数据的主流方法，而 LLM 在代码生成（如 HumanEval、SWE-bench）和数学推理（如 MATH）上屡创佳绩。但"编写一个完整的 GNN 训练/推理解决方案"这一任务，涉及领域知识（图卷积、消息传递）、工程实现（数据处理、训练循环）与评测（隐藏测试集泛化）的复合能力，远超现有代码基准的覆盖范围。

### 现有方法的局限性
- 现有代码基准聚焦通用算法或 Web/软件工程任务，缺乏图学习领域的专项任务。
- 缺乏"端到端"定义：多数基准只测片段补全，不测从数据到模型的完整解决方案。
- 缺乏人机可比的竞赛式设定，难以量化 LLM 相对人类专家的差距。

### 研究动机
需要一个统一的、竞赛式的、可复现的 GNN 编程基准，来系统回答"LLM 能否像人类一样解决图机器学习问题"。

## 研究问题

### 核心研究问题
在真实竞赛约束下，LLM 能否端到端自主解决 GNN 编程任务？其与人类顶尖水平（Human Top）的差距有多大？哪种评估协议（非智能体 vs 自主智能体）更能发挥 LLM 能力？

## 方法概述

### 核心思想
把"评测 GNN 编程能力"做成**竞赛基准**：18 个任务覆盖节点/边/图三级预测与多难度、多领域；用统一自动化流水线 + 隐藏测试集 + 标准化评分保证公平；对人类与 LLM 采用可对齐的受控协议。

### 方法框架

#### 整体架构
GNN-CB 由三层组成：① **任务层**（18 个竞赛）；② **评测层**（统一流水线、隐藏测试集、标准评分）；③ **协议层**（人类受控约束 / LLM 冻结零样本 plan-then-code + 有界 execute-and-repair，支持非智能体与自主智能体两种模式）。

![[llm2_vs_human_per_competition.png|600]]

> 图1：LLM 与人类顶尖在每项竞赛上的得分对比（来源：arxiv-source）。

#### 关键设计
- **Plan-then-Code 范式**：LLM 先规划解题步骤，再生成代码，降低端到端任务的难度。
- **有界 execute-and-repair 循环**：允许有限次执行与修复，模拟真实工程迭代。
- **双评估模式**：非智能体（单次推理）与自主智能体（可调用工具/多轮迭代）。

## 实验结果

### 实验目标
量化 LLM 在 18 个 GNN 竞赛上的表现，并与人类顶尖进行系统对比。

### 主要结果
- LLM **很少达到** Human Top 水平，且跨竞赛稳定性更差。
- **无单一模型全面占优**：少数竞赛由 LLM 胜出，但大多数任务人类仍保持最高分。
- 评测协议对结果影响显著，自主智能体模式在部分任务上带来提升。

### 实验结果图

![[llm_leaderboard_easy.png|600]]

> 图2：简单难度组 LLM 排行榜。

![[llm_leaderboard_hard.png|600]]

> 图3：困难难度组 LLM 排行榜。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出首个竞赛式 GNN 编程基准，将"GNN 编程能力"这一模糊概念操作化为可量化指标。
- 建立人类与 LLM 可对齐的受控评测协议，为跨模型公平对比提供框架。

#### 实际应用价值
- 为 GNN 教学、模型选型与能力评估提供实践导向的公开资源。
- "活基准 + 动态排行榜"支持持续追踪 LLM 在图学习领域的能力演进。

### 局限性分析
- 18 个竞赛规模有限，领域覆盖仍需扩展（如时序图、异构图、超图）。
- 冻结零样本协议可能低估具备工具调用/微调能力的模型。
- 人类样本量及竞赛纪律的一致性需进一步说明。

## 我的综合评价

### 价值评分

#### 总体评分
**8.8/10** - 方向填补空白、评测设计规范，但作为"基准论文"理论创新有限，更偏向工程与生态价值。

#### 分项评分
| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 首个 GNN 竞赛基准，但方法论本身组合已有范式 |
| 技术质量 | 9/10 | 统一流水线、隐藏测试、标准评分，设计严谨 |
| 实验充分性 | 9/10 | 多模型、多难度、人机对比，实验较全面 |
| 写作质量 | 8/10 | 结构清晰，量化结论明确 |
| 实用性 | 9/10 | 活基准 + 排行榜 + 可复现流水线，实用价值高 |

> [!tip] 关键启示
> LLM 在"通用编程"上的高分尚未迁移到"领域编程（GNN）"，领域知识 + 端到端工程能力仍是明显短板。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读——适合关注 LLM 编程能力边界与图学习评估的研究者。
