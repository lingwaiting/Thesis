---
date: "2026-11-08"
paper_id: "arXiv:2611.08216"
title: "ProQuant: Progressive Quantization-aware Training for Edge MLLMs"
authors: "Yufei Xue, Yushi Huang, Jiawei Shao, Pingcheng Dong, Yonghao Tan, Shiyao Li, Kwang-Ting Cheng, Jun Zhang"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - 量化
  - MLLM
  - Quantization-Aware-Training
  - Edge-Deployment
quality_score: "8.5/10"
created: "2026-11-08"
updated: "2026-11-08"
status: analyzed
---

# ProQuant: Progressive Quantization-aware Training for Edge MLLMs

## 核心信息
- **论文ID**：arXiv:2611.08216
- **作者**：Yufei Xue, Yushi Huang, Jiawei Shao, Pingcheng Dong, Yonghao Tan, Shiyao Li, Kwang-Ting Cheng, Jun Zhang
- **机构**：--
- **发布时间**：2026-11-05
- **分类**：cs.CV, cs.CL
- **链接**：[arXiv](https://arxiv.org/abs/2611.08216) | [PDF](https://arxiv.org/pdf/2611.08216)
- **代码**：https://github.com/yufeixue-ai/ProQuant/

## 摘要翻译

### 英文摘要
Multi-modal large language models (MLLMs) with impressive perception and understanding capabilities will enable a range of downstream applications. However, their substantial computational and memory requirements hinder the real-world implementation, particularly for resource-constrained edge devices. Quantization has emerged as an effective solution for deploying large models. While post-training quantization (PTQ) successfully retains the performance at moderate bit-width (e.g., W8A8), it suffers from severe performance degradation under low-bit quantization (e.g., W4A8), especially for small-scale models that are more suitable for edge deployment. In this paper, we propose ProQuant, a novel weight-activation quantization-aware training (QAT) framework tailored for low-bit MLLMs.

### 中文翻译
多模态大语言模型（MLLM）凭借出色的感知与理解能力，将支撑一系列下游应用。然而，其庞大的计算与内存开销阻碍了真实落地，尤其在资源受限的边缘设备上。量化已成为部署大模型的有效手段。虽然训练后量化（PTQ）在中位宽（如 W8A8）下能保留性能，但在低位宽（如 W4A8）下性能严重退化，尤其对于更适合边缘部署的小规模模型。本文提出 ProQuant，一个专为低位宽 MLLM 设计的权重-激活量化感知训练（QAT）框架。

### 核心要点提炼
- **研究背景**：MLLM 边缘部署受限于计算/内存，低位宽量化下 PTQ 性能崩坏。
- **研究动机**：小模型在 W4A8 下性能下降严重，需要针对性的 QAT 而非通用 PTQ。
- **核心方法**：自重标注（SRL）对齐微调与预训练分布 + 渐进式块激活（PBA）优先恢复多模态理解。
- **主要结果**：W4A8 Qwen3-VL-2B-Instruct 平均准确率提升 6.6%，仅约 1% 性能损失即逼近全精度。
- **研究意义**：为边缘 MLLM 提供了一种可落地的低位宽量化训练方案。

## 研究背景与动机

### 领域现状
MLLM 在多模态理解上取得显著进展，但推理成本高，难以在移动/边缘设备部署。量化是主流压缩手段，其中 PTQ 无需训练、成本低，但只在中位宽（W8A8）有效。

### 现有方法的局限性
- PTQ 在 W4A8 等低位宽下性能骤降，且对"更适合边缘的小模型"尤其不友好。
- 直接使用通用 QAT 微调时，微调数据分布与预训练数据分布不匹配，导致量化后的信息损失被放大。
- 多模态理解能力与语言建模能力在量化中被同等对待，未针对性地保护视觉理解这条更脆弱的链路。

### 研究动机
需要一套专门面向低位宽 MLLM 的 QAT 框架，既能对齐数据分布，又能有差别地保护多模态理解能力。

## 研究问题

核心问题：**如何在低位宽（W4A8）量化下，使小规模 MLLM 在边缘设备上保持接近全精度的多模态理解能力？**

## 方法概述

### 核心思想
把 MLLM 的量化训练拆解为"数据对齐"与"能力分层恢复"两个维度：先用自重标注对齐微调与预训练分布，再渐进地、优先地恢复多模态理解层。

### 方法框架

![[overview_page1.png|800]]

> 图1：ProQuant 整体框架（数据自重标注 SRL + 渐进式块激活 PBA + 视觉敏感因子 τ）

### 关键组件

**组件1：自重标注（Self-Relabeling, SRL）**
- **功能**：用开源多模态数据集的响应重采样，对齐有限的微调数据集分布与预训练数据集分布。
- **作用**：缓解 QAT 微调阶段的数据分布漂移。

**组件2：渐进式块激活（Progressive Block Activation, PBA）**
- **功能**：首次将"多模态理解"与"语言建模"能力解耦看待，优先恢复多模态理解能力。
- **作用**：按阶段逐步激活不同块，避免一次性量化带来的能力坍塌。

**组件3：视觉敏感因子 τ（vision-sensitive factor）**
- **功能**：有效识别多模态关键层。
- **作用**：指导哪些层需要优先/精细地恢复，把有限的量化预算花在刀刃上。

## 实验结果

### 主要结果
- 覆盖 2B–8B 参数规模的多种模型与多个 benchmark。
- **W4A8 Qwen3-VL-2B-Instruct 平均准确率提升 6.6%**。
- 与全精度相比仅约 **1%** 性能损失。
- 显著优于现有 PTQ / QAT 方法。

## 深度分析

### 研究价值
- **理论贡献**：提出"解耦多模态理解与语言建模"的量化视角，以及视觉敏感因子 τ 识别关键层。
- **实际应用**：直接面向边缘 MLLM 部署，方法开销可控（基于 QAT，无需额外推理开销）。
- **可复现性**：开源代码。

### 方法优势
1. 数据分布对齐（SRL）解决 QAT 微调漂移。
2. 分层渐进恢复（PBA）保护最脆弱的多模态理解链路。
3. 视觉敏感因子（τ）让量化预算分配更有针对性。

### 局限性
- 摘要未给出更多低位宽（如 W3A4）或更多模型家族的消融。
- QAT 相对 PTQ 仍需要一定微调成本，部署流水线更复杂。
- 视觉敏感因子的泛化性（跨架构、跨任务）有待验证。

## 我的综合评价

### 总体评分
**8.5/10** — 针对边缘 MLLM 低位宽量化的系统性 QAT 方案，实用性强、实验扎实，是模型压缩部署方向的优质工作。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 解耦式量化视角 + 视觉敏感因子有新意 |
| 技术质量 | 8/10 | SRL/PBA/τ 三组件设计清晰 |
| 实验充分性 | 8/10 | 2B–8B 多模型多 benchmark |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 9/10 | 直接服务边缘部署，开源 |

## 相关论文
- [[DictLLM_Post-training_Compression_of_Large_Language_Model_with_Dictionary_Kernels|DictLLM]] - 同属 LLM 后训练压缩方向
- [[A_Unified_Hessian-Guided_Framework_for_Post-Training_Quantization|A Unified Hessian-Guided PTQ Framework]] - PTQ 量化基线

> [!tip] 关键启示
> 低位宽量化失败的关键不在"压缩"，而在"数据漂移"与"能力脆弱链"未得到保护；ProQuant 用 SRL 对齐分布、用 PBA 分层恢复、用 τ 识别关键层，把问题拆解得干净而可复用。
