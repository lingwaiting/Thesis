---
date: "2026-10-03"
paper_id: "arXiv:2610.02117"
title: "Where-OPD: Spatially Guided On-Policy Self-Distillation of MLLMs with Synthetic Scenes"
authors: "Sophia Sirko-Galouchenko, Monika Wysoczańska, Andrei Bursuc, Nicolas Thome, Spyros Gidaris"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态
  - Vision-Language
  - On-Policy-Self-Distillation
  - MLLM
  - Synthetic-Data
quality_score: "9.0/10"
related_papers: []
created: "2026-10-03"
updated: "2026-10-03"
status: analyzed
---

# Where-OPD: Spatially Guided On-Policy Self-Distillation of MLLMs with Synthetic Scenes

## 核心信息
- **论文ID**：arXiv:2610.02117
- **作者**：Sophia Sirko-Galouchenko, Monika Wysoczańska, Andrei Bursuc, Nicolas Thome, Spyros Gidaris
- **机构**：Valeo.ai / Sorbonne Université, CNRS, ISIR / Institut universitaire de France (IUF) / ILLS, CNRS, Montreal
- **发布时间**：2026-10-01
- **会议/期刊**：ICLR 2027 投稿（分类 cs.CV, cs.AI, cs.CL, cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.02117) | [PDF](https://arxiv.org/pdf/2610.02117)
- **项目页**：https://github.com/sirkosophia/Where-OPD

## 摘要翻译

### 英文摘要
On-policy self-distillation has recently emerged as an effective approach for improving language-model reasoning by supervising students with a frozen or EMA version of themselves that receives privileged information. Its application to multimodal large language models (MLLMs), however, remains largely unexplored. Recent approaches use privileged visual information, such as image crops corresponding to a question, to improve fine-grained perception, but their gains are confined to tasks that benefit from such visual zooming and require either human-annotated grounding data or external teacher models. We introduce a different form of on-policy self-distillation for MLLMs that provides the teacher with textual, spatially grounded guidance identifying the visual elements relevant to a query. We use procedurally generated scenes with automatically available object identities and spatial coordinates, enabling scalable and annotation-free post-training. The teacher uses this spatial guidance to locate and integrate evidence from multiple relevant image regions, while the student learns to reproduce the resulting behavior from the image and question alone. Our approach consistently improves performance on counting, document and chart understanding benchmarks across multiple models.

### 中文翻译
On-policy 自蒸馏近来已成为提升语言模型推理能力的有效方法：用接收了特权信息的"冻结版/EMA 版"自身来监督学生模型。但其在多模态大语言模型（MLLM）上的应用仍几乎未被探索。近期方法使用特权视觉信息（如与问题对应的图像裁剪）来提升细粒度感知，但它们的增益局限于受益于这种视觉缩放的任务，且需要人工标注的 grounding 数据或外部教师模型。本文提出一种不同形式的 MLLM on-policy 自蒸馏：为教师提供文本化的、空间 grounded 的引导，标识与查询相关的视觉元素。作者使用程序化生成的场景，其物体身份与空间坐标可自动获取，从而支持可扩展且无需标注的后训练。教师利用这种空间引导定位并整合多个相关图像区域的证据，学生则学会仅从图像和问题本身复现这种行为。该方法在多个模型上一致提升了计数、文档与图表理解基准的性能。

### 核心要点提炼
- **研究背景**：On-policy 自蒸馏在 LLM 推理上有效，但 MLLM 上的应用尚未被探索；现有 MLLM 特权视觉信息方法局限于"视觉缩放"类任务。
- **研究动机**：用**文本化的空间引导**（而非图像裁剪）作为特权信息，避免对人工标注 grounding 数据或外部教师的依赖。
- **核心方法**：Where-OPD —— 程序化生成合成场景，教师用空间引导定位并整合多区域证据，学生从图像+问题复现该行为。
- **主要结果**：合成场景后训练即可迁移到真实世界感知基准，CVBench、V*、ZoomBench、BLINK、HR-Bench、MME-RealWorld 六个基准平均提升 **3.23 分**。
- **研究意义**：证明空间 grounded 特权信息能通过 on-policy 自蒸馏诱导更广泛的感知能力，实现超越任务与数据分布的 synthetic-to-real 迁移。

## 研究背景与动机

### 领域现状
- On-policy 自蒸馏（self-distillation）是近期提升 LLM 推理的主流技术：学生模型由一个"冻结/EMA 版本"的自身监督，而该教师版本在训练时接收**特权信息**（privileged information）。
- 在 MLLM 场景中，已有工作用特权视觉信息（例如与问题对应的图像 crops）提升细粒度感知。

### 现有方法的局限性
- 增益**局限于**"视觉缩放"（visual zooming）类任务——只有需要局部放大观察的任务才受益。
- 需要**人工标注的 grounding 数据**，或依赖**外部教师模型**，扩展性差。

### 研究动机
能否用**文本化、空间 grounded 的特权信息**替代视觉裁剪，使 on-policy 自蒸馏在更广泛感知任务上受益，同时保持**可扩展、无需标注**的后训练？

## 研究问题

### 核心研究问题
如何为 MLLM 设计一种不依赖视觉裁剪与人工标注的 on-policy 自蒸馏范式，通过空间 grounded 的文本引导诱导出可泛化到真实世界的感知能力？

## 方法概述

### 核心思想
把特权信息从"视觉裁剪"换成"文本化的空间引导"：教师模型被告知与查询相关的视觉元素及其位置（物体身份 + 空间坐标），从而能主动定位并整合多个相关图像区域的证据；学生模型只能看到原始图像与问题，被迫学习复现教师的感知行为，从而内化出更强的细粒度感知能力。

### 方法框架

#### 整体架构
![[2610.02117_page1.png|600]]

> 图1：Where-OPD 方法总览。教师模型（冻结/EMA 版）在训练时接收文本化的空间 grounded 引导（物体身份与空间坐标），据此定位并整合多个相关图像区域；学生模型仅从原始图像与问题学习，复现教师行为。

- **合成场景生成**：使用程序化生成的场景，自动获得物体身份与空间坐标，实现可扩展、零标注的后训练数据。
- **教师（Teacher）**：接收空间引导，定位并整合多个相关图像区域的证据，产生高质量的感知行为。
- **学生（Student）**：仅从图像和问题本身学习，通过 on-policy 自蒸馏复现教师的行为。

### 关键创新
1. **文本空间引导作为特权信息** —— 用物体身份+空间坐标替代视觉裁剪，不依赖人工 grounding 数据或外部教师模型。
2. **零标注合成后训练** —— 程序化场景使数据生成可扩展，突破人工标注瓶颈。
3. **Synthetic-to-Real 迁移** —— 仅用合成场景后训练即可在真实世界感知基准上稳定提升，证明空间 grounded 特权信息能诱导更广泛的感知能力。

## 实验结果

### 数据集
- **后训练**：程序化生成的合成场景（仅合成数据）
- **评估**：CVBench、V*、ZoomBench、BLINK、HR-Bench、MME-RealWorld（真实世界感知基准），以及计数、文档、图表理解基准

### 主要结果
- 在**多个模型**上一致提升计数、文档与图表理解基准的性能。
- 尽管后训练只用合成场景，迁移到真实世界感知基准平均提升 **3.23 分**（CVBench、V*、ZoomBench、BLINK、HR-Bench、MME-RealWorld）。

## 深度分析

### 研究价值
- **理论贡献**：首次系统性地将"文本空间引导"作为 MLLM on-policy 自蒸馏的特权信息，并验证其能诱导出超越任务与数据分布的感知能力。
- **实际应用**：为 MLLM 细粒度感知（计数、文档理解、图表理解）提供了一种低成本、可扩展的后训练方案。
- **领域影响**：为 synthetic-to-real 迁移在 MLLM 感知上的可行性提供了有力证据。

### 优势
- 无需人工标注 grounding 数据与外部教师模型，后训练可扩展性强。
- 增益不局限于视觉缩放类任务，泛化到计数、文档、图表等多样任务。
- 合成→真实迁移显著，实用价值高。

### 局限性
- 依赖程序化场景的物体身份与坐标标注，复杂真实场景的合成保真度仍是隐忧。
- 空间引导的粒度与格式对最终效果的影响尚未充分消融。

### 未来工作
- 探索更复杂、更贴近真实的合成场景，进一步缩小 synthetic-to-real 差距。
- 将空间 grounded 特权信息扩展到视频理解、具身智能等多模态任务。
