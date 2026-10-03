---
date: "2026-10-03"
paper_id: "arXiv:2610.02039"
title: "CARM: Cancellation-Aware Response Masking for LLM Reinforcement Learning"
authors: "Yafei Zhang, Songshuo Lu, Sicong Liao, Zhi Chen, Yaohua Tang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - Reinforcement-Learning
  - Response-Masking
  - Off-Policy
quality_score: "8.5/10"
related_papers: []
created: "2026-10-03"
updated: "2026-10-03"
status: analyzed
---

# CARM: Cancellation-Aware Response Masking for LLM Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2610.02039
- **作者**：Yafei Zhang, Songshuo Lu, Sicong Liao, Zhi Chen, Yaohua Tang
- **机构**：Moore Threads AI（摩尔线程）
- **发布时间**：2026-10-01
- **会议/期刊**：ICLR 2027 投稿（分类 cs.LG, cs.AI, cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2610.02039) | [PDF](https://arxiv.org/pdf/2610.02039)

## 摘要翻译

### 英文摘要
Recent years have witnessed the rapid adoption of reinforcement learning (RL) in large language model (LLM) post-training, with substantial gains in mathematical reasoning and code generation. In practical systems, however, policy updates and differences between rollout and training engines can make sampled responses off-policy. Sequence-level masking addresses this mismatch by deciding whether an entire response should contribute to optimization. A common masking rule uses the length-normalized geometric mean of sampled token probability ratios. Its signed log-ratios can cancel across positions, concealing substantial bidirectional policy drift. We propose Cancellation-Aware Response Masking (CARM), a sequence-level mask that takes the absolute value of each token log-ratio before averaging, preventing opposing probability changes from canceling. We prove that accepted responses satisfy a joint bound on the fraction of sampled-token ratios outside a prescribed band and their mean log-distance beyond its boundaries.

### 中文翻译
近年来，强化学习（RL）在大语言模型（LLM）后训练中得到迅速采用，在数学推理与代码生成上取得显著增益。但在实际系统中，策略更新以及 rollout 引擎与训练引擎之间的差异，会使采样到的响应偏离当前策略（off-policy）。序列级掩码（sequence-level masking）通过决定整条响应是否应参与优化来解决这种不匹配。一种常见掩码规则使用采样 token 概率比的长度归一化几何平均。然而，其带符号的 log-ratio 会跨位置相互抵消，掩盖了实质性的双向策略漂移。本文提出 Cancellation-Aware Response Masking（CARM），一种序列级掩码，在平均之前对每个 token log-ratio 取绝对值，从而防止相反的概率变化相互抵消。作者证明，被接受的响应满足一个联合界：采样 token 比值超出预设带的比例，以及其超出边界的平均 log 距离。

### 核心要点提炼
- **研究背景**：LLM RL 后训练中，策略更新与引擎差异导致采样响应 off-policy，需用序列级掩码筛选。
- **研究动机**：常见"几何平均掩码"的带符号 log-ratio 会跨位置**相互抵消**，掩盖真实的双向策略漂移。
- **核心方法**：CARM —— 对每个 token log-ratio 取**绝对值**再平均，构造抵消感知的序列级掩码。
- **主要结果**：数学推理（AIME/BeyondAIME）mean@16 最多提升 **3.13 分**，四个代码基准平均 pass@1 提升 **2.88 分**。
- **研究意义**：为 LLM RL 中的响应级 off-policy 控制提供有理论保证且有效的方案。

## 研究背景与动机

### 领域现状
- RL 已成为 LLM 后训练的核心手段，在数学推理、代码生成上带来显著提升。
- 实践中，rollout 引擎与训练引擎的差异、以及策略自身的更新，都会使采样到的响应与当前策略不一致（off-policy）。

### 现有方法的局限性
- 序列级掩码是应对 off-policy 的常见手段，其主流规则用"长度归一化的几何平均 token 概率比"。
- 但带符号的 log-ratio 在不同 token 上**正负相消**，会掩盖实质性的双向策略漂移，导致掩码失效。

### 研究动机
能否设计一种对"概率变化的抵消"敏感的掩码，从而更准确地识别应参与优化的响应？

## 研究问题

### 核心研究问题
如何构造一种序列级掩码，使其不会被 token 间相反方向的概率变化所抵消，从而更可靠地控制 LLM RL 中的响应级 off-policy？

## 方法概述

### 核心思想
CARM 的直觉很简单：既然带符号的 log-ratio 会相消，那就先对每个 token 的 log-ratio 取**绝对值**再平均。这样，无论概率变化是向上还是向下，都会被计入掩码分数，从而捕捉到真实的策略漂移。

### 方法框架

#### 整体架构
![[2610.02039_page1.png|600]]

> 图1：CARM 方法示意。对比"几何平均掩码"（带符号 log-ratio 相消）与 CARM（取绝对值再平均），展示后者如何避免双向策略漂移被掩盖。

- **几何平均掩码（基线）**：对每个 token 的 log-ratio 取带符号值后做长度归一化平均，正负相消会掩盖漂移。
- **CARM**：对每个 token log-ratio 取绝对值后再平均，构造抵消感知的序列级掩码。
- **理论保证**：被接受的响应满足"采样 token 比值超出预设带的比例 + 其平均 log 距离"的联合界。

### 关键创新
1. **抵消感知的掩码设计** —— 用绝对值消除 log-ratio 的正负相消，捕捉真实双向漂移。
2. **理论保证** —— 证明被接受响应满足联合界，方法有坚实的理论支撑。
3. **实践有效** —— 无需额外训练成本，即可在数学与代码任务上稳定提升。

## 实验结果

### 数据集
- **数学推理**：AIME 2024/2025/2026、BeyondAIME（mean@16）
- **代码生成**：四个代码基准（pass@1）

### 主要结果
- 数学推理：CARM 相对几何平均掩码，AIME/BeyondAIME 平均 mean@16 最多提升 **3.13 分**。
- 代码生成：四个代码基准平均 pass@1 相对最强基线提升 **2.88 分**。

## 深度分析

### 研究价值
- **理论贡献**：揭示了主流几何平均掩码的"抵消"缺陷，并给出有理论保证的替代方案。
- **实际应用**：为 LLM RL 后训练中的 off-policy 控制提供轻量、易部署的改进。
- **领域影响**：响应级掩码是 RLHF/GRPO 类方法的重要组件，CARM 可直接融入现有训练流程。

### 优势
- 改动小、无额外训练成本，工程落地容易。
- 理论证明清晰，方法动机直观可信。
- 在数学与代码两类代表性任务上均验证有效。

### 局限性
- 绝对值的引入是否丢失了方向信息（区分"有利"与"不利"漂移）值得进一步讨论。
- 评测聚焦数学与代码，泛化到更广泛任务仍有待验证。

### 未来工作
- 探索将方向信息与幅度信息结合的掩码设计。
- 在更多 RL 目标（如安全对齐、指令遵循）上验证 CARM 的普适性。
