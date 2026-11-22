---
date: "2026-11-22"
paper_id: "arXiv:2611.26363"
title: "HiSA: Sparsifying the Hidden Dimension of RoPE Attention in LLMs"
authors: "Ambroise Borbely, Mathieu Salzmann, Olivier Verscheure"
domain: "大语言模型"
tags:
  - 论文笔记
  - 稀疏注意力
  - RoPE
  - 大语言模型
  - 推理加速
  - Sparse-Attention
  - Hidden-Dimension
  - RoPE
quality_score: "7.83/10"
created: "2026-11-22"
updated: "2026-11-22"
status: analyzed
---

# HiSA: Sparsifying the Hidden Dimension of RoPE Attention in LLMs

## 核心信息
- **论文ID**：arXiv:2611.26363
- **作者**：Ambroise Borbely, Mathieu Salzmann, Olivier Verscheure
- **机构**：EPFL（洛桑联邦理工学院）；瑞士数据科学中心（EPFL 与 ETH Zurich）
- **发布时间**：2026-11-19
- **会议/期刊**：arXiv 预印本（cs.LG / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2611.26363) | [PDF](https://arxiv.org/pdf/2611.26363)
- **代码**：https://github.com/ambroise-b/hisa

## 摘要翻译

### 英文摘要
Attention in large language models (LLMs) scores every query against every key with a full dot product. We investigate whether this dot product must be computed completely for attention to be effective. We propose Hidden Sparse Attention (HiSA), which keeps the query and key projections intact but only computes a partial dot product, following a static, parameter-free rule set by the token positions: attention becomes sparse in the hidden (feature) dimension, an axis much less explored than token sparsity, rather than over tokens as in almost all sparse attention methods. Based on the structure of rotary position embeddings (RoPE), we decompose keys and queries into parts which can be omitted when computing attention. By training LLMs from scratch with HiSA, we find that this partial dot product does not hurt but improves performance compared with the complete product while computing only about a third of the query-key products. During inference HiSA improves decoding speed and shrinks memory footprint. We also extend HiSA to FlashAttention-3 kernels for training and inference. Finally, we motivate our approach by showing that dense attention learns to suppress the attention score parts that HiSA drops.

### 中文翻译
大语言模型（LLM）中的注意力会对每个 query 与每个 key 计算完整的点积。本文探究：注意力是否必须完整计算这个点积才能有效？作者提出 Hidden Sparse Attention（HiSA），它保持 query 与 key 的投影不变，只计算部分点积，遵循一个由 token 位置决定的、静态且无参数的规则：注意力在**隐藏（特征）维度**上变得稀疏，而非像几乎所有稀疏注意力方法那样在 token 维度上稀疏。基于旋转位置编码（RoPE）的结构，作者将 key 与 query 分解为计算注意力时可省略的部分。通过从零训练 LLM，作者发现这种部分点积非但无害，反而优于完整点积，同时仅计算约三分之一的 query-key 乘积。推理时 HiSA 提升了解码速度并缩小了显存占用；作者还将 HiSA 扩展到 FlashAttention-3 内核以用于训练与推理。最后，作者证明密集注意力在训练中会主动抑制 HiSA 所丢弃的注意力得分部分，从而为方法提供了动机。

### 核心要点提炼
- **研究背景**：主流注意力加速方法（token 稀疏、滑动窗口、KV-cache 淘汰）都在"减少 key 集合"上做文章，保留完整点积。
- **研究动机**：一个正交的问题——注意力是否必须完整计算 query-key 点积？
- **核心方法**：HiSA——基于 RoPE 结构，在隐藏维度上按 token 位置静态、无参数地稀疏化点积，用角度预算 $\Phi$（默认 90°）决定保留哪些频率对。
- **主要结果**：从零训练 LLM 时，部分点积非但无害反而更优，同时只算约 1/3 的 query-key 乘积；推理提速、显存缩小；可扩展到 FlashAttention-3。
- **研究意义**：开辟了"隐藏维度稀疏"这一此前被忽视的注意力加速维度，并给出几何（RoPE）与可解释性（密集注意力抑制）双重依据。

## 研究背景与动机

### 领域现状
注意力层对每个 query 与每个 key 在完整 head 维度上计算点积。绝大多数加速注意力的工作都**保留完整点积**，改为减少 query 关注的 key 集合：固定模式、滑动窗口、KV-cache 淘汰、以及训练无关或可学习的 token 选择。

### 现有方法的局限性
这些"token 稀疏"方法都默认"点积必须完整计算"。而 RoPE 赋予了一个被忽视的结构：高频频率对在几个 token 内就转了很多圈，低频频率对在整个上下文中几乎不旋转。高频对得分对精确距离极其敏感，而语言在远距离词之间并不遵循精确结构。

### 研究动机
由此引出一个正交问题：是否每一个频率对在每个相对距离上都需要计算？作者假设——当 token 距离较大时，高频对并不必要，移除它们无害甚至有益。

## 研究问题

### 核心研究问题
注意力是否必须在每个相对距离上完整计算所有频率对的 query-key 点积？能否在隐藏（特征）维度上、而非 token 维度上稀疏化注意力，且不牺牲甚至提升性能？

## 方法概述

### 核心思想
把 RoPE 的旋转几何转成注意力规则：query 与 key 之间每个频率对 $l$ 的点积，仅在 RoPE 诱导的旋转角低于角度预算 $\Phi$（默认 90°）时保留。query/key 投影不变，稀疏化的是点积的隐藏维度；稀疏模式仅由 RoPE 频率与位置决定，无学习参数、无需运行时选择。

![[teaser_page1.png|600]]

> 图1：密集注意力计算每个频率对（左）；token 稀疏注意力丢弃整个 key（中）；HiSA 只在该频率对旋转角低于 $\Phi$ 时保留——近处 key 比较所有频率对，远处 key 只比较低频对，稀疏性落在隐藏维度而非 token 上（右）。

### 方法框架

#### 整体架构
- **RoPE 分解**：RoPE 按频率 $l$ 旋转 query/key 的每对二维分量，频率 $\theta_l = b^{-2l/d}$。旋转可组合，query 与 key 的相对距离编码为一次旋转。
- **稀疏规则**：对每个频率对 $l$，若旋转角 $\Delta\theta_l < \Phi$ 则保留该项点积，否则省略。高频对只在近处 key 上计算，低频对在远处 key 上计算。
- **无参数、静态**：与需要在运行时打分/路由/选择 key 的稀疏方法不同，HiSA 无学习参数、无选择步骤，稀疏模式完全由 RoPE 频率与位置确定。

#### 计算/可达性/显存分析
- 推导了 HiSA 的计算量、注意力可达范围（reach）与 KV-cache 显存占用（Section 3）。
- 从零预训练与长上下文扩展两种设置下评测（Section 4.1–4.4）。
- 将 HiSA 实现到 FlashAttention 内核以支持训练与推理。

## 实验结果

### 实验目标
验证 HiSA 在从零预训练、长上下文扩展下的有效性，并与密集注意力在验证损失、训练速度、KV-cache 显存、解码吞吐上对比；同时验证预训练密集模型能否经持续训练转换为 HiSA。

### 主要结果
- **从零训练**：HiSA 只计算约 1/3 的 query-key 乘积，验证损失非但未上升反而优于完整点积（密集注意力）。
- **推理效率**：HiSA 提升解码速度、缩小 KV-cache 显存占用。
- **长上下文扩展**：HiSA 在长上下文场景下同样有效。
- **密集→HiSA 转换**：预训练密集模型可通过持续训练转换为 HiSA，且与密集持续训练相当。
- **可解释性**：密集注意力在训练中学习到"抑制 HiSA 丢弃的得分部分"，说明 HiSA 的稀疏规则不仅由 RoPE 几何驱动，也由密集模型实际学到的行为度量支撑。

### 消融与实现
- 角度预算 $\Phi$ 作为超参数（默认 90°）进行调节。
- FlashAttention-3 内核扩展支持训练与推理加速。

## 深度分析

### 研究价值评估

#### 理论贡献
- **新稀疏维度**：提出"隐藏维度稀疏"这一此前极少被探索的注意力加速方向，与 token 稀疏正交。
- **几何驱动的规则**：把 RoPE 的旋转结构转成可解释、无参数的稀疏规则，连接位置编码理论与注意力效率。
- **可解释性佐证**：证明密集注意力主动抑制 HiSA 丢弃的得分部分，为规则提供了经验层面的正当性。

#### 实际应用价值
- **推理降本**：约 1/3 的 query-key 乘积 + 更小显存 + 更快解码，直接降低 LLM 部署成本。
- **训练可迁移**：可从零训练，也可把现有密集模型经持续训练转换为 HiSA，落地路径清晰。

### 方法优势详解
- **无额外参数与选择开销**：相比需要运行时路由/选择的 token 稀疏方法，HiSA 的稀疏模式零学习、零选择成本。
- **性能无损甚至提升**：在节省 2/3 点积的同时验证损失反而下降，颠覆"稀疏必然降质"的直觉。
- **正交可叠加**：隐藏维度稀疏与 token 稀疏理论上可组合，潜力未被穷尽。

### 局限性分析
- **依赖 RoPE**：规则建立在 RoPE 频率结构之上，对不使用 RoPE 的架构（如某些 ALiBi/无位置编码模型）不直接适用。
- **角度预算敏感**：$\Phi$ 作为超参数需调优，最优值可能随模型规模、上下文长度、训练阶段变化。
- **长距离信息折中**：远处 key 只保留低频对，可能丢失某些细粒度长距离语义关联（尽管实验显示影响可忽略）。

### 适用性与场景分析
- **适用场景**：使用 RoPE 的 LLM 预训练、长上下文部署、显存受限的推理场景。
- **不适用场景**：非 RoPE 架构、对长距离细粒度位置信息极度敏感的任务。

## 与相关论文对比

### 对比论文选择依据
- token 稀疏注意力代表（滑动窗口、KV-cache 淘汰）与 RoPE 结构分析工作。

| 对比维度 | 滑动窗口 / KV-cache 淘汰 | 本文（HiSA） |
|----------|--------------------------|--------------|
| 稀疏维度 | token（减少 key 集合） | 隐藏/特征（部分点积） |
| 是否有学习参数 | 部分有（可学习选择） | 无，静态无参数 |
| 是否依赖运行时选择 | 需要打分/路由 | 无选择步骤 |
| 点积完整性 | 保留完整点积 | 只算约 1/3 |
| 关系类型 | 正交/互补 | 新增维度 |

## 技术路线定位

### 所属技术路线
高效注意力机制（Efficient Attention）中的**稀疏注意力**方向，但切入点是隐藏维度（特征稀疏）而非 token 稀疏，属于结合 RoPE 位置编码几何的新颖分支，兼具推理加速与可解释性价值。
