---
date: "2026-07-21"
paper_id: "arXiv:2607.19181"
title: "Reasoning Before Translation: Enhancing Legal Machine Translation with Structured Reasoning"
authors: "Aixiu An, Michael Jungo, Eloi Eynard, Mark Drenhaus, Andreas Fischer, Jean Hennebert, Sebastien Rumley"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM
  - RLVR
  - Neural-Machine-Translation
  - Reasoning
  - Legal-Translation
  - SFT
  - GRPO
quality_score: "7.5/10"
created: "2026-07-23"
updated: "2026-07-23"
status: analyzed
---

# Reasoning Before Translation: Enhancing Legal Machine Translation with Structured Reasoning

## 核心信息
- **论文ID**：arXiv:2607.19181
- **作者**：Aixiu An, Michael Jungo, Eloi Eynard, Mark Drenhaus, Andreas Fischer, Jean Hennebert, Sebastien Rumley
- **机构**：iCoSys, HES-SO; AIBEX, University of Fribourg; Switzerland
- **发布时间**：2026-07-21
- **会议/期刊**：arXiv 预印本
- **链接**：[arXiv](http://arxiv.org/abs/2607.19181v1) | [PDF](https://arxiv.org/pdf/2607.19181v1)
- **代码**：https://github.com/aixiuxiuxiu/Legal-MT-SFT-RL
- **评分**：9.27 (start-my-day 推荐评分)

## 摘要翻译

### 英文摘要
Neural machine translation (NMT) in the legal domain is a linguistically and conceptually demanding task, primarily due to the complexity of legal language and the high level of precision it requires. The recent emergence of reasoning-capable language models opens new possibilities for tackling such challenges. They add to a set of other previously proposed techniques to enhance the translation quality, which includes supervised fine-tuning and reinforcement learning. In this work, we perform a comparison between these various approaches. More particularly, we evaluate small language models such as Qwen3.5 4B, Qwen3.5 9B, and Gemma 3 12B enhanced with various re-training paradigms and compare their performances against frontier reasoning models. We focus on the Swiss legal system, which -- with its unique multilingual statutes -- offers a particularly challenging testbed for reasoning-augmented models. Our results show that the quality of small base models can be greatly enhanced, and that reinforcement learning with verifiable rewards can be applied to NMT in the legal domain and surpasses the translation quality of supervised fine-tuning. The performance of enhanced small models is close to the one of state-of-the-art reasoning models yet remains inferior. We also note that re-training paradigms yield diminishing returns as model size increase.

### 中文翻译
法律领域的神经机器翻译在语言和概念上都是一项要求极高的任务，主要因为法律语言的复杂性和对高精度的要求。最近出现的具有推理能力的语言模型为解决此类挑战开辟了新的可能性。它们加入了一组先前提出的其他技术来增强翻译质量，包括监督微调和强化学习。在这项工作中，我们对这些不同方法进行了比较。特别地，我们评估了经过各种重训练范式增强的小型语言模型（如 Qwen3.5 4B、Qwen3.5 9B 和 Gemma 3 12B），并将它们与前沿推理模型进行性能比较。我们聚焦于瑞士法律体系——其独特的多语言法规为增强推理模型提供了特别具有挑战性的测试平台。我们的结果表明，小型基础模型的质量可以大幅提升，带可验证奖励的强化学习可以应用于法律领域的 NMT，并且超越监督微调的翻译质量。增强后的小型模型性能接近但略低于 SOTA 推理模型。我们还注意到，重训练范式的收益随模型规模增大而递减。

### 核心要点提炼
- **研究背景**：法律 NMT 对精度要求极高，推理能力可能成为突破点
- **研究动机**：系统比较 SFT、RLVR 等不同后训练范式在法律翻译中的效果
- **核心方法**：对 Qwen3.5 和 Gemma 3 等小模型使用 SFT/RLVR 多种后训练方式，与前沿推理模型对比
- **主要结果**：RLVR > SFT；小模型增强后接近但未超越大推理模型；收益随规模递减
- **研究意义**：为法律 NMT 的后训练策略选择提供了系统对比

## 方法概述

### 核心思想
利用多种后训练范式（SFT、RLVR/GRPO）增强小型开源模型的法律翻译能力，并在瑞士多语言法律体系这一难度较大的测试平台上与前沿推理模型进行全面对比。

### 实验设计
- **模型**：Qwen3.5 4B, Qwen3.5 9B, Gemma 3 12B
- **训练范式**：SFT（监督微调）、RLVR（GRPO）
- **对比对象**：前沿推理模型（如 DeepSeek-R1 等）
- **测试平台**：SwiLTra-Bench（瑞士法律翻译基准，多语言）

## 实验结果

### 主要发现
1. **RLVR > SFT**：RLVR（GRPO）在法律 NMT 中显著优于监督微调
2. **小模型可大幅增强**：经过后训练的小模型翻译质量大幅提升，接近大推理模型
3. **小模型未超越大模型**：增强后的小模型接近但略低于 SOTA 推理模型
4. **收益递减**：后训练范式的收益随模型规模增大而递减——小模型从 RLVR 中获益最大

## 深度分析

### 研究价值
- **首次系统对比**：法律 NMT 中 SFT vs RLVR 的首次系统比较
- **实用指导意义**：对资源受限场景（只能用小型模型）提供了明确的后训练策略建议
- **瑞士多语言场景**：为多语言法律系统提供了有价值的 benchmark 数据

### 局限性
- **仅小型模型**：未测试更大模型（如 70B+）的后训练效果
- **单一 RLVR 方法**：仅使用 GRPO，其他 RL 变体可能有不同表现
- **领域特定**：结论是否适用于其他翻译领域未验证
- **与论文 #1 互补**：两篇论文来自同一团队且同一天发布，可对照阅读

## 与相关论文对比
- [[20_Research/Papers/大语言模型/The_Price_of_Reasoning_Cost-Quality_Tradeoffs_in_Reinforcement_Learning_for_Neural_Machine_Translation|The Price of Reasoning]] - 同团队工作，聚焦于推理的成本-质量 tradeoff，本文则聚焦于不同后训练范式的比较

## 我的综合评价

**7.5/10** - 扎实的系统对比研究，为法律 NMT 的后训练策略提供了清晰指导

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 系统对比而非新方法，但系统性和全面性值得肯定 |
| 技术质量 | 8/10 | 实验设计严谨，多模型、多范式对比 |
| 实验充分性 | 7/10 | 模型规模和领域覆盖可扩大 |
| 写作质量 | 8/10 | 结构清晰，结果呈现直观 |
| 实用性 | 7/10 | 为法律翻译实践提供有价值的参考 |

> [!tip] 关键启示
> RLVR 是法律 NMT 的首选后训练方法，且小型模型从中获益最大——为资源受限的场景提供了明确指引。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 对法律 NMT 和 RLVR 后训练研究者值得一读，与同团队的另一篇论文配合阅读效果更佳。
