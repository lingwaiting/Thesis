---
date: "2026-09-30"
paper_id: "arXiv:2609.36301"
title: "MoRE: Scaling mixture of experts with hardware-aware low-rank routing"
authors: "Honam Wong, Surbhi Goel, Enric Boix-Adserà"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - Mixture-of-Experts
  - MoE
  - Efficient-Training
  - Low-Rank
quality_score: "9.0/10"
related_papers: []
created: "2026-09-30"
updated: "2026-09-30"
status: analyzed
---

# MoRE: Scaling mixture of experts with hardware-aware low-rank routing

## 核心信息
- **论文ID**：arXiv:2609.36301
- **作者**：Honam Wong, Surbhi Goel, Enric Boix-Adserà
- **机构**：--
- **发布时间**：2026-09-28
- **会议/期刊**：cs.LG / cs.AI / cs.CL / stat.ML
- **链接**：[arXiv](https://arxiv.org/abs/2609.36301) | [PDF](https://arxiv.org/pdf/2609.36301)

## 摘要翻译

### 英文摘要
Mixture-of-Experts (MoE) layers are central to frontier language models, and recent architectures push toward more and smaller experts. In this regime, the standard linear router becomes a bottleneck: with $M$ experts and hidden dimension $h$, its per-token cost $\Theta(Mh)$ dominates the MoE layer once $M$ is large. We introduce MoRE (Mixture of Rank-reduced-routed Experts), which factorizes the router weight matrix at rank $r$ and reduces the routing cost to $O((h + M)r)$. We prove that rank logarithmic in $M$ suffices for routing expressivity when the number of active experts is fixed, and is necessary up to precision factors. We also prove that logarithmic rank preserves load balance in a Gaussian memorization model, and training on a synthetic phonebook task shows that low rank does not hurt memorization. At matched active FLOPs, the factorization allows a factor of $\Theta(h/r)$ more experts. To realize this gain in wall-clock time, we design a fused Triton kernel at inference that avoids expensive memory operations on HBM. Empirically, MoRE improves memorization on the phonebook task and performance on knowledge-intensive Q&A benchmarks after pretraining, while matching reasoning ability. Code available at https://github.com/Matheart/MoRE_code.

### 中文翻译
Mixture-of-Experts（MoE）层是前沿语言模型的核心组件，而近期架构正朝着"更多、更小的专家"方向演进。在这一趋势下，标准的线性路由器（router）成为了瓶颈：设有 $M$ 个专家、隐藏维度为 $h$，其每 token 成本 $\Theta(Mh)$ 在 $M$ 较大时会主导整个 MoE 层。作者提出 MoRE（Mixture of Rank-reduced-routed Experts，秩降低路由专家混合），将路由器权重矩阵按秩 $r$ 分解，把路由成本降到 $O((h + M)r)$。作者证明：当激活专家数固定时，秩只需对 $M$ 呈对数增长即足以保证路由表达能力，并且（在精度因子范围内）这是必要的。作者还证明，在高斯记忆模型中对数秩可以保持负载均衡；在合成 phonebook 任务上的训练表明，低秩不会损害记忆能力。在匹配的激活 FLOPs 下，该分解允许专家数量提升 $\Theta(h/r)$ 倍。为了在墙钟时间内实现这一收益，作者设计了一个推理阶段的融合 Triton 内核，避免了 HBM 上昂贵的显存操作。实验上，MoRE 在 phonebook 任务上改善了记忆能力，在预训练后的知识密集型问答基准上提升了性能，同时保持了推理能力。代码见 https://github.com/Matheart/MoRE_code。

### 核心要点提炼
- **研究背景**：MoE 架构走向"更多更小专家"，线性路由成本 $\Theta(Mh)$ 成为瓶颈。
- **研究动机**：在不牺牲表达能力与负载均衡的前提下，降低路由的计算/显存成本。
- **核心方法**：把路由器权重矩阵做秩 $r$ 分解（MoRE），并配融合 Triton 内核实现墙钟加速。
- **主要结果**：对数秩即可保证表达力与负载均衡；专家数可提升 $\Theta(h/r)$ 倍；知识密集问答提升、推理持平。
- **研究意义**：为大规模 MoE 的路由提供理论与系统双重支撑。

## 研究背景与动机

### 领域现状
MoE 已成为前沿大模型（如 DeepSeek、Mixtral、Qwen-MoE 等）的核心架构，通过"每个 token 只激活少数专家"在参数规模与计算成本之间取得平衡。近年趋势是增加专家数量、减小单个专家规模，以进一步摊薄每 token 的激活参数。

### 现有方法的局限性
- 标准线性路由器的每 token 成本为 $\Theta(Mh)$，在专家数 $M$ 很大时，路由成本会反超专家计算本身，成为新的瓶颈。
- 现有降低路由成本的方案（如哈希路由、Top-k 近似）往往牺牲表达能力或负载均衡。
- 缺少"路由秩"与"表达能力/负载均衡"之间关系的理论刻画。

### 研究动机
作者提出一个关键问题：**路由器的秩能降到多低，而不损害表达能力与负载均衡？** 若能证明"对数秩足够"，就能在保持性能的同时，把路由成本从 $\Theta(Mh)$ 降到 $O((h+M)r)$，从而支撑"更多更小专家"的规模化。

## 研究问题

### 核心研究问题
- 路由器权重矩阵可被压缩到多低的秩，同时保证路由表达力与负载均衡？
- 低秩路由在理论上是否充分/必要？在系统上如何兑现为墙钟加速？

## 方法概述

### 核心思想
用低秩分解 $W_{\text{router}} = UV^\top$（秩 $r$）替代稠密路由矩阵，把每 token 路由成本从 $\Theta(Mh)$ 降为 $O((h+M)r)$；并从理论（表达力、负载均衡）与系统（融合 Triton 内核）两方面证明其可行性。

### 方法框架

#### 整体架构

![[teaser2_page1.png|700]]

> 图1：MoRE 概览。将路由器权重矩阵按秩 $r$ 分解，路由成本从 $\Theta(Mh)$ 降到 $O((h+M)r)$，在匹配激活 FLOPs 下可支撑更多专家。

#### 各模块详细说明

**模块1：低秩路由器**
- **功能**：把输入 token 路由到 $k$ 个激活专家。
- **输入**：token 隐藏状态 $x \in \mathbb{R}^h$。
- **输出**：专家选择 + 门控权重。
- **关键技术**：$W = UV^\top$，其中 $U \in \mathbb{R}^{h \times r}, V \in \mathbb{R}^{M \times r}$，计算成本 $O((h+M)r)$。

**模块2：理论保证**
- **功能**：证明对数秩 $r = O(\log M)$ 的表达力充分性、必要性与负载均衡保持性。
- **关键技术**：在固定激活专家数下分析路由表达力；在高斯记忆模型中证明负载均衡。

**模块3：融合 Triton 内核**
- **功能**：在推理时高效执行低秩路由，避免 HBM 上昂贵显存操作。
- **输出**：把理论 FLOPs 节省兑现为墙钟时间加速。
- **关键技术**：融合路由计算，减少中间张量的读写。

### 方法架构图

![[fig_rlr_switch_page1.png|700]]

> 图2：秩降低路由（RLR）在 Switch 类 MoE 上的路由示意图，展示低秩分解如何在专家数增大时控制路由开销。

### 关键创新
1. **低秩路由的完整理论刻画** - 证明对数秩对表达力充分且必要，且保持负载均衡。
2. **表达力与效率的兼顾** - 在匹配激活 FLOPs 下专家数可提升 $\Theta(h/r)$ 倍。
3. **系统级兑现** - 融合 Triton 内核把理论节省转化为实际墙钟加速。

## 实验结果

### 数据集
- 合成 phonebook 任务（记忆能力）。
- 知识密集型问答基准（预训练后评测）。

### 实验设置
- **基线方法**：标准线性路由 MoE。
- **评估指标**：phonebook 记忆、问答准确率、推理能力、墙钟时间。
- **实验环境**：A100 / B200 GPU，融合 Triton 内核。

### 主要结果

| 指标 | MoRE vs 基线 |
|------|-------------|
| 路由成本 | $\Theta(Mh)$ → $O((h+M)r)$ |
| 专家数（匹配 FLOPs） | 提升 $\Theta(h/r)$ 倍 |
| phonebook 记忆 | 改善 |
| 知识密集问答 | 提升 |
| 推理能力 | 持平 |

### 实验结果图

![[end_to_end_more_h512_page1.png|700]]

> 图3：端到端性能随专家数/隐藏维度的变化，展示 MoRE 在更多专家下仍保持较低路由开销并改善记忆/问答性能。

## 深度分析

### 研究价值
- **理论贡献**：首次系统刻画了"路由秩"与"表达力/负载均衡"的关系，为 MoE 路由压缩提供了原理依据。
- **实际应用**：直接支撑"更多更小专家"的前沿 MoE 架构，降低训练与推理成本。
- **领域影响**：MoE 高效路由方向的重要工作，理论-系统兼顾。

### 优势
- 理论与实验闭环，结论可信。
- 系统实现完整（开源 Triton 内核）。
- 明确界定了低秩路由的适用边界（对数秩、固定激活专家数）。

### 局限性
- 激活专家数固定假设；动态/可变激活下理论是否成立待验证。
- 知识密集问答提升、推理持平的结论需更大规模验证。
- 低秩路由对负载均衡的长期训练稳定性仍需观察。

### 适用场景
- 大规模 MoE 模型的训练/推理加速。
- 需要"更多专家"以摊薄激活参数的场景。

## 与相关论文对比

### 相关论文 - DeepSeekMoE / Mixtral（细粒度专家）
- **差异**：这些工作侧重专家切分与负载均衡策略，本文侧重路由器的低秩压缩。
- **改进**：从路由器维度解决"专家变多后的路由瓶颈"。

### 相关论文 - 哈希/近似路由
- **差异**：哈希路由牺牲表达力换取速度，本文用低秩在表达力与速度间取得理论保证的平衡。
- **改进**：有表达力充分性/必要性定理支撑。

## 技术路线定位

本文属于 **MoE 高效训练与路由**技术路线，主要关注 **低秩路由压缩**子方向。核心特点是：把"路由成本随专家数线性增长"的瓶颈，用有理论保证的低秩分解加以解决，并配系统内核兑现收益。

## 未来工作建议

1. 扩展到可变激活专家数、动态路由的场景。
2. 在更大规模（更多专家、更大模型）上验证长期训练稳定性与负载均衡。
3. 探索低秩路由与其他路由优化（如 expert choice、负载均衡损失）的组合。

## 我的综合评价

### 价值评分
- **总体评分**：9.0/10
- **分项评分**：
  - 创新性：9/10（低秩路由 + 完整理论刻画，新颖）
  - 技术质量：9/10（理论严谨 + 系统实现完整）
  - 实验充分性：8/10（合成 + 真实基准，可更大规模）
  - 写作质量：9/10（清晰）
  - 实用性：9/10（直接支撑前沿 MoE 架构，开源）

### 突出亮点
- "对数秩即可保证表达力与负载均衡"的理论结论。
- 融合 Triton 内核把理论收益兑现为墙钟加速。

### 可借鉴点
- "秩"作为 MoE 路由压缩的关键维度的思路。
- 理论（表达力/负载均衡）与系统（内核）并重的研究范式。

### 批判性思考
- 固定激活专家数的假设在真实负载中是否稳健？
- 知识密集问答的提升是否主要来自"更多专家"而非"低秩路由"本身？

## 我的笔记

%% 用户可以在这里添加个人阅读笔记 %%

## 相关论文
- [[How_to_Loop_MoE_Flatten_the_Experts,_Untie_the_Attention|How to Loop MoE: Flatten the Experts, Untie the Attention]] - 同为 MoE 架构效率的近期工作

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.36301)
- [PDF](https://arxiv.org/pdf/2609.36301)
- [代码](https://github.com/Matheart/MoRE_code)
