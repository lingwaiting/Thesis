---
date: "2026-11-02"
paper_id: "2611.04079"
title: "AlgoPilot: Cross-Paradigm Reasoning in Language Models via Strategy Selection and Guidance"
authors: "Yilun Hao, Ruixiao Yang, Hudson Hilal, Yongchao Chen, Kaizhi Qian, Yada Zhu, Yang Zhang, Chuchu Fan"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 推理
  - 强化学习
  - RAG
quality_score: "9/10"
related_papers: []
created: "2026-11-04"
updated: "2026-11-04"
status: analyzed
---

# AlgoPilot: Cross-Paradigm Reasoning in Language Models via Strategy Selection and Guidance

## 核心信息
- **论文ID**：2611.04079
- **作者**：Yilun Hao, Ruixiao Yang, Hudson Hilal, Yongchao Chen, Kaizhi Qian, Yada Zhu, Yang Zhang, Chuchu Fan
- **机构**：--
- **发布时间**：2026-11-02
- **会议/期刊**：cs.AI / cs.CL / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2611.04079) | [PDF](https://arxiv.org/pdf/2611.04079)
- **项目页**：https://sites.google.com/view/algopilot

## 研究问题

真实世界问题横跨程序合成、符号推理、规划、优化等多个领域，往往需要**根本不同的求解范式**（如搜索、动态规划、形式化验证等）。LLM 推理面临的核心挑战有二：一是**识别合适的求解方法**，二是**正确地形式化问题**。现有提示方法依赖固定的推理模板（如 CoT、ToT），无法根据输入自适应地选择与结构化求解策略。

**核心问题**：如何让 LLM 学会根据输入问题自适应地选择求解范式并生成结构化的问题建模，而不是套用固定推理模板？

## 方法概述

### 核心方法

AlgoPilot 是一个跨范式推理框架，包含三个组件：

1. **SteerLM**（SFT + RL 训练）
   - 根据输入问题，自适应选择求解策略（范式与模式）。
   - 作为"策略选择器"，决定用什么方法、以什么模式去解。

2. **GuideLM**（SFT 训练）
   - 为所选策略生成**定制化的结构化问题建模引导**。
   - 把问题的形式化方式"教"给下游执行模型。

3. **ExecutionLM**
   - 同时条件于「所选策略」与「其建模」来生成最终解。
   - 与提示方法不同，它学到的是自适应选择并结构化策略，而非套模板。

### 关键机制
- 三组件分工：**选择（Steer）→ 引导（Guide）→ 执行（Execute）** 的流水线，把"识别方法"与"形式化问题"两个难点显式解耦。
- 策略选择模块（SteerLM）与建模模块（GuideLM）可**跨执行模型迁移**，即训练一次可提升其他 LLM 的推理能力。

## 关键创新

1. **可训练的策略选择**：用 SFT+RL 让模型学习"何时用何种范式"，而非人工预设推理模板。
2. **策略-建模-执行三阶段解耦**：把跨范式推理拆成可独立训练、可迁移的模块。
3. **模块可迁移性**：Steer/Guide 模块可迁移到不同执行模型，具备通用提升潜力。

## 实验结果

- **主结果**：基于 Qwen3-8B，在 5 类问题、13 个基准、66 个独特领域上，把平均准确率从 38.4% 提升到 **72.9%**。
- **对比大型模型**：超过最强大型 LLM 提示基线 GLM-5 达 **11.7%**。
- **消融实验**：确认了 SteerLM、GuideLM、ExecutionLM 每个组件的有效性。
- **迁移性**：学习到的 steering 与 formulation 模块可跨执行模型迁移。

## 深度分析

**评价**：这篇工作把"跨范式推理"从一个提示工程问题升级为一个可训练的模块化框架，思路清晰、实验扎实。三组件分工（选策略、做建模、去执行）对应了人类求解复杂问题时的认知流程，具备很好的可解释性与工程落地价值。

**与相关工作的区别**：相比 ToT/CoT 等固定模板的提示方法，AlgoPilot 的核心区别在于**策略选择与问题建模是学出来的、输入自适应的**，而非预定义的。相比纯 RL 推理（如 R1 类方法），它显式地把"求解范式"作为中间表示引入。

**局限与展望**：三组件流水线在推理时引入额外开销；SteerLM/GuideLM 本身仍需训练数据与 RL 环境。未来可探索在更大规模、更多范式类别上验证，以及进一步挖掘模块迁移的通用性。
