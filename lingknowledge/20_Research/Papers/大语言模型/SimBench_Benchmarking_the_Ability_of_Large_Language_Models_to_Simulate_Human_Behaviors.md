---
date: "2025-10-20"
paper_id: "arXiv:2510.17516"
title: "SimBench: Benchmarking the Ability of Large Language Models to Simulate Human Behaviors"
authors: "6位作者"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Simulation
  - Benchmark
  - Human-Behavior
  - Alignment
  - Instruction-Tuning
quality_score: "7.5/10"
created: "2026-07-27"
updated: "2026-07-27"
status: analyzed
---

# SimBench: Benchmarking the Ability of Large Language Models to Simulate Human Behaviors

## 核心信息
- **论文ID**：arXiv:2510.17516
- **作者**：6位作者
- **机构**：--
- **发布时间**：2025-10-20
- **链接**：[arXiv](https://arxiv.org/abs/2510.17516) | [PDF](https://arxiv.org/pdf/2510.17516)
- **引用**：37（Semantic Scholar）

## 摘要翻译

### 英文摘要
Large language model (LLM) simulations of human behavior have the potential to revolutionize the social and behavioral sciences, if and only if they faithfully reflect real human behaviors. Current evaluations of simulation fidelity are fragmented, based on bespoke tasks and metrics, creating a patchwork of incomparable results. To address this, we introduce SimBench, the first large-scale, standardized benchmark for a robust, reproducible science of LLM simulation. By unifying 20 diverse datasets covering tasks from moral decision-making to economic choice across a large global participant pool, SimBench provides the necessary foundation to ask fundamental questions about when, how, and why LLM simulations succeed or fail. We show that the best LLMs today achieve meaningful but modest simulation fidelity (score: 40.80/100), with performance scaling log-linearly with model size but not with increased inference-time compute. We discover an alignment-simulation tradeoff: instruction tuning improves performance on low-entropy (consensus) questions but degrades it on high-entropy (diverse) ones. Models particularly struggle when simulating specific demographic groups. Finally, we demonstrate that simulation ability correlates most strongly with knowledge-intensive reasoning (MMLU-Pro, r = 0.939).

### 中文翻译
大语言模型对人类行为的模拟有潜力彻底改变社会和行为科学——前提是它们能忠实地反映真实人类行为。当前的模拟保真度评估分散在定制任务和指标上，形成了不可比较的结果拼接。为此，我们提出 SimBench，首个大规模标准化基准，用于建立稳健、可复现的 LLM 模拟科学。通过统一 20 个多样化数据集，覆盖从道德决策到经济选择等任务，并跨越大规模全球参与者池，SimBench 为回答 LLM 模拟何时、如何以及为何成功或失败提供了必要的基础。我们发现，当前最佳的 LLM 实现了有意义但有限的模拟保真度（得分 40.80/100），性能随模型规模呈对数线性增长，但不随推理时计算的增加而提升。我们发现了一个**对齐-模拟权衡**：指令微调在低熵（共识）问题上提升表现，但在高熵（多样化）问题上反而退化。模型在模拟特定人口群体时尤其困难。最后，模拟能力与知识密集型推理（MMLU-Pro）的相关性最强（r = 0.939）。

### 核心要点提炼
- **研究背景**：LLM模拟人类行为的研究分散，缺乏统一基准
- **研究动机**：建立可复现的 LLM 模拟科学
- **核心方法**：统一20个数据集，建立标准化评估框架
- **主要结果**：最佳模型仅得40.80/100，发现对齐-模拟权衡
- **研究意义**：为 LLM 模拟研究提供可测量的科学基础

## 研究背景与动机

### 领域现状
LLM 越来越多地被用于模拟人类行为——从经济学实验到社会调查。然而，不同研究使用不同的任务、指标和模型，结果无法比较，阻碍了该领域的科学进展。

### 现有方法的局限性
- **碎片化评估**：各研究自定任务和指标
- **不可比较**：不同基准的结果无法对齐
- **缺乏系统理解**：不知道 LLM 模拟何时、为何成功或失败

### 研究动机
建立标准化、可复现的评估框架，使 LLM 模拟研究从"艺术"走向"科学"。

## 研究问题

### 核心研究问题
1. 当前 LLM 模拟人类行为的整体保真度如何？
2. 什么因素影响 LLM 的模拟能力（模型规模、推理计算、训练方法）？
3. LLM 模拟在不同任务类型和人口群体上的表现有何差异？

## 方法概述

### 核心思想
统一 20 个多样化数据集，覆盖多种行为类型，建立标准化评估协议，系统分析 LLM 模拟能力的影响因素。

![[centaur_entropy_faceted_page1.png|800]]

> 图1：SimBench 核心发现——对齐-模拟权衡的可视化

### 方法框架

**数据集统一**
- 20 个多样化数据集
- 任务类型：道德决策、经济选择、社会判断、风险评估等
- 参与者池：大规模全球样本

**评估维度**
- **保真度分数**（0-100）：模拟人类回答的准确度
- **任务熵值**：低熵（共识）vs 高熵（多样化）
- **人口群体**：不同年龄、性别、文化背景

**分析框架**
- 模型规模：log-linear scaling
- 推理计算：inference-time compute scaling
- 训练方法：instruction tuning 的影响
- 基准能力：与 MMLU-Pro 等基准的相关性

### 关键发现

1. **最佳模型模拟保真度仅 40.80/100**：LLM 远未达到忠实模拟人类的水平
2. **对齐-模拟权衡**：指令微调提升共识问题但损害多样化问题
3. **规模定律**：保真度随模型规模对数线性增长
4. **推理计算无用**：增加推理时计算不提升模拟保真度
5. **能力相关**：模拟能力与知识推理（MMLU-Pro r=0.939）强相关

## 实验结果

### 主要结果
- 最佳 LLM 模拟保真度：40.80/100（有改进但远非完美）
- 模型规模对数线性 scaling（但边际收益递减）
- 推理时计算增加不带来提升
- 指令微调在低熵问题上增益，高熵问题上退化
- 特定人口群体模拟尤其困难

### 关键洞察
- **对齐-模拟权衡**是最重要的发现：让 LLM 更"对齐"可能使其更不擅长模拟多样化的人类行为
- 模拟能力与推理能力高度相关，暗示模拟主要依赖知识而非"共情"

## 深度分析

### 研究价值评估

#### 理论贡献
- **标准化框架**：统一20个数据集建立可复现的评估科学
- **对齐-模拟权衡**：首次实证发现这一关键权衡
- **影响因素系统分析**：规模、计算、训练方法的影响

#### 实际应用价值
- 为社会科学研究者提供模型选择参考
- 揭示当前 LLM 在模拟中的不足
- 指出指令微调的潜在副作用

### 局限性
- 20 个数据集可能遗漏重要的行为类型
- 主要覆盖英文和西方文化背景
- 静态评估，未考虑时间动态
- 仅评估单轮问答，忽略交互式模拟

## 我的综合评价

### 价值评分

#### 总体评分
**7.5/10** — 标准化了 LLM 模拟评估，对齐-模拟权衡是关键洞察，但任务覆盖和文化多样性有待扩展

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次系统化 LLM 模拟基准，发现对齐-模拟权衡 |
| 技术质量 | 7/10 | 方法严谨但复杂度适中 |
| 实验充分性 | 8/10 | 多维度系统分析，20个数据集 |
| 写作质量 | 7/10 | 清晰，发现突出 |
| 实用性 | 7/10 | 对社会科学研究有指导价值 |

> [!tip] 关键启示
> 对齐-模拟权衡是一个重要警示：让 LLM 更"有用"（对齐）可能使其更不擅长模拟真实人类的多样性。

> [!warning] 注意事项
> - 结果可能受西方文化偏差影响
> - 仅覆盖单轮问答，不适用于交互式模拟场景
> - 40.80/100 的保真度表明 LLM 模拟仍有很长的路要走

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐！对于关注 LLM 社会科学应用和行为模拟的研究者，这是必读的基准工作。

## 相关论文
- [[Out of One, Many]] — LLM 模拟人类行为
- [[Generative Agents]] — 交互式 Agent 模拟

## 外部资源
- 论文主页：https://arxiv.org/abs/2510.17516
- Semantic Scholar：https://www.semanticscholar.org/paper/a0ba6c7efb166d5f1715d684314ee1a9dfb3d427
