---
date: "2026-10-04"
paper_id: "arXiv:2610.02193"
title: "Hierarchical Continuous Diffusion Language Models"
authors: "Hui Ren, Zihan Li, Chang Liu, Huidong Liu, Alexander Schwing"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - Diffusion-Language-Model
  - 连续扩散
  - 结构化推理
quality_score: "8.5/10"
related_papers: []
created: "2026-10-04"
updated: "2026-10-04"
status: analyzed
---

# Hierarchical Continuous Diffusion Language Models

## 核心信息
- **论文ID**：arXiv:2610.02193
- **作者**：Hui Ren, Zihan Li, Chang Liu, Huidong Liu, Alexander Schwing
- **机构**：--
- **发布时间**：2026-10-01
- **会议/期刊**：分类 cs.CL, cs.AI, cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.02193) | [PDF](https://arxiv.org/pdf/2610.02193)

## 摘要翻译

### 英文摘要
Discrete diffusion language models offer a compelling alternative to autoregressive generation for tasks demanding bidirectional reasoning and global constraint satisfaction. Yet they share a structural bottleneck: when decoding in parallel, each token is sampled independently from its marginal, severing the statistical dependencies among the tokens decoded together. Continuous diffusion language models avoid this by denoising a shared continuous state, but their denoiser sees only that state, so nothing ties it to a valid token configuration until it is finally decoded. To address this, we propose Hierarchical Continuous Diffusion Language Models (HC-DLM).

### 中文翻译
离散扩散语言模型为需要双向推理与全局约束满足的任务提供了一种有吸引力的自回归替代方案。但它们共享一个结构性瓶颈：并行解码时，每个 token 独立地从其边缘分布采样，割裂了同时解码的 token 之间的统计依赖。连续扩散语言模型通过对共享连续状态去噪规避了这一问题，但其去噪器只看到该状态，直到最终解码前都没有任何东西将其与合法 token 配置绑定。为此，本文提出分层连续扩散语言模型（Hierarchical Continuous Diffusion Language Models, HC-DLM）。

### 核心要点提炼
- **研究背景**：扩散语言模型适合双向推理与全局约束任务；离散扩散在并行解码时割裂 token 依赖，连续扩散则缺乏与合法 token 配置的绑定。
- **研究动机**：需要一种同时"保持 token 依赖"又"绑定连续状态与合法 token"的扩散语言模型。
- **核心方法**：HC-DLM —— 在单一有原则的去噪过程中，将离散 token 生成与连续潜在轨迹耦合，训练目标由 token 似然的变分界推导而来。
- **主要结果**：在 Sudoku、Countdown、LM1B 上，匹配模型规模下优于离散与连续扩散基线。
- **研究意义**：让连续潜在状态成为唯一持久的生成状态，token 每步从潜变量读出并作为下一步潜变量更新的脚手架。

## 研究背景与动机

### 领域现状
- 扩散语言模型在需要**双向推理与全局约束满足**的任务上，是自回归生成的有力替代。
- 离散扩散：并行解码时各 token 独立采样，**割裂 token 依赖**。
- 连续扩散：对共享连续状态去噪，但去噪器只看到该状态，**缺乏与合法 token 配置的绑定**。

### 现有方法的局限性
- 离散扩散：并行 token 的边缘采样破坏统计依赖，难以保证全局一致性。
- 连续扩散：直到最终解码前，连续状态与合法 token 无关联。

### 研究动机
能否把"离散 token 生成"与"连续潜在轨迹"耦合进单一的去噪过程，同时获得两者优势？

## 研究问题

### 核心研究问题
如何设计一种扩散语言模型，使并行解码时既保持 token 间依赖、又能将连续潜状态与合法 token 配置绑定？

## 方法概述

### 核心思想
HC-DLM 让**连续潜在状态成为唯一持久的生成状态**：token 每一步从潜变量读出，并作为下一步潜变量更新的"脚手架"反哺回去。与"给自包含离散链附加连续上下文"的近期方法不同，HC-DLM 的训练目标由 token 似然的**变分界**推导而来，耦合是原则性的而非拼接式的。

### 方法框架

#### 整体架构
![[2610.02193_page1.png|600]]

> 图1：HC-DLM 整体示意 —— 连续潜变量与离散 token 的层级耦合去噪。

![[pipeline_train_v2_page1.png|600]]

> 图2：HC-DLM 训练 pipeline（变分界推导的训练目标）。

![[pipeline_inference_v2_page1.png|600]]

> 图3：HC-DLM 推理 pipeline —— token 逐步读出并反哺潜变量更新。

- **连续潜变量为持久状态**：潜变量是唯一持久的生成状态，token 从潜变量读出。
- **层级耦合去噪**：token 生成与连续潜在轨迹在同一去噪过程中耦合。
- **变分界训练目标**：目标由 token 似然的变分下界推导，保证原则性。

### 关键创新
1. **层级耦合范式** —— 将连续潜在轨迹作为唯一持久状态，token 作为其"读出 + 脚手架"。
2. **原则性目标** —— 训练目标由 token 似然变分界推导，非拼接式设计。
3. **统一结构化与语言建模** —— 在同一框架下同时处理 Sudoku/Countdown 结构化推理与 LM1B 语言建模。

## 实验结果

### 数据集
- **结构化推理**：Sudoku（谜题准确率）、Countdown（数学规划）
- **语言建模**：LM1B（生成困惑度）

### 主要结果
- 在匹配模型规模下，HC-DLM 在 Sudoku 与 Countdown 的谜题准确率、以及 LM1B 的生成困惑度上，均优于离散与连续扩散基线。

## 深度分析

### 研究价值
- **理论贡献**：从变分界出发统一了"token 依赖保持"与"连续状态-合法 token 绑定"两大需求。
- **实际应用**：为需要全局约束满足（如规划、推理、代码、可控生成）的场景提供新范式。
- **领域影响**：为扩散语言模型提供了一条"连续潜变量主导"的层级化路线。

### 优势
- 原则性的变分界训练目标，方法动机清晰。
- 同时覆盖结构化推理与语言建模，通用性强。
- 在匹配规模下对两类基线均取得提升。

### 局限性
- 扩散式解码的推理速度与自回归相比仍有差距，实用化需进一步优化。
- 评测规模相对较小（LM1B、Sudoku/Countdown），大规模语言建模上的表现有待验证。

### 未来工作
- 在大规模语料与更多结构化推理任务上验证扩展性。
- 优化推理速度，探索与蒸馏、多步解码加速技术的结合。
