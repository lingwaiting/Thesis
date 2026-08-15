---
date: "2026-08-15"
paper_id: "arXiv:2608.13426"
title: "Reduced Matrix Multiplication: Input-Adaptive Matrix-Product Reduction for LLM Inference"
authors: "Zixuan Lan, Yanhong Li, Jiawei Zhou"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 推理优化
  - 矩阵乘法
  - Transformer
  - 激活感知剪枝
quality_score: "8.0/10"
created: "2026-08-15"
updated: "2026-08-15"
status: analyzed
---

# Reduced Matrix Multiplication: Input-Adaptive Matrix-Product Reduction for LLM Inference

## 核心信息
- **论文ID**：arXiv:2608.13426
- **作者**：Zixuan Lan, Yanhong Li, Jiawei Zhou
- **机构**：--
- **发布时间**：2026-08-13
- **会议/期刊**：arXiv（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](http://arxiv.org/abs/2608.13426v1) | [PDF](https://arxiv.org/pdf/2608.13426v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Transformer-based language models achieve strong performance but incur substantial inference cost due to repeated high-dimensional matrix multiplications. We propose Reduced Matrix Multiplication (RMM), a training-free, input-adaptive inference method that reduces Transformer matrix products by selecting informative slices along their contraction dimensions, without modifying model weights. Under a simple retention-ratio control, RMM provides a smooth and predictable accuracy-efficiency trade-off. Across language models ranging from 1B to 70B parameters, we find that reduction tolerance depends on the model family, task, component, and retention ratio, although it often improves with model scale. Under moderate reduction, RMM remains robust across the evaluated discriminative, autoregressive generation, and long-context settings. We further show that the same principle extends to multimodal vision-language inference. Mechanistic ablations reveal a structural asymmetry within Transformers: attention-side computations are substantially more reducible than MLP components. Finally, wall-clock benchmarks with custom kernels on an NVIDIA A100 show that these computational savings can translate into practical runtime gains, especially at longer sequence lengths.

### 中文翻译
基于 Transformer 的语言模型性能强大，但由于反复执行高维矩阵乘法，推理成本高昂。本文提出 Reduced Matrix Multiplication（RMM），一种无需训练、输入自适应的推理方法，通过沿收缩维度选择信息量大的切片来缩减 Transformer 的矩阵乘积，且不修改模型权重。在简单的保留率控制下，RMM 提供了平滑且可预测的精度-效率权衡。在 1B 到 70B 参数的模型中，作者发现缩减容忍度取决于模型家族、任务、组件和保留率，但通常随模型规模增大而提升。在中等缩减程度下，RMM 在判别式、自回归生成和长上下文场景中均保持稳健。同一原理还可扩展到多模态视觉语言推理。机制消融揭示了 Transformer 内的结构不对称性：注意力侧的计算比 MLP 组件更容易缩减。最后，在 NVIDIA A100 上用定制核的墙钟时间基准表明，这些计算节省可以转化为实际的运行时增益，尤其在更长序列时。

### 核心要点提炼
- **研究背景**：Transformer 推理的核心成本在于大量矩阵乘法。
- **研究动机**：经典的近似矩阵乘法依赖随机采样平均，不适用于单次前向的推理场景。
- **核心方法**：RMM——按激活范数 TopK 选择收缩维度的切片，做确定性的输入自适应缩减。
- **主要结果**：平滑的精度-效率权衡；注意力侧比 MLP 更可缩减；A100 上实现实际加速。
- **研究意义**：为输入自适应的推理时优化提供了可扩展的新方向。

## 研究背景与动机

### 领域现状
LLM 推理优化是热门方向，主流路线包括：权重量化、静态剪枝（SparseGPT、Wanda、SliceGPT）、KV 缓存管理（H2O）等。这些方法大多在「权重侧」或「缓存侧」做文章。

### 现有方法的局限性
- **静态剪枝**：剪枝决策在推理前固定，无法适应不同输入，导致性能下降不均匀。
- **随机近似**：经典蒙特卡洛矩阵乘法需要多次采样取平均，与单次前向推理不兼容。
- **KV 缓存方法（H2O）**：只在 token 维度缩减，不触及特征维度的矩阵乘法。

### 研究动机
作者希望找到一种「无需训练、逐输入自适应、确定性」的矩阵乘法缩减方法，直接在特征维度上降低计算量。

## 研究问题

### 核心研究问题
如何在保证质量的前提下，用确定性的输入自适应方式缩减 Transformer 中的矩阵乘法？

## 方法概述

### 核心思想
把 Transformer 中所有核心计算统一为 `Y = AB` 的矩阵乘法形式。RMM 选择收缩维度 `[d]` 的一个子集 `I`（`|I| = ⌈ρd⌉`，ρ 为保留率），只计算 `A[:,I] · B[I,:]`。子集 `I` 由激活矩阵 A 的列范数 `‖A[:,j]‖₂` 的 TopK 决定——完全确定性，且随输入、层、注意力头、解码步而变化。作者在附录中证明：按列范数 TopK 在给定保留预算下是 minimax 最优的（最坏情况误差最小）。

### 方法框架

#### 整体架构
![[2608.13426_method_p1.png|800]]

> 图1：RMM 在 Transformer 主要计算中的应用示意。对注意力（QKᵀ、PV）和 MLP/线性投影，均按激活范数选择收缩维度切片。

#### 各模块详细说明

**模块1：Attention 侧缩减**
- **QKᵀ 缩减**：按 `‖Q[:,j]‖₂` 选择头维度切片，得到 `S̃ = Q[:,I] K[:,I]ᵀ / √d_h`。
- **PV 缩减**：可选地对 token 维度再做稀疏化，按 `‖P[:,t]‖₂` 选择 token 子集。
- **分组查询注意力（GQA）**：对每个头在 Q 上独立选择，从共享的 K/V 收集对应维度。

**模块2：MLP 与线性投影缩减**
- 对 `Y = XW`，按 `‖X[:,j]‖₂` 选择特征维度切片，计算 `Ỹ = X[:,I] W[I,:]`。

**模块3：复杂度**
- 密集计算 `O(ndm)` → 缩减后 `O(n·ρd·m)`。
- 特征分数计算 `O(nd)`，TopK 选择是轻量向量操作，开销相对较小。

## 实验结果

### 模型与任务
- **模型**：Llama 3.1（70B/8B）、Llama 3.2（3B/1.5B）、Qwen3 32B、Qwen 3.1 7B、Qwen2.5-VL-7B。
- **任务**：QA/推理（COPA、PiQA、CommQA、ARC、MMLU）、语言建模（WikiText、BookCorpus）、数学代码（GSM8K、HumanEval）、长上下文（Ruler-CWE/Hotpot）、摘要（CNN/DailyMail）、视觉语言（POPE、Blink）。
- **设置**：全部零样本，无任务微调。

### 主要结果
**QA（LLaMA 3.1 8B，RR=0.5，平均准确率）**：
- Full model（RR=1.0）：69.8
- **RMM：59.8**（最优剪枝方法）
- SparseGPT：56.1 / Wanda：52.7 / SliceGPT：37.0 / Magnitude：39.3

**摘要（CNN/DailyMail，RR=0.5）**：RMM 的 ROUGE-1 达 34.2，显著优于 Static（28.0）、Random（5.7）、H2O（24.4）。

![[2608.13426_1b3bplots_p1.png|600]]

> 图2：不同模型规模下 RMM 的性能-保留率曲线，展示了平滑的权衡。

![[2608.13426_VLMSample_p1.png|600]]

> 图3：RMM 在多模态视觉语言推理上的扩展实验样例。

### 结果分析
- RMM 在所有剪枝方法中平均准确率最高，且退化更均匀、更可预测。
- 静态剪枝在生成任务中质量急剧下降；随机剪枝虽动态但无法维持语义一致；H2O 始终弱于激活感知的矩阵级剪枝。
- **结构不对称性**：注意力侧计算远比 MLP 更可缩减（机制消融揭示）。
- **规模效应**：缩减容忍度通常随模型规模增大而提升。

## 深度分析

### 研究价值评估

#### 理论贡献
- **minimax 最优性证明**：按列范数 TopK 选择是给定预算下最坏情况误差最小的策略，为激活感知缩减提供了理论依据。
- **统一矩阵乘法视角**：把注意力与 MLP 统一为 `Y=AB`，方法简洁通用。

#### 实践价值
- 训练无关、即插即用，无需任何微调。
- 保留率 ρ 提供平滑可控的精度-效率旋钮，便于按场景调优。
- A100 定制核实测加速，长序列下收益更明显。

### 局限与思考
- 中等缩减下鲁棒，但激进缩减（低 ρ）仍会明显掉点。
- 特征分数计算与 TopK 虽有开销，但作者声称相对较小；实际加速高度依赖定制核的工程实现。
- 与量化、KV 缓存等正交方法的组合潜力尚未充分探索。
