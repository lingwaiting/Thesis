---
date: "2026-10-15"
paper_id: "arXiv:2610.19121"
title: "Unlocking Structured Pruning for Reasoning Language Models via Attention-Preserving MLP Trimming"
authors: "Woosang Lim, Haneul Yoo, Vincent-Daniel Yun, Sungjoo Yoo"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 模型压缩
  - 结构化剪枝
  - 推理模型
  - 推理加速
quality_score: "8.5/10"
related_papers: []
created: "2026-10-15"
updated: "2026-10-15"
status: analyzed
---

# Unlocking Structured Pruning for Reasoning Language Models via Attention-Preserving MLP Trimming

## 核心信息
- **论文ID**：arXiv:2610.19121
- **作者**：Woosang Lim、Haneul Yoo、Vincent-Daniel Yun、Sungjoo Yoo（共同一作 3 人，通讯作者 Sungjoo Yoo）
- **机构**：首尔国立大学（SNU）、纽约大学（NYU）、南加州大学（USC）
- **发布时间**：2026-10-13
- **会议/期刊**：预印本（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2610.19121) | [PDF](https://arxiv.org/pdf/2610.19121)
- **引用**：--

## 摘要翻译

### 英文摘要
Structured pruning reduces LLM inference costs by removing model components to produce smaller dense models. However, existing methods can cause reasoning performance to collapse on long-form reasoning tasks such as AIME. We propose LayerTrim, a training-free structured pruning method that identifies redundant decoder layers and selectively trims their MLP features while preserving attention and decoder depth. LayerTrim is the first training-free structured pruning method designed to preserve long-form reasoning without gradients or recovery training. Across three reasoning language models and five benchmarks, LayerTrim outperforms existing structured pruning methods in most evaluated settings, improving average accuracy by up to 21.4 percentage points over RESP. On Qwen3-14B, LayerTrim achieves 1.24× faster prefill and 19.7% lower peak GPU memory than the dense model, while better preserving reasoning ability.

### 中文翻译
结构化剪枝通过移除模型组件来降低 LLM 推理成本，产出更小的稠密模型。然而，现有方法在长形式推理任务（如 AIME）上会导致推理性能崩塌。作者提出 **LayerTrim**，一种无训练的结构化剪枝方法：识别冗余的解码器层，并选择性裁剪其 MLP 特征，同时保留注意力与解码器深度。LayerTrim 是首个无需梯度或恢复训练、专为保留长形式推理设计的无训练结构化剪枝方法。在 3 个推理语言模型、5 个基准上，LayerTrim 在多数设定下优于现有结构化剪枝方法，平均精度较 RESP 最多提升 21.4 个百分点；在 Qwen3-14B 上，LayerTrim 实现 1.24× 的 prefill 加速与 19.7% 的峰值 GPU 显存降低，同时更好地保留推理能力。

### 核心要点提炼
- **研究背景**：推理语言模型（RLM）依赖长 CoT，推理成本高，需要高效推理。
- **研究动机**：通用剪枝方法在长推理任务（如 AIME）上会导致性能崩塌。
- **核心方法**：无训练的 LayerTrim——选择性裁剪冗余层的 MLP 特征，保留 attention 与深度。
- **主要结果**：平均精度最多 +21.4pp（vs RESP），Qwen3-14B 上 1.24× prefill 加速、19.7% 显存下降。

## 研究背景与动机

### 领域现状
推理语言模型通过 CoT 在复杂任务上表现强劲，但长推理序列带来高推理成本。结构化剪枝能产出更小的稠密模型以降低推理成本，但面向非推理 LLM 开发的剪枝方法迁移到 RLM 时，常导致长推理任务（如 AIME）性能崩塌。

### 现有方法的局限性
- 现有结构化剪枝（LaCo、GRASP、ShortGPT、CoCurve、FLAP、RESP 等）会导致推理轨迹更长、精度更低。
- 缺乏专门针对「保留长形式推理」设计的无训练剪枝方法。

### 研究动机
在剪枝预算下，如何既压缩模型又保留长形式推理能力？

## 方法概述

### 三个关键观察（动机来源）
1. **MLP 裁剪比注意力裁剪/整模块移除的表示误差更小**。
2. **MLP 特征重要性集中在少数特征子集上**。
3. **把相同预算集中在冗余层比分散到所有层的误差更小**。

### LayerTrim 核心思想
- **识别冗余解码器层**，在这些层上**选择性裁剪 MLP 特征**。
- **保留注意力与解码器深度**，避免破坏推理所需的信息通路。
- **无训练、无梯度、无恢复训练**——首个专为长形式推理设计的无训练结构化剪枝。

![[layertrim_overview-4_page1.png|600]]

> 图：LayerTrim 方法概览（选择性 MLP 裁剪 + 保留注意力与深度）

## 实验结果

### 主要结果
- **精度**：在 3 个 RLM、5 个基准上，LayerTrim 在多数设定下优于现有方法，平均精度较 RESP 最多 +21.4pp。
- **效率**：Qwen3-14B 上 1.24× prefill 加速、19.7% 峰值 GPU 显存降低。
- **生成长度**：在评估的剪枝模型中取得最短的平均生成长度，同时保持最高的平均精度——即「又快又准」。

![[hero_fig_g_page1.png|600]]

> 图：Qwen3-14B 上「精度 vs 平均生成 token 数」的权衡（LayerTrim 位于左上最优区）

## 深度分析

### 亮点
1. **精准定位问题**：通过三个量化观察，把「为什么剪枝会毁掉推理」讲清楚了。
2. **反直觉的保留策略**：只剪 MLP、保留 attention 与深度，直接命中推理模型的结构要害。
3. **实用价值高**：无训练 + 无恢复，开箱即用，prefill 加速 + 显存下降直接落地。

### 局限
- 聚焦于「结构化 MLP 裁剪」，对 attention 本身的高开销（长 CoT 的 KV cache）优化有限。
- 评测以推理/代码基准为主，通用任务的泛化性有待进一步验证。

### 对研究方向的启示
「保留 attention + 深度、只剪 MLP」可作为一种针对推理模型的通用压缩范式，值得与量化、投机解码等方法结合。
