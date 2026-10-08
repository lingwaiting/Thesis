---
date: "2026-10-08"
paper_id: "arXiv:2610.09003"
title: "Algorithmic Scratchpads and Curriculum Staging for Arithmetic Reasoning in Tiny Transformers"
authors: "Sourabh Kasliwal"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Reasoning
  - Arithmetic
  - Tiny-Transformer
  - MoE
  - Curriculum-Learning
quality_score: "8.0/10"
related_papers: []
created: "2026-10-08"
updated: "2026-10-08"
status: analyzed
---

# Algorithmic Scratchpads and Curriculum Staging for Arithmetic Reasoning in Tiny Transformers

## 核心信息
- **论文ID**：arXiv:2610.09003
- **作者**：Sourabh Kasliwal
- **机构**：--
- **发布时间**：2026-10-06
- **分类**：cs.LG / cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2610.09003) | [PDF](https://arxiv.org/pdf/2610.09003)
- **引用**：--

## 摘要翻译

### 英文摘要
Autoregressive LLMs frequently struggle with deterministic multi-step algorithmic tasks such as multi-digit multiplication and long division. This paper investigates multi-step arithmetic in compact "Tiny" Transformers (~10.6M non-embedding params, 49.3M total) trained on synthetic data across four operations unrolled as step-by-step scratchpads. Key training foundations: (1) dataloader sequence padding creates an 83% gradient starvation artifact collapsing accuracy 40%→1%, remediated via continuous sequence packing; (2) linguistic pretraining is essential (≤2.0% without); (3) modern primitives (RoPE, RMSNorm, SwiGLU) + Sparse MoE substantially improve additive reasoning over GPT-2. On algorithmic scratchpad formulation, a Digit-by-Digit Long Division scratchpad within a 4-stage Hierarchical Developmental Curriculum elevates single-digit division 4.0%→86.7%. Multiplication remains hard: FOIL scratchpad failed because it forced simultaneous summation of up to nine multi-digit terms without pairwise accumulation. Two boundaries identified: 0.00% on unseen 4-digit operands; unbuffered training induces catastrophic forgetting (86.7%→0.00%).

### 中文翻译
自回归大语言模型在确定性的多步算法任务（如多位数乘法和长除法）上经常表现不佳。本文在约 10.6M 非嵌入参数（总计 49.3M）的紧凑"Tiny" Transformer 上，用合成数据研究四种基本运算（+、-、*、/）展开为逐步 scratchpad 的多步算术机制。首先确立必要的训练基础：(1) 数据加载器的序列填充造成 83% 的"梯度饥饿"伪影，使准确率从 40% 暴跌到 1%，通过连续序列打包修复；(2) 语言预训练是必要前提（否则 ≤2.0%）；(3) 现代架构原语（RoPE、RMSNorm、SwiGLU）与稀疏混合专家（MoE）相比 GPT-2 显著提升加法推理。在 scratchpad 公式化方面，在四阶段"层级发展课程"中引入确定性的逐位长除法 scratchpad，将个位除法准确率从 4.0% 提升到 86.7%。相比之下，多位数乘法依然困难：详细误差分析显示，模型虽能正确计算单数字乘积与进位零，但 FOIL scratchpad 因强制在单步内同时累加多达九个多位数项、而缺乏逐对中间累加而失败。最后识别出两个边界：对未见过的 4 位操作数性能崩溃到 0.00%；无缓冲训练导致灾难性遗忘，除法准确率从 86.7% 跌到 0.00%。

### 核心要点提炼
- **研究背景**：LLM 在确定性多步算术任务上不可靠
- **研究动机**：剖析 Tiny Transformer 学算术的机制与瓶颈
- **核心方法**：scratchpad 公式化 + 层级发展课程 + 连续序列打包
- **主要结果**：个位除法 4.0%→86.7%；揭示乘法与 4 位泛化的失败边界
- **研究意义**：为"小模型可解释算术推理"提供机制性洞察与训练配方

## 研究背景与动机

### 领域现状
大语言模型在自然语言推理上表现优异，但在确定性的多步算法任务（多位乘法、长除法）上却意外脆弱。这类任务本应"确定、可验证"，却成为 LLM 的短板，说明算术能力并非简单规模扩张就能解决。

### 现有方法的局限性
- 大模型在长乘法/除法上错误率高
- 缺乏对小模型如何习得算术的机制性理解
- 训练细节（padding、预训练、架构选择）对算术能力的影响未被系统研究

### 研究动机
在可完全控制的 Tiny Transformer + 合成数据环境下，系统地拆解"哪些训练配方、哪些 scratchpad 格式"决定了算术推理的成败，从而为小模型的算法推理能力提供可复现的路线图。

## 研究问题

### 核心研究问题
紧凑的自回归 Transformer 如何可靠地习得多步算术？哪些训练基础（数据、预训练、架构）和哪些 scratchpad 公式化是成败的关键，泛化与遗忘的边界在哪里？

## 方法概述

### 核心思想
用合成数据训练 Tiny Transformer（~10.6M 参数）执行四则运算，将每一步展开为逐步 scratchpad（chain-of-thought 式的中间步骤），并通过"层级发展课程"由易到难地引入运算类型，从而剖析算术推理的机制。

### 方法框架

![[figure1_hierarchical_trajectory_page1.png|600]]

> 图1：层级发展课程下的训练轨迹——展示四阶段课程如何逐步引入更复杂的算术操作，以及关键训练基础对准确率的提升。

### 关键创新

1. **三项训练基础的系统性发现** - 定位到 83% 的"梯度饥饿"伪影（由 padding 引起）、语言预训练的必要性、现代架构原语 + MoE 的作用
2. **逐位长除法 scratchpad** - 确定性 Digit-by-Digit 公式化，配合 4 阶段层级课程，将除法准确率拉升至 86.7%
3. **失败边界的机制性归因** - 精确解释乘法 FOIL scratchpad 为何失败（单步累加九项、缺逐对中间累加），以及 4 位操作数与遗忘的边界

## 实验结果

### 数据集 / 实验设置
- 数据：合成数据，四种运算（+、-、*、/）逐步展开为 scratchpad
- 模型：Tiny Transformer（~10.6M 非嵌入参数，49.3M 总参数）
- 架构原语：RoPE、RMSNorm、SwiGLU、Sparse MoE
- 基准：4,000 题 held-out 测试集

### 主要结果
- 连续序列打包修复 83% 梯度饥饿：准确率 40%→1% 恢复
- 语言预训练：缺失时 ≤2.0%
- 逐位长除法 scratchpad + 4 阶段课程：个位除法 4.0%→86.7%
- 乘法：FOIL scratchpad 失败（单步累加 9 项）
- 泛化边界：未见 4 位操作数 → 0.00%；无缓冲训练 → 灾难性遗忘（86.7%→0.00%）

## 深度分析

### 研究价值
- **理论贡献**：对小模型算术推理机制（梯度饥饿、预训练作用、scratchpad 格式、遗忘边界）提供精细的实证刻画
- **实际应用**：为边缘/端侧小模型的数学能力训练提供配方
- **领域影响**：呼应"能力涌现依赖训练细节"这一日益受重视的方向

### 优势
- 实验设计干净可控（合成数据 + 小模型），因果归因清晰
- 发现了"梯度饥饿"这类容易被忽略的训练陷阱
- 对失败案例（乘法、4 位泛化、遗忘）的分析有深度

### 局限性
- 仅覆盖合成数据上的四则运算，与真实数学推理仍有距离
- 单作者，规模有限（Tiny 模型），结论外推到大规模模型需谨慎
- 乘法失败未给出有效解决配方

### 适用场景
- 端侧/低资源模型的算术与算法推理能力训练
- 训练数据管线（padding/序列打包）的工程优化

## 技术路线定位

本文属于**可解释的模型能力机制研究**路线，具体聚焦"小模型算术推理的机制与训练配方"，用可控实验拆解能力涌现的边界条件。

## 我的综合评价

### 价值评分
- **总体评分**：8.0/10
- **创新性**：8/10（机制性洞察而非新架构）
- **技术质量**：8/10
- **实验充分性**：8/10（消融全面，失败分析到位）
- **写作质量**：8/10
- **实用性**：7/10

### 突出亮点
- 定位 83% 梯度饥饿这一隐蔽训练陷阱
- 逐位除法 scratchpad 带来 4%→86.7% 的戏剧性提升
- 失败边界的精确归因（乘法/泛化/遗忘）

### 重点关注
- "语言预训练是算术前提"这一发现值得深入
- 连续序列打包对梯度健康的通用意义

### 可借鉴点
- 用合成数据 + Tiny 模型做机制归因的方法论
- 层级发展课程 + 确定性 scratchpad 的训练配方
- 对"灾难性遗忘"与"操作数长度泛化"的边界刻画

## 相关论文
- 待补充

## 外部资源
- [arXiv](https://arxiv.org/abs/2610.09003)
- [PDF](https://arxiv.org/pdf/2610.09003)
