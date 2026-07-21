---
date: "2026-07-21"
paper_id: "arXiv:2607.16872"
title: "Trace-Based On-Policy Distillation for Masked Diffusion Language Models"
authors: "Haolin Ren, Ziyang Huang, Chenhao Yuan, Jun Zhao, Kang Liu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Diffusion-Language-Model
  - On-Policy-Distillation
  - LLM-Post-training
  - Mathematical-Reasoning
  - Knowledge-Distillation
quality_score: "8.5/10"
created: "2026-07-21"
updated: "2026-07-21"
status: analyzed
---

# Trace-Based On-Policy Distillation for Masked Diffusion Language Models

## 核心信息
- **论文ID**：arXiv:2607.16872
- **作者**：Haolin Ren, Ziyang Huang, Chenhao Yuan, Jun Zhao, Kang Liu
- **机构**：--
- **发布时间**：2026-07-18
- **会议/期刊**：arXiv 预印本（cs.CL, cs.AI, cs.LG）
- **链接**：[arXiv](http://arxiv.org/abs/2607.16872v1) | [PDF](https://arxiv.org/pdf/2607.16872v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Diffusion large language models (dLLMs) are a promising alternative to autoregressive generation. However, reasoning-oriented post-training for dLLMs remains challenging. SFT for dLLMs requires dense but often off-policy masked states, while RL relies on sparse rewards or value modeling. This paper proposes trace-based on-policy distillation (TOPD), a teacher-supervised framework that transfers reasoning ability to a target dLLM without reward estimation. The key idea is to supervise a dLLM on its own denoising trajectory, focusing on the trace-aligned token decisions that form the final response. TOPD samples on-policy diffusion trajectories from the target dLLM, obtains teacher token distributions from a teacher model on the corresponding partially denoised states, and updates the target dLLM with a token-level Reverse-KL objective. On mathematical reasoning benchmarks, TOPD enables SDAR-4B-Chat to match the MATH500 accuracy of its RL-trained counterpart TraDo-4B-Instruct, with gains of +5.7 under static evaluation and +4.5 under dynamic evaluation. Compared with the RL-trained counterpart, TOPD achieves this with 4× fewer rollout rounds, corresponding to an estimated 96.0× to-accuracy model-compute speedup.

### 中文翻译
扩散大语言模型（dLLM）是自回归生成的一个有前景的替代方案。然而，面向推理的 dLLM 后训练仍具挑战性。dLLM 的监督微调（SFT）需要密集但通常是离线的掩码状态，而强化学习（RL）依赖于稀疏奖励或价值建模。本文提出基于轨迹的在线策略蒸馏（TOPD），一个无需奖励估计即可将推理能力迁移至目标 dLLM 的教师监督框架。其核心思想是在 dLLM 自身的去噪轨迹上进行监督，聚焦于构成最终响应的轨迹对齐 token 决策。具体而言，TOPD 从目标 dLLM 采样在线策略扩散轨迹，从教师模型获取对应部分去噪状态的 token 分布，并使用 token 级 Reverse-KL 目标更新目标 dLLM。在数学推理基准上，TOPD 使 SDAR-4B-Chat 达到了其 RL 训练版本 TraDo-4B-Instruct 的 MATH500 准确率，静态评估 +5.7、动态评估 +4.5。与 RL 训练版本相比，TOPD 以 4 倍的 rollout 轮次减少实现了这一点，换算为约 96.0 倍的到达准确率模型计算加速。

### 核心要点提炼
- **研究背景**：dLLM 的后训练方法（SFT/RL）各自存在离线偏差或稀疏奖励问题，在线策略蒸馏从自回归模型迁移到扩散模型面临轨迹对齐的关键挑战
- **研究动机**：现有 dLLM 后训练中随机掩码可能暴露后续答案而隐藏前置推理变量，导致训练状态与推理时去噪轨迹不一致
- **核心方法**：TOPD — 采样学生模型在线策略去噪轨迹 → 提取轨迹对齐的 token 决策 → 教师分布匹配 → Reverse-KL 优化
- **主要结果**：SDAR-4B-Chat 以 4× 更少的 rollout 达到 RL 方法的 MATH500 准确率，计算加速 96 倍
- **研究意义**：首次证明在线策略蒸馏可高效替代 RL 进行 dLLM 推理后训练，大幅降低训练成本

## 研究背景与动机

### 领域现状
扩散大语言模型（dLLM）通过迭代去噪而非逐 token 自回归生成文本，提供了一种竞争性的非自回归替代方案。代表性工作包括 DiffusionLM、MDLM、SDAR、Dream 等。该类模型的后训练主要遵循两条路线：

1. **监督微调（SFT）**：在外部构造的目标上进行训练，但训练状态（随机掩码版本的目标响应）与学生自身生成策略产生的状态不一致，导致曝光偏差（exposure bias）
2. **强化学习（RL）**：基于学生自身采样，但使用稀疏、延迟的奖励信号，长程信用分配（credit assignment）困难且昂贵

### 现有方法的局限性
核心问题在于**随机掩码不匹配（random-mask mismatch）**：当前 dLLM 的 RL 流程通常通过随机掩码部分 token 构造训练状态，这可能导致：
- 暴露下游答案（如最终结果 66），同时隐藏上游推理变量（如 $80 × (1-25\%)$）
- 创建"反向重建"的训练上下文，与推理时前向去噪轨迹不一致
- 揭示顺序和推理调度显著影响掩码扩散行为

### 研究动机
在线策略蒸馏（OPD）在自回归模型中取得成效，但直接移植到 dLLM 面临根本困难：扩散步骤同时操作多个并行 token 决策，必须确定哪些部分去噪状态和 token 位置应接收教师反馈。

## 研究问题

### 核心研究问题
如何使在线策略蒸馏**既在线（on-policy）又轨迹感知（trajectory-aware）**——学生应在自身去噪过程采样的状态上接受监督，且教师信号应按轨迹构建而非独立采样掩码来组织。

## 方法概述

### 核心思想
TOPD 的核心洞察：不应监督任意随机掩码状态，而应监督学生自身推理时的扩散轨迹中**真正构成最终响应的 token 决策**。这避免了"答案暴露但推理隐藏"的训练偏差。

### 方法框架

#### 整体架构

![[diffopd_full_pipeline.png|800]]

> 图1：TOPD 整体流程 — (1) 学生采样在线策略去噪轨迹，(2) 提取轨迹对齐的 token 决策，(3) 教师在学生访问的状态上提供分布，(4) Reverse-KL 优化

#### 各模块详细说明

**模块1：在线策略扩散轨迹采样（On-Policy Diffusion Trajectories）**

- **功能**：从当前学生模型采样推理时去噪轨迹
- **输入**：prompt $q$，完全掩码的初始响应状态 $s_0$
- **输出**：轨迹 $\tau = (s_0, a_0, s_1, a_1, \ldots, s_{T-1}, a_{T-1}, s_T)$
- **处理流程**：
  1. 从 $s_0$（全 MASK）开始
  2. 每个扩散步骤 $t$：学生预测选中掩码位置的 token
  3. 更新状态至 $s_{t+1}$，记录 token 决策集 $a_t = \{(j, x_j)\}$
  4. 重复 $T$ 步至 $s_T$（最终响应）
- **关键技术**：采样自当前学生策略，确保训练状态与推理行为一致

**模块2：轨迹对齐决策选择（Trace-Aligned Decision Selection）**

- **功能**：从轨迹中仅保留最终响应中存活的 token 决策
- **输入**：完整轨迹 $\tau$
- **输出**：轨迹对齐子集 $\tilde{a}_t = \{(j, x_j) \in a_t : x_j = s_{T,j}\}$
- **处理流程**：
  1. 对每个扩散步骤 $t$ 的每个 token 决策
  2. 检查该 token 是否在最终响应 $s_T$ 中保持不变
  3. 仅保留"存活"的决策
- **关键设计**：后期去噪可能修订或覆盖临时提议，轨迹对齐确保仅对输出形成性决策进行监督

**模块3：逐步教师分布匹配（Step-Wise Teacher Distribution Matching）**

- **功能**：在轨迹对齐的状态-位置上评估教师和学生分布
- **输入**：轨迹对齐决策集 $\tilde{a}_t$、教师模型 $\pi_{tea}$、学生模型 $\pi_\theta$
- **输出**：教师分布 $p^{tea}_{t,j}$ 和学生分布 $p^{\theta}_{t,j}$
- **处理流程**：
  1. 对每个轨迹对齐的 $(j, x_j) \in \tilde{a}_t$
  2. 在同一状态-位置对 $(q, s_t, j)$ 上评估教师分布 $p^{tea}_{t,j}(\cdot) = \pi_{tea}(\cdot \mid q, s_t, j)$
  3. 评估学生分布 $p^{\theta}_{t,j}(\cdot) = \pi_{\theta}(\cdot \mid q, s_t, j)$
- **优势**：状态匹配而非答案匹配——教师在学生访问的状态上提供反馈

**模块4：Reverse-KL 训练目标**

- **功能**：将教师分布转换为稳定 token 级更新的目标函数
- **数学公式**：
  $$\mathcal{L}_{\text{TOPD}}(\theta) = \frac{1}{BG} \sum_{i=1}^{BG} \sum_{t: |\tilde{a}_{t,i}|>0} \frac{1}{|\tilde{a}_{t,i}|} \sum_{(j,x_j)\in\tilde{a}_{t,i}} D_{KL}\left(p^{\theta}_{t,j}(\cdot) \,\|\, p^{tea}_{t,j}(\cdot)\right)$$
- **核心设计**：
  - 按轨迹归一化，再按扩散步骤内保留位置归一化——防止多 token 步骤主导更新
  - Reverse-KL（$D_{KL}(student \| teacher)$）将学生集中于教师偏好模式，在噪声部分去噪上下文中抑制发散
  - 实现上使用采样 token 得分函数估计器：$r_{t,j} = \text{sg}(\log p^{tea}_{t,j}(x_j) - \log p^{\theta}_{t,j}(x_j))$

### 方法架构图

![[diffopd_comparison.png|800]]

> 图2：TOPD 与随机掩码 RL 的概念对比 — 左侧：在线策略状态保持因果关系方向；右侧：随机掩码状态破坏因果推理路径

## 实验结果

### 实验目标
验证 TOPD 在三个维度上的有效性：(1) 是否能在数学推理基准上匹配 RL 后训练，(2) 在线策略状态、轨迹对齐 token 选择、Reverse-KL 各自贡献，(3) 密集教师监督是否降低训练成本。

### 数据集

| 数据集 | 类型 | 用途 |
|--------|------|------|
| MATH（Level 3-5） | 数学推理 | 训练集（8K 任务） |
| MATH500 | 数学推理 | 评估 |
| AIME2024 | 竞赛数学 | 评估 |
| GSM8K | 小学数学 | 评估 |

### 实验设置

#### 基线方法
- **SFT**：使用 TraDo-8B-Instruct 生成的响应进行半自回归目标训练
- **ESPO**：LLaDA-8B-Instruct 的 RL 训练版本
- **TraceRL / TraDo**：SDAR-4B-Chat 的 RL 训练版本（使用 TraceRL 方法）

#### 评估设置
- 模型配置：学生 SDAR-4B-Chat（block-attention），教师 TraDo-8B-Instruct
- 扩散设置：4-token blocks，4 denoising steps/block，每 rollout 轮 64 prompts
- 评估策略：静态解码和动态解码，avg@3（MATH500/GSM8K）或 avg@20（AIME2024）

### 主要结果

#### 主实验结果（SDAR-4B-Chat 学生模型）

| 方法 | MATH500-Static | MATH500-Dynamic | AIME2024-Static | AIME2024-Dynamic | GSM8K-Static | GSM8K-Dynamic | 每轮加速 | 到达准确率加速 |
|------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| SFT | 51.5 | 49.6 | 0.7 | 1.3 | 82.2 | 82.0 | -- | -- |
| TraceRL (TraDo-4B) | 60.4 | 60.3 | 3.9 | 3.6 | **87.7** | 87.3 | 1× | 1× |
| **TOPD** | **66.1** | **64.8** | **4.4** | **4.7** | 87.3 | **88.7** | **4.0×** | **96.0×** |

#### 结果分析
- TOPD 在 MATH500 上大幅超越 TraceRL（+5.7 静态/+4.5 动态）
- AIME2024 上 TOPD 也优于 TraceRL（+0.5 静态/+1.1 动态）
- GSM8K 上两者相当
- **关键效率优势**：TOPD 仅需 1/4 的 rollout 轮次，每轮计算速度更快（每轮加速 4×），综合产生 96× 到达准确率加速
- TOPD 在 LLaDA-8B（full-attention）上也有效，达到 10.9× 每轮加速和 511.6× 到达准确率加速

### 消融实验

#### 消融结果

| 消融变体 | MATH500-Static | MATH500-Dynamic |
|----------|:---:|:---:|
| TOPD（完整） | **66.1** | **64.8** |
| 去掉在线策略状态（Off-Policy SFT） | 显著下降 | 显著下降 |
| 随机掩码 + 蒸馏（去掉轨迹对齐） | 下降 | 下降 |
| Forward-KL 替代 Reverse-KL | 下降 | 下降 |

### 实验结果图

![[response_length_math500.png|600]]

> 图3：MATH500 响应长度分析 — 显示不同方法的生成行为差异

## 深度分析

### 研究价值评估

#### 理论贡献
- **形式化 dLLM 在线策略蒸馏问题**：首次系统性地形式化了"随机掩码不匹配"在 dLLM 后训练中的问题，揭示了随机掩码可能创建与推理时去噪轨迹不一致的条件依赖
- **轨迹对齐监督范式**：提出 trace-aligned 的概念——仅对最终响应中存活的 token 决策进行监督，这为扩散模型的教师监督提供了理论基础
- **状态匹配 vs 答案匹配**：论证了状态级匹配（同一学生状态上的教师分布）优于答案级匹配（固定目标响应上的掩码目标）

#### 实际应用价值
- **大幅降低 dLLM 后训练成本**：96× 的到达准确率加速意味着从数周的训练时间缩短到数小时
- **跨架构通用性**：在 block-attention（SDAR）和 full-attention（LLaDA）架构上均有效
- **无需奖励工程**：避免了 RL 方法中奖励函数设计、价值模型训练等复杂工程

#### 领域影响
- **短期**：为 dLLM 推理后训练提供高效替代方案，降低入门门槛
- **中期**：可能推动 dLLM 从"仅预训练+RL"向"预训练+蒸馏"的范式转变
- **长期**：在线策略蒸馏框架可能扩展到其他非自回归生成模型类别

### 方法优势详解

1. **在线策略状态对齐**：训练状态来自学生自身去噪过程，消除曝光偏差
2. **轨迹感知监督**：仅对构成最终输出的决策进行监督，避免在临时/被修订的决策上浪费训练信号
3. **密集 token 级反馈**：每个轨迹对齐 token 接收教师分布，比稀疏奖励提供更丰富的学习信号
4. **Reverse-KL 模式集中**：在噪声部分去噪状态下，Reverse-KL 让学生集中于教师偏好模式

### 局限性分析

1. **依赖强教师**：TOPD 效果取决于教师模型质量；若师生差距过大，蒸馏效果可能受限
2. **仅验证数学推理**：实验限于数学推理（MATH/AIME/GSM8K），在其他推理类型（代码、逻辑等）上待验证
3. **4B 规模验证**：仅在 4B/8B 参数规模验证，更大规模（70B+）上是否需要调整超参数未知
4. **轨迹采样成本**：虽然减少 rollout 轮次，但每轮仍需采样完整去噪轨迹

### 适用性与场景分析

**适用场景**：
- 有强教师 dLLM 可用，需训练较小学生 dLLM 的场景
- 数学推理和需要长程推理链的任务
- 计算预算有限但追求高性能的场景

**不适用场景**：
- 无合适教师模型的场景
- 推理能力非核心需求的任务（如创意写作）

## 技术路线定位

### 所属技术路线
本文属于 **dLLM 后训练 × 知识蒸馏** 交叉方向，核心特点：
- 延续 TraceRL 的轨迹构建原则，但以蒸馏替代 RL
- 桥接了自回归 OPD（Agarwal 2024）和扩散模型后训练两个领域

### 技术路线发展历程
```
MDLM/SDAR (预训练) → TraceRL (RL后训练) → TOPD (蒸馏后训练) → 未来: 多模态dLLM蒸馏
```

## 未来工作建议

1. **扩展到更多推理领域**：代码生成、定理证明、多跳问答
2. **更大规模验证**：70B+ 参数级别，验证可扩展性
3. **多教师蒸馏**：多个教师模型的分布融合
4. **在线教师更新**：教师随学生进步同步更新（迭代蒸馏）

## 我的综合评价

### 价值评分

**总体评分**：**8.5/10** — 首次在 dLLM 后训练中系统性地解决随机掩码不匹配问题，以蒸馏高效替代 RL，理论和实验均扎实。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 将 OPD 适配到 dLLM 轨迹层面是新鲜且非平凡的贡献 |
| 技术质量 | 9/10 | 方法设计严谨（轨迹采样→对齐→匹配→优化），理论动机清晰 |
| 实验充分性 | 8/10 | 多基准、双架构验证、消融实验完整，但规模限于 4B/8B |
| 写作质量 | 9/10 | 问题定义清晰，图示（Fig 2 的随机掩码不匹配示例尤其出色）说服力强 |
| 实用性 | 8/10 | 96× 加速极具实用价值，方法无需复杂奖励工程 |

## 相关论文

### 直接相关
- [[TraDo / TraceRL]] - 轨迹构建原则的来源，TOPD 以蒸馏替代其 RL 部分
- [[SDAR]] - TOPD 的主要学生/基线模型

### 背景相关
- [[MDLM]] - 掩码扩散语言模型基础
- [[On-Policy Distillation]] - 自回归 OPD 概念来源
- [[ESPO / D1]] - dLLM RL 后训练同期工作

> [!tip] 关键启示
> 扩散模型后训练的核心不是"在哪里监督"，而是"在哪个状态上以什么顺序监督"——训练状态必须匹配推理轨迹，这比 RL 中的奖励函数设计更重要。

> [!warning] 注意事项
> - TOPD 依赖教师模型质量，强教师 + 弱教师场景下推荐使用
> - 轨迹对齐假设去噪轨迹中的 token 决策是"最终"或"被覆盖"的二元关系，更细粒度的部分贡献建模可能是改进方向
> - 目前仅验证数学推理，代码/逻辑推理需额外验证

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！dLLM 后训练方向的重要进展，96× 加速对实践者极具吸引力，方法设计优雅，图示清晰易懂。
