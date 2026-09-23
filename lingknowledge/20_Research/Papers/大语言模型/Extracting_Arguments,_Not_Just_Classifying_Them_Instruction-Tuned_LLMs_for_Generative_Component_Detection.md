---
date: "2026-09-23"
paper_id: "2609.24855"
title: "Extracting Arguments, Not Just Classifying Them: Instruction-Tuned LLMs for Generative Component Detection"
authors: "Sofiane Elguendouze, Erwan Hain, Elena Cabrio, Serena Villata"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Argument-Mining
  - Instruction-Tuning
quality_score: "7.6/10"
related_papers: []
created: "2026-09-23"
updated: "2026-09-23"
status: analyzed
---

# Extracting Arguments, Not Just Classifying Them: Instruction-Tuned LLMs for Generative Component Detection

## 核心信息
- **论文ID**：2609.24855
- **作者**：Sofiane Elguendouze, Erwan Hain, Elena Cabrio, Serena Villata
- **机构**：Université Côte d'Azur (UniCA), I3S, CNRS, MARIANNE 团队（法国）
- **发布时间**：2026-09-21
- **会议/期刊**：arXiv 预印本（cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2609.24855) | [PDF](https://arxiv.org/pdf/2609.24855)
- **引用**：--
- **代码**：论文中提到的 GitHub 仓库（公开）

## 摘要翻译

### 英文摘要
Argumentative component detection (ACD) is a core subtask of Argument(ation) Mining (AM) and one of its most challenging aspects, as it requires jointly delimiting argumentative spans and classifying them into components such as claims and premises. Most existing approaches formulate it as a simplified sequence labeling problem, component classification, or a pipeline of component segmentation followed by classification. In this paper, we propose ITFACD, a novel approach based on instruction-tuned Large Language Models (LLMs) using compact instruction-based prompts, and reframe ACD as a language generation task, enabling arguments to be identified directly from plain text without relying on pre-segmented components. Experiments on standard benchmarks show that our approach achieves higher performance compared to state-of-the-art systems.

### 中文翻译
论证组件检测（ACD）是论证挖掘（AM）的核心子任务，也是其中最具挑战性的方面之一，因为它需要联合地界定论证跨度（span），并将其分类为 claim（论点）与 premise（前提）等组件。现有方法大多将其简化为序列标注问题、组件分类问题，或"组件切分后接分类"的流水线。本文提出 ITFACD，一种基于指令微调大语言模型（LLM）的新方法，使用紧凑的指令式提示，并将 ACD 重构为语言生成任务，从而能够直接从纯文本中识别论证，无需依赖预切分的组件。在标准基准上的实验表明，该方法取得了优于当前最先进系统的性能。

### 核心要点提炼
- **研究背景**：ACD 是论证挖掘的核心但困难子任务，需同时完成 span 界定与组件分类。
- **研究动机**：现有方法把 ACD 简化成序列标注/分类/pipeline，割裂了"界定"与"分类"，且依赖预切分。
- **核心方法**：ITFACD——用 instruction-tuned LLM + 紧凑指令提示，把 ACD 重构为生成式任务。
- **主要结果**：在标准基准上超越 SOTA 系统。
- **研究意义**：首批将 ACD 完全建模为生成式任务的尝试，展示了指令微调对复杂 AM 子任务的潜力。

## 研究问题

### 核心研究问题
能否将论证组件检测（ACD）从传统的"序列标注/分类"范式，重构为"直接生成"范式，从而一次性完成论证 span 的界定与分类，并摆脱对预切分组件的依赖？

## 方法概述

### 核心思想
不再把 ACD 当作"标注/分类"的判别式任务，而是当作"文本生成"的生成式任务：给定一段纯文本，让 instruction-tuned LLM 直接生成其中论证组件的结构化表示（包含 span 与组件类型 claim/premise）。

### 方法框架

#### 整体架构
ITFACD 的核心是"紧凑指令提示 + 生成式解码"：

![[example_prompt_page1.png|800]]

> 图1：ITFACD 的 prompt 示例，展示如何用紧凑的指令引导 LLM 直接从纯文本中生成论证组件。

#### 各模块详细说明

**模块1：指令构建（Instruction Prompt）**
- **功能**：将 ACD 任务描述为"从文本中提取论证组件"的生成指令。
- **特点**：使用紧凑（compact）的指令式提示，避免冗长上下文，降低推理成本。
- **关键点**：无需预切分组件，输入即原始纯文本。

**模块2：生成式组件检测（Generative Component Detection）**
- **功能**：LLM 按指令直接输出论证组件及其类型（claim / premise）。
- **关键点**：同时完成 span 界定与组件分类，二者联合建模。

### 关键创新

1. **范式重构**——把 ACD 从判别式（标注/分类）转为生成式任务，简化了任务建模。
2. **紧凑指令提示**——用精简的 instruction 引导 LLM，兼顾效果与效率。
3. **无需预切分**——直接从纯文本识别论证，去掉了 pipeline 的中间依赖。

## 实验结果

### 数据集 / 基准
- 标准 ACD 基准数据集（论文未在摘要中详列，需查看正文）。

### 主要结果
- 在标准基准上取得 **优于 SOTA 系统** 的性能。

### 实验结论
生成式范式在 ACD 上可行且更优，instruction tuning 是解决复杂论证挖掘子任务的有效路径。

## 深度分析

### 研究价值
- **理论贡献**：为 ACD 提供了"生成式建模"的新视角，摆脱了传统序列标注/分类范式的约束。
- **实际应用**：可简化论证挖掘系统的构建流程，直接端到端地从文本提取论证结构。
- **领域影响**：为论证挖掘其他子任务（如关系分类、论证评价）的生成式改造提供了范本。

### 优势
- 范式简洁，端到端，无需预切分。
- 紧凑提示降低推理开销。
- 首批把 ACD 完全生成式建模，具有一定开创性。

### 局限性
- 摘要信息有限，具体基准、基线与指标需查看正文。
- ACD 属于较垂直的 NLP 子任务，通用影响力有限。
- 生成式输出的结构化约束（如 span 边界精确性）需进一步保证。

### 适用场景
- 论证挖掘、辩论分析、观点挖掘等需要提取论证结构的场景。

## 与相关论文对比

### [[序列标注式 ACD 方法]] - 对比关系
- **差异**：传统方法把 ACD 当作序列标注，本文重构为生成式。
- **改进**：联合建模 span 界定与分类，端到端。

### [[pipeline 式 ACD 方法]] - 对比关系
- **差异**：pipeline 先切分再分类，本文一步生成。
- **改进**：消除误差传播与预切分依赖。

## 技术路线定位

本文属于 **论证挖掘（Argument Mining）+ 生成式 LLM** 技术路线，主要关注**ACD 的生成式重构**这一子方向。

```
序列标注 ACD → pipeline ACD → 生成式 ACD（本文 ITFACD）→ 未来方向
```

## 未来工作建议

1. 扩展到论证关系分类、论证质量评价等其他 AM 子任务。
2. 增强生成式输出的结构化约束（span 边界精确性）。
3. 在多语言、跨领域语料上验证泛化性。

## 我的综合评价

### 价值评分
- **总体评分**：**7.6/10** —— 范式转换有启发意义，但属较垂直子任务，且摘要信息有限。
- **分项评分**：
  - 创新性：7/10 —— 生成式重构 ACD 有新意，但并非颠覆性突破。
  - 技术质量：8/10 —— 方法清晰、工程合理。
  - 实验充分性：7/10 —— 声称超 SOTA，但摘要缺具体数字。
  - 写作质量：8/10 —— 表述清晰。
  - 实用性：7/10 —— 论证挖掘场景可用，通用性有限。

### 突出亮点
- "生成式 vs 判别式"的范式对比视角。
- 紧凑指令提示的工程实践。

### 重点关注
- 正文中的具体基准、基线与指标数值。
- 生成式输出如何保证 span 边界的精确性。

### 可借鉴点
- 把判别式子任务重构为生成式任务的思路。
- 紧凑指令提示在垂直任务上的应用。

### 批判性思考
- 摘要未给出具体性能数字，"优于 SOTA"的说服力需正文支撑。
- 生成式方法在精确 span 界定上可能存在幻觉风险。

## 我的笔记

[用户阅读后手动补充的内容]

## 相关论文
- [[序列标注式 ACD 方法]] - 传统判别式方法
- [[pipeline 式 ACD 方法]] - 分阶段处理方法

## 外部资源
- arXiv：https://arxiv.org/abs/2609.24855
- 代码：论文中提到的 GitHub 仓库
