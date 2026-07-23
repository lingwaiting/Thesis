---
date: "2026-07-21"
paper_id: "arXiv:2607.19226"
title: "The Price of Reasoning: Cost-Quality Tradeoffs in Reinforcement Learning for Neural Machine Translation"
authors: "Michael Jungo, Aixiu An"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM
  - RLVR
  - Neural-Machine-Translation
  - Reasoning
  - Legal-Translation
  - Post-Training
quality_score: "7.5/10"
created: "2026-07-23"
updated: "2026-07-23"
status: analyzed
---

# The Price of Reasoning: Cost-Quality Tradeoffs in Reinforcement Learning for Neural Machine Translation

## 核心信息
- **论文ID**：arXiv:2607.19226
- **作者**：Michael Jungo, Aixiu An
- **机构**：AIBEX, University of Fribourg, Switzerland; iCoSys, University of Applied Sciences and Arts Western Switzerland (HES-SO)
- **发布时间**：2026-07-21
- **会议/期刊**：DocEng '26 (ACM Symposium on Document Engineering)
- **链接**：[arXiv](http://arxiv.org/abs/2607.19226v1) | [PDF](https://arxiv.org/pdf/2607.19226v1)
- **评分**：9.6 (start-my-day 推荐评分)

## 摘要翻译

### 英文摘要
Reinforcement learning with verifiable rewards (RLVR) has been established as a viable paradigm for the post-training of Large Language Models (LLMs), including downstream tasks, such as Neural Machine Translation (NMT). With the latest research indicating that RLVR could be the preferred training method for translating legal documents due to the induced reasoning capabilities, it raises the question whether it is really attributed to the reasoning or more generally to the training paradigm. We investigate the importance of including the model's reasoning trace in the generated responses during both training and inference by systematically omitting it from one of the phases. Our experiments show that including the reasoning, specifically during inference, has a positive effect on the overall translation quality. Furthermore, we recognise that the reasoning leads to an increase in output tokens, hence we study the cost-quality tradeoff between the increased computational demands and the improved translation quality.

### 中文翻译
带可验证奖励的强化学习（RLVR）已成为大语言模型后训练的一种可行范式，包括神经机器翻译等下游任务。最新研究表明，RLVR 可能是法律文件翻译的首选训练方法，因为它能诱导推理能力。但这引发了一个问题：翻译质量的提升究竟归因于推理能力本身，还是更一般地归因于训练范式？本文通过在训练和推理阶段系统地移除推理轨迹，研究了在生成响应中包含模型推理过程的重要性。实验表明，包含推理（尤其是在推理阶段）对整体翻译质量有正向影响。此外，我们认识到推理会导致输出 token 数量增加，因此研究了增加的计算需求与改进的翻译质量之间的成本-质量权衡。

### 核心要点提炼
- **研究背景**：RLVR 在 LLM 后训练中应用广泛，但推理轨迹的具体作用尚不清晰
- **研究动机**：分离 RLVR 训练范式和推理能力各自对翻译质量的贡献
- **核心方法**：在训练/推理阶段分别省略推理轨迹，进行系统的对照实验
- **主要结果**：推理在推理阶段包含时对翻译质量提升最显著；但增加了 token 成本
- **研究意义**：首次量化 RLVR 中 reasoning trace 的成本-质量权衡

## 研究背景与动机

### 领域现状
RLVR 已广泛应用于 LLM 后训练，特别是在需要精确输出的任务中（如数学推理、代码生成）。在 NMT 领域，RLVR 被证明可以提升翻译质量，特别是在法律文件翻译等对准确性要求极高的场景。

### 现有方法的局限性
- 现有工作将 RLVR 在 NMT 中的成功归因于"推理能力的诱导"，但未严格区分推理能力和训练范式的各自贡献
- 推理过程会增加输出 token 数量，带来推理成本，但这一成本尚未被系统量化

### 研究动机
回答两个核心问题：
1. RLVR 带来的翻译质量提升是由推理能力还是训练范式驱动的？
2. 推理的成本（增加 token 数）与质量提升之间的 tradeoff 是什么？

## 研究问题

### 核心研究问题
在 RLVR 训练的 NMT 中，模型推理轨迹在训练和推理阶段分别扮演什么角色？推理带来的质量提升是否值得其增加的计算成本？

## 方法概述

### 核心思想
通过系统地控制推理轨迹（reasoning trace）在训练和推理阶段的有无，设计四种实验配置来分离推理能力和 RLVR 训练范式的贡献。

### 方法框架

#### 实验设计
四种配置：
1. **训练时包含推理 + 推理时包含推理**（完整 RLVR）
2. **训练时包含推理 + 推理时不含推理**（仅训练时有推理）
3. **训练时不含推理 + 推理时包含推理**（仅推理时有推理）
4. **训练时不含推理 + 推理时不含推理**（纯 RLVR，无推理）

使用 Qwen3.5 4B 和 9B 模型，在 Swiss Legal Translation Benchmark 上评估。

#### 成本-质量分析
- 记录每种配置的输出 token 数量
- 绘制 Pareto 前沿：翻译质量 vs 推理成本
- 分析不同模型规模下的 tradeoff 差异

## 实验结果

### 实验设置
- **模型**：Qwen3.5 4B, Qwen3.5 9B
- **数据集**：SwiLTra-Bench (瑞士法律翻译基准)
- **评估指标**：BLEU, COMET
- **训练方法**：RLVR (GRPO)

### 主要结果
1. **推理在推理阶段包含时质量最优**：在所有配置中，推理时包含推理轨迹（thinking）的配置取得最佳翻译质量
2. **训练时去掉推理影响较小**：训练时是否包含推理对最终质量的影响小于推理时是否包含推理
3. **成本与质量正相关**：推理导致输出 token 增加 2-5 倍，但换来了显著的 BLEU/COMET 提升
4. **模型规模效应**：Qwen3.5 9B 的 thinking 效率高于 4B（单位 token 的质量提升更大）

## 深度分析

### 研究价值评估

#### 理论贡献
- **分离推理与训练范式的贡献**：首次通过控制实验分离了 RLVR 训练范式和推理能力对翻译质量的影响
- **成本-质量 tradeoff 的量化**：首次在 RLVR+NMT 场景下系统研究 token 成本与翻译质量的 Pareto 关系
- **实用指导价值**：为实际部署提供了"是否使用 thinking"的决策依据

#### 实际应用价值
- 对需要高质量翻译的场景（法律、医疗等），推荐启用推理
- 对成本敏感场景，可根据 Pareto 分析选择最优配置
- 小模型从推理中获益更大（相对提升）

### 局限性分析
- **仅测两个模型规模**：4B 和 9B，未覆盖更大模型
- **单一领域**：仅测试法律翻译，不一定泛化到其他领域
- **仅一种 RLVR 方法**：使用 GRPO，其他 RLVR 变体的行为可能不同
- **推理质量未评估**：未分析推理轨迹本身的正确性和有用性

### 适用性与场景分析
- **适用**：高质量要求场景（法律、金融翻译）、有推理预算的场景
- **不适用**：实时翻译、成本极度敏感的场景

## 我的综合评价

### 价值评分
**7.5/10** - 扎实的消融实验，首次量化 RLVR+NMT 中的 cost-quality tradeoff

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 系统分离了推理与训练范式的贡献，但实验设计较直接 |
| 技术质量 | 8/10 | 实验设计严谨，四种配置对照完备 |
| 实验充分性 | 7/10 | 模型规模和领域的覆盖有限 |
| 写作质量 | 8/10 | 清晰明了，结构合理 |
| 实用性 | 7/10 | 对实际部署有参考价值，但领域限制明显 |

> [!tip] 关键启示
> RLVR 的收益主要来自推理阶段的 reasoning trace，而非训练范式本身；推理带来的质量提升需要与增加的推理成本权衡。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 对于关注 RLVR 后训练和 NMT 的研究者值得一读，提供了清晰的消融分析。

## 相关论文
- [[20_Research/Papers/大语言模型/Reasoning_Before_Translation_Enhancing_Legal_Machine_Translation_with_Structured_Reasoning|Reasoning Before Translation]] - 同组工作，比较 SFT/RLVR 在法律翻译中的效果
