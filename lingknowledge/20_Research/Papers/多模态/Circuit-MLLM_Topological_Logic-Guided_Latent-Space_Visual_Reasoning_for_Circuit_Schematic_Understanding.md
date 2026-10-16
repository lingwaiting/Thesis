---
date: "2026-09-16"
paper_id: "arXiv:2609.15668"
title: "Circuit-MLLM: Topological Logic-Guided Latent-Space Visual Reasoning for Circuit Schematic Understanding"
authors: "Jinyuan Deng, Yuqi Jiang, Wenjing Huang, Xin Li, Qi Sun, Cheng Zhuo"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态技术
  - MLLM
  - 电路原理图理解
  - 视觉推理
  - 拓扑逻辑
quality_score: "8.5/10"
created: "2026-09-16"
updated: "2026-09-16"
status: analyzed
---

# Circuit-MLLM: Topological Logic-Guided Latent-Space Visual Reasoning for Circuit Schematic Understanding

## 核心信息
- **论文ID**：arXiv:2609.15668
- **作者**：Jinyuan Deng, Yuqi Jiang, Wenjing Huang, Xin Li, Qi Sun, Cheng Zhuo
- **机构**：--
- **发布时间**：2026-09-14
- **会议/期刊**：cs.CV / cs.AI / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2609.15668) | [PDF](https://arxiv.org/pdf/2609.15668)
- **代码**：https://github.com/IC-Yuan/Circuit-MLLM

## 摘要翻译

### 英文摘要
Through pre-training on extensive text and image datasets, current multi-modal large language models (MLLMs) achieve strong performance on general tasks. However, circuit schematics present a unique challenge for MLLMs due to their dense component layouts and distinct topological logic, demanding fine-grained structural parsing to extract the electrical semantics. To address this, we propose Circuit-MLLM, a multimodal reasoning framework that reformulates circuit topology analysis as a process of device localization, path tracing, and sequential reasoning within the latent space. We introduce a circuit knowledge mining mechanism that deeply aligns the model's latent representations with structurally rich features derived from multi-granularity circuit vision experts, enabling the model to effectively internalize topological semantics. Building upon these internalized semantics, we devise a topology-guided sequencing strategy that decouples reasoning from the rigid raster-scan order, enforcing stepwise inference along the circuit's topological logic in latent space. Across diverse circuit analysis tasks, Circuit-MLLM consistently outperforms strong baselines, notably achieving a 25% higher average score than GPT-5.1.

### 中文翻译
通过对大规模文本与图像数据的预训练，现有多模态大语言模型（MLLM）在通用任务上表现强劲。然而，电路原理图因其密集的元件布局与独特的拓扑逻辑，对 MLLM 构成独特挑战，要求精细的结构解析以提取电气语义。为此，作者提出 Circuit-MLLM，一个多模态推理框架，将电路拓扑分析重新表述为潜在空间中的「器件定位 → 路径追踪 → 顺序推理」过程。其引入电路知识挖掘机制，将模型的潜在表征与来自多粒度电路视觉专家的结构丰富特征深度对齐，使模型有效内化拓扑语义；在此基础上，设计拓扑引导的顺序策略，将推理与僵化的光栅扫描顺序解耦，强制沿电路拓扑逻辑在潜在空间中进行逐步推理。在多种电路分析任务上，Circuit-MLLM 持续超越强基线，平均得分比 GPT-5.1 高出 25%。

### 核心要点提炼
- **研究背景**：MLLM 在通用视觉-语言任务上成熟，但在电路原理图理解这一专业领域表现不佳。
- **研究动机**：电路图的密集布局 + 拓扑逻辑，与自然图像的栅格扫描顺序不匹配。
- **核心方法**：电路知识挖掘（对齐潜在表征与电路视觉专家特征）+ 拓扑引导排序（按拓扑逻辑而非光栅顺序推理）。
- **主要结果**：平均得分比 GPT-5.1 高 25%。
- **研究意义**：为专业领域（EDA/电路）的多模态推理提供了「领域语义内化」的新范式。

## 研究背景与动机

### 领域现状
多模态大语言模型（如 GPT-4V、Qwen-VL 等）通过大规模图文预训练，在通用视觉问答、图像理解、OCR 等任务上已接近或超越人类水平。它们通常采用统一的视觉编码器 + LLM 架构，将图像切成 patch 后按栅格扫描（raster-scan）顺序输入。

### 现有方法的局限性
电路原理图与自然图像存在本质差异：
1. **密集元件布局**：电阻、电容、晶体管、连线等元件高度密集、尺度不一。
2. **拓扑逻辑优先**：电路的电气语义（电流路径、节点连接、环路）由拓扑关系决定，而非像素空间位置。
3. **光栅扫描不匹配**：MLLM 默认的光栅扫描顺序无法捕捉「沿导线追踪路径」这类需要拓扑遍历的推理。
因此，通用 MLLM 直接用于电路图时，往往只看到「一堆符号」，无法提取电气语义。

### 研究动机
需要一个框架，让 MLLM 学会「电路图的结构化理解」，将拓扑关系内化到潜在表征中，并按拓扑逻辑（而非像素顺序）进行推理。

## 研究问题

### 核心研究问题
如何让多模态大语言模型对电路原理图执行**拓扑感知的潜在空间推理**，从而在器件定位、路径追踪、电路分析等任务上超越通用 MLLM？

## 方法概述

### 核心思想
把电路拓扑分析拆解为三个潜在空间内的子过程——**器件定位（device localization）→ 路径追踪（path tracing）→ 顺序推理（sequential reasoning）**，并通过「电路知识挖掘」把电路的拓扑语义注入模型表征，通过「拓扑引导排序」改变推理的遍历顺序。

### 方法框架

#### 整体架构

![[overall.png|800]]

> 图1：Circuit-MLLM 整体架构。由多粒度电路视觉专家提取结构丰富特征，经电路知识挖掘机制与 MLLM 潜在表征对齐，再通过拓扑引导排序策略实现沿拓扑逻辑的逐步推理。

#### 各模块详细说明

**模块1：多粒度电路视觉专家（Multi-Granularity Circuit Vision Experts）**
- **功能**：从不同粒度提取电路图的结构化特征（器件级、连线级、拓扑级）。
- **输入**：电路原理图图像。
- **输出**：结构丰富（structurally rich）的特征表示。
- **关键技术**：多个专门化的视觉编码器，分别关注器件识别、连线提取、节点/环路拓扑。

**模块2：电路知识挖掘机制（Circuit Knowledge Mining）**
- **功能**：将 MLLM 的潜在表征与电路视觉专家特征深度对齐，使模型内化拓扑语义。
- **输入**：MLLM 的中间层表征 + 视觉专家特征。
- **输出**：对齐后的、蕴含电路拓扑语义的潜在表征。
- **关键技术**：表征对齐（representation alignment），本质是一种领域知识蒸馏。

**模块3：拓扑引导排序策略（Topology-Guided Sequencing）**
- **功能**：将推理顺序从僵化的光栅扫描解耦，改为沿电路拓扑逻辑逐步推理。
- **输入**：内化了拓扑语义的潜在表征。
- **输出**：按拓扑逻辑排列的推理序列。
- **关键技术**：在潜在空间中强制 stepwise inference，遵循「器件定位 → 路径追踪 → 顺序推理」的因果链。

### 方法架构图

![[motivation.png|800]]

> 图2：动机示意——光栅扫描顺序 vs 拓扑逻辑顺序的差异，说明为何需要拓扑引导的推理序列。

## 实验结果

### 实验目标
验证 Circuit-MLLM 在电路分析任务上相对通用 MLLM 基线的优势，以及各模块（知识挖掘、拓扑排序）的贡献。

### 数据集
电路分析任务（circuit analysis tasks），涵盖器件定位、路径追踪、电路功能理解等多种子任务。

### 主要结果

#### 主实验结果
Circuit-MLLM 在多种电路分析任务上持续超越强基线，**平均得分比 GPT-5.1 高出 25%**。

#### 结果分析
- 相比 GPT-5.1 等通用 MLLM，Circuit-MLLM 的领先主要来自两个方面：电路知识挖掘（让模型真正「看懂」拓扑）与拓扑引导排序（让模型按正确顺序推理）。
- 该框架证明了「领域语义内化 + 推理顺序重构」在专业视觉领域（EDA、电路）的有效性。

### 实验结果图

![[bench.png|800]]

> 图3：电路分析任务上的评测结果对比。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出「将电路拓扑分析重新表述为潜在空间中的定位-追踪-推理过程」这一范式，为结构化专业图（电路、原理图、流程图）的 MLLM 理解提供了新思路。
- 引入「电路知识挖掘」实现领域语义内化，可迁移到其他专业视觉领域。

#### 实际应用价值
- **EDA 自动化**：辅助电路设计验证、原理图理解、逆向工程。
- **智能硬件调试**：自动解析电路故障。
- **教育**：电路原理图的自动讲解。

### 方法优势详解

#### 优势1：拓扑语义内化
- **描述**：通过知识挖掘把电路拓扑注入潜在表征，而非仅在输入层面提供 patch。
- **技术基础**：多粒度视觉专家 + 表征对齐。
- **实验验证**：相对 GPT-5.1 提升 25% 平均得分。

#### 优势2：推理顺序重构
- **描述**：拓扑引导排序打破了通用 MLLM 的光栅扫描先验，适配拓扑遍历式推理。

### 局限性分析

#### 局限1：领域专门化程度
- **描述**：方法高度针对电路图，泛化到其他结构化图需重新设计视觉专家与拓扑逻辑。

#### 局限2：依赖专家特征质量
- **描述**：电路知识挖掘效果受限于多粒度视觉专家提取特征的质量。

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** - 在 MLLM 专业领域理解这一前沿方向给出清晰可复现的方案，兼具创新性与工程价值。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 「知识内化 + 拓扑排序」组合创新，定位清晰 |
| 技术质量 | 8/10 | 方法结构完整，机理清晰 |
| 实验充分性 | 7/10 | 有基线对比与 25% 提升，但数据集细节较少 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 9/10 | EDA 自动化场景明确，代码开源 |

## 相关论文

### 直接相关
- [[BLINDSPOT_A_Benchmark_for_Safety_and_Refusal_Calibration_in_Long-Horizon_Tool-Using_Agents|BLINDSPOT]] - 同为多模态/智能体安全评测方向

> [!tip] 关键启示
> 专业领域（EDA/电路）的 MLLM 理解，关键不在更大的模型，而在于「把领域拓扑语义内化进表征」+「重构推理顺序」这两点。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 对多模态专业领域理解、EDA 自动化方向感兴趣的读者推荐阅读。
