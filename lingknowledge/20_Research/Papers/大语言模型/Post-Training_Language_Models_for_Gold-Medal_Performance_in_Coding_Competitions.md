---
date: "2026-09-04"
paper_id: "arXiv:2609.02849"
title: "Post-Training Language Models for Gold-Medal Performance in Coding Competitions"
authors: "Aleksander Ficek, Sean Narenthiran, Mehrzad Samadi, Somshubra Majumdar, Boris Ginsburg"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 代码生成
  - 强化学习
  - 竞赛编程
  - 测试时计算
  - 推理模型
quality_score: "8.5/10"
created: "2026-09-04"
updated: "2026-09-04"
status: analyzed
---

# Post-Training Language Models for Gold-Medal Performance in Coding Competitions

## 核心信息
- **论文ID**：arXiv:2609.02849
- **作者**：Aleksander Ficek, Sean Narenthiran, Mehrzad Samadi, Somshubra Majumdar, Boris Ginsburg
- **机构**：NVIDIA
- **发布时间**：2026-09-02
- **会议/期刊**：--（arXiv 预印本，cs.LG / cs.AI / cs.CL / cs.MA / cs.SE）
- **链接**：[arXiv](http://arxiv.org/abs/2609.02849v1) | [PDF](https://arxiv.org/pdf/2609.02849v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Competitive programming has become a key test of large language model reasoning, with international competitions such as IOI and ICPC representing its most challenging settings. We present an end-to-end specialization pipeline combining large-scale problem curation, synthetic reasoning traces, supervised fine-tuning (SFT), and reinforcement learning (RL). Using 22,000 curated problems, we train Nemotron-3-Nano-CC (30B-A3B) with SFT and RL and Nemotron-3-Ultra-CC (550B-A55B) with SFT alone. We further introduce GenCorrect, a feedback-driven test-time compute strategy that iteratively generates, evaluates, and refines diverse solutions. On IOI 2025, Nano-CC improves from 130 points to 291 after post-training and to 468 with GenCorrect, exceeding the gold threshold of 438.3 while Ultra-CC reaches 502. Guided by these results, we develop a competition-specific Ultra-CC system and evaluate it prospectively during IOI 2026. Under the same time, internet-access, and submission constraints as human contestants, it scores 535.4 out of 600, exceeding both the gold threshold of 361.12 and the top human score of 498.27. To our knowledge, this is the first AI system to outscore the highest-scoring human contestant on an IOI problem set.

### 中文翻译
竞赛编程已成为检验大语言模型推理能力的关键测试，而 IOI 与 ICPC 等国际竞赛代表了其中最具挑战性的场景。作者提出一个端到端的专精化流水线，结合大规模题目整理、合成推理轨迹、监督微调（SFT）与强化学习（RL）。利用 22,000 道整理后的题目，作者用 SFT 与 RL 训练了 Nemotron-3-Nano-CC（30B-A3B），并仅用 SFT 训练了 Nemotron-3-Ultra-CC（550B-A55B）。作者进一步提出 GenCorrect，一种反馈驱动的测试时计算策略，迭代地生成、评估并精化多样化解答。在 IOI 2025 上，Nano-CC 从后训练前的 130 分提升到 291 分，配合 GenCorrect 达到 468 分，超过 438.3 的金牌线；Ultra-CC 则达到 502 分。基于这些结果，作者开发了竞赛专用的 Ultra-CC 系统，并在 IOI 2026 上进行了前瞻性评估。在与人类选手相同的时间、联网访问与提交约束下，它取得 600 分中的 535.4 分，同时超过 361.12 的金牌线与 498.27 的人类最高分。据作者所知，这是首个在 IOI 题集上超越最高分人类选手的 AI 系统。

### 核心要点提炼
- **研究背景**：竞赛编程是检验 LLM 推理的难点，IOI/ICPC 最具挑战性。
- **研究动机**：构建端到端流水线，让 LLM 在真实竞赛约束下达到金牌乃至超越人类最高分。
- **核心方法**：大规模题目整理 + 合成推理轨迹 + SFT/RL 后训练 + GenCorrect 测试时计算。
- **主要结果**：IOI 2025 Nano-CC 130→468 分（超金牌线）；IOI 2026 竞赛专用 Ultra-CC 达 535.4/600，超人类最高分。
- **研究意义**：首次实现 AI 在 IOI 题集上超越最高分人类选手，验证后训练 + 测试时计算的规模化路径。

## 研究背景与动机

### 领域现状
竞赛编程（IOI/ICPC）是 LLM 推理能力的终极试金石。此前系统虽能接近但尚未稳定达到金牌水平，更未在真实竞赛约束下超越顶级人类选手。

### 现有方法的局限性
1. **缺乏端到端专精**：通用模型在竞赛题的算法推理、严格时间/内存约束下表现不足。
2. **测试时计算未充分开发**：缺乏反馈驱动的生成—评估—精化闭环。
3. **真实约束未对齐**：多数评测未对齐竞赛的时间、联网与提交次数限制。

### 研究动机
作者希望用"题目整理 + 合成轨迹 + SFT/RL + 测试时计算"的完整流水线，在真实 IOI 约束下实现金牌级乃至超人类的表现。

## 研究问题

### 核心研究问题
能否通过规模化后训练与反馈驱动的测试时计算，让 LLM 在真实竞赛编程约束下稳定达到金牌并超越顶级人类选手？

## 方法概述

### 核心思想
以大规模题目整理与合成推理轨迹为数据基础，用 SFT/RL 后训练提升竞赛编程能力，再以 GenCorrect 在测试时迭代生成、评估、精化多样化解答。

### 方法框架

#### 整体架构
![[fig_post_training_pipeline_page1.png|600]]

> 图1：端到端后训练流水线——题目整理 → 合成推理轨迹 → SFT/RL 专精化。

![[fig_gencorrect_pipeline_page1.png|600]]

> 图2：GenCorrect 测试时计算——迭代生成、评估并精化多样化解答的反馈闭环。

#### 各模块详细说明

**模块1：大规模题目整理与合成轨迹**
- 整理 22,000 道竞赛题，生成合成推理轨迹作为训练监督信号。

**模块2：后训练（SFT + RL）**
- Nano-CC（30B-A3B）用 SFT + RL；Ultra-CC（550B-A55B）仅用 SFT。

**模块3：GenCorrect 测试时计算**
- 反馈驱动：迭代生成多样化解答，评估（execute/evaluate），并据反馈精化。

## 实验结果

### 数据集
IOI 2025（回顾性）、IOI 2026（前瞻性，真实竞赛约束）。

### 主要结果
- **IOI 2025**：Nano-CC 后训练 130→291 分，加 GenCorrect 达 **468**（金牌线 438.3）；Ultra-CC 达 **502**。
- **IOI 2026**：竞赛专用 Ultra-CC 在真实约束下取得 **535.4/600**，超过金牌线 **361.12** 与人类最高分 **498.27**。
- **里程碑**：首个在 IOI 题集上超越最高分人类选手的 AI 系统。

![[fig_model_performance_page1.png|600]]

> 图3：模型在 IOI 各轮次上的得分演进与能力提升。

## 深度分析

### 研究价值评估
- **理论贡献**：系统验证了"后训练 + 测试时计算"在竞赛编程这一极端推理场景的规模化路径。
- **实际应用**：为代码智能体、算法设计与严格约束下的推理系统提供直接可复用的方法。

### 方法优势
1. **端到端完整**：从数据整理到后训练到测试时计算闭环，工程体系完整。
2. **真实约束对齐**：IOI 2026 前瞻性评估对齐时间/联网/提交限制，可信度高。
3. **测试时计算有效**：GenCorrect 带来显著增益（Nano-CC 291→468）。

### 局限性
- Ultra-CC 仅用 SFT，RL 在大规模模型上的增益未充分挖掘。
- 依赖大量整理题目与合成轨迹，数据成本高。
- 结论高度绑定 Nemotron 模型家族与竞赛场景，通用推理泛化性待验证。

## 技术路线定位
本文属于 **竞赛编程推理 × 后训练与测试时计算** 路线，是"规模化 RL + test-time compute"在真实竞赛约束下的一次里程碑式验证。

## 未来工作建议
1. 在 Ultra 规模上引入 RL，探索大模型 RL 的进一步增益。
2. 把 GenCorrect 的反馈闭环推广到更广泛的代码与数学推理任务。
3. 研究降低数据整理成本、提升合成轨迹质量的方法。

## 我的综合评价

### 价值评分
**8.5/10** — 里程碑式成果（首个超人类 IOI 选手），工程体系完整、前瞻性评估可信，但结论绑定特定模型家族与竞赛场景。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | GenCorrect + 端到端专精，组合式但里程碑意义强 |
| 技术质量 | 9/10 | 前瞻性真实约束评估，严谨 |
| 实验充分性 | 9/10 | 两届 IOI + 多尺度模型对照 |
| 写作质量 | 8/10 | 清晰 |
| 实用性 | 8/10 | 代码智能体与竞赛推理直接可借鉴 |

> [!tip] 关键启示
> 真实竞赛约束下的前瞻性评估，让"后训练 + 测试时计算"的规模化路径具备了可信度——这是评测与工程共同严谨的范本。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐——AI 首次在 IOI 上超越人类最高分，竞赛编程推理的里程碑之作。
