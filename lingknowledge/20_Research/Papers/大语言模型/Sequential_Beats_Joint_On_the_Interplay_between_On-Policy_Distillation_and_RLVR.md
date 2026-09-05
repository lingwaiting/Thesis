---
date: "2026-09-05"
paper_id: "arXiv:2609.04108"
title: "Sequential Beats Joint: On the Interplay between On-Policy Distillation and RLVR"
authors: "Boyan Li, Bingsen Chen, Chenghao Yang, Ping Nie, Chen Zhao, Xi Ye"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 推理模型
  - On-Policy-Distillation
  - RLVR
  - 知识蒸馏
  - 强化学习
quality_score: "8.5/10"
created: "2026-09-05"
updated: "2026-09-05"
status: analyzed
---

# Sequential Beats Joint: On the Interplay between On-Policy Distillation and RLVR

## 核心信息

- **论文ID**：arXiv:2609.04108
- **作者**：Boyan Li, Bingsen Chen, Chenghao Yang, Ping Nie, Chen Zhao, Xi Ye
- **机构**：University of Alberta, New York University, University of Chicago, University of Waterloo, Amii
- **发布时间**：2026-09-03
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](http://arxiv.org/abs/2609.04108v1) | [PDF](https://arxiv.org/pdf/2609.04108v1)
- **引用**：--

## 摘要翻译

### 英文摘要

Reinforcement learning with verifiable rewards (RLVR) and on-policy distillation (OPD) have emerged as two dominant methods for post-training reasoning LLMs. Prior work uses OPD's dense token-level supervision to complement the sparse RL reward, fusing the two signals within a single step: either as a *weighted-additive combination* or a *teacher-modulated rescaling* of the RL advantage. In this paper, we show that a simple two-stage scheme, OPD-then-RL, consistently outperforms pure OPD, pure RLVR, and all such joint baselines across logic and math reasoning benchmarks. Beyond the empirical results, we further provide a systematic understanding through pass@k behavior, learning dynamics, and parameter updates, yielding a consistent explanation: OPD expands the student's coverage of teacher-supported solutions and RL sharpens within that support, while jointly optimizing the two signals causes them to interfere. To provide a practical recipe, we find that the OPD validation score is the key signal for when to switch to RL, and that OPD is a better cold start for RL than SFT. Together, our results establish OPD-then-RL as a simple yet strong way to combine the two methods, turning two entangled signals into complementary stages.

### 中文翻译

带可验证奖励的强化学习（RLVR）与在线策略蒸馏（OPD）已成为后训练推理大模型的两大主流方法。此前的工作用 OPD 稠密的 token 级监督来补充稀疏的 RL 奖励，并在单步内融合这两个信号：要么是*加权相加组合*，要么是*教师调制的 RL 优势缩放*。本文表明，一个简单的两阶段方案——先 OPD 后 RL（OPD-then-RL）——在逻辑与数学推理基准上一致地优于纯 OPD、纯 RLVR 以及所有此类联合基线。除了经验结果，作者还通过 pass@k 行为、学习动力学与参数更新提供了系统性理解，得到一个一致的解释：OPD 扩展了学生对教师支持解法的覆盖，RL 在该支撑范围内进行锐化；而联合优化这两个信号会导致它们相互干扰。作为实用配方，作者发现 OPD 验证分数是"何时切换到 RL"的关键信号，且 OPD 比 SFT 更适合作为 RL 的冷启动。综上，本文确立了 OPD-then-RL 这一简单而强力的方法，将两个纠缠的信号转化为互补的阶段。

### 核心要点提炼

- **研究背景**：RLVR 与 OPD 是推理 LLM 后训练的两大主流，业界普遍尝试在单步内联合两者。
- **研究动机**：联合优化（加权相加 / 优势缩放）虽直觉合理，但两个信号可能互相干扰。
- **核心方法**：拆成"先 OPD 后 RL"的两阶段方案，以 OPD 验证分数作为切换信号。
- **主要结果**：OPD-then-RL 在逻辑与数学基准上一致超越所有联合基线；OPD 是比 SFT 更好的 RL 冷启动。
- **研究意义**：为"如何组合 OPD 与 RLVR"提供了一个简单、可复现、机理清晰的配方。

## 研究背景与动机

### 领域现状

推理大模型的后训练已经形成了两条清晰的路线：

1. **RLVR（Reinforcement Learning with Verifiable Rewards）**：用可验证奖励（答案正确性）作为稀疏奖励信号，通过策略优化（如 PPO / GRPO）提升推理能力，但奖励稀疏、样本效率低。
2. **OPD（On-Policy Distillation）**：让学生模型在自生成的 rollout 上，接收教师模型稠密的 token 级监督，样本效率高，但受限于教师能力上限。

### 现有方法的局限性

主流的"联合"思路试图在单步内融合两种信号：

- **加权相加（weighted-additive）**：把 OPD 的稠密监督与 RL 的优势估计相加，隐式假设两者可以线性叠加。
- **教师调制缩放（teacher-modulated rescaling）**：用教师信号缩放 RL 优势。

这些方法的问题在于：两个信号的优化目标并不完全一致——OPD 希望学生尽量贴合教师的分布，RL 希望学生在奖励指引下探索偏离。在单步内硬融合会导致信号相互拉扯、更新方向冲突。

### 研究动机

如果两个信号在单步内会干扰，那么把它们放到时间上前后分离的两个阶段，是否就能各自发挥最大作用？本文的核心假设是：**OPD 负责"铺开"（扩大覆盖），RL 负责"锐化"（在覆盖范围内优化）**——二者是互补的，而非需要同时优化的竞争信号。

## 研究问题

### 核心研究问题

1. 简单地把 OPD 和 RL 拆成两阶段（先 OPD 后 RL），能否一致优于所有单步联合方案？
2. 两阶段方案为何有效？背后的学习动力学与参数更新机制是什么？
3. 在实践中，如何决定"何时从 OPD 切换到 RL"？用什么信号？

## 方法概述

### 核心思想

**OPD-then-RL**：第一阶段做纯 OPD，用教师的稠密监督"铺开"学生对教师支持解法的覆盖；第二阶段做纯 RLVR，在教师支撑的范围内"锐化"学生的策略。两个纠缠的信号被拆成时间上互补的两个阶段，避免了单步融合带来的干扰。

![[fig_opd_switch_dynamics_page1.png|600]]

> 图：OPD-then-RL 的切换动力学示意。OPD 阶段扩展解法覆盖，RL 阶段在覆盖内锐化，切换点由 OPD 验证分数决定。

### 方法框架

#### 整体架构

```
 SFT 冷启动 ──► 阶段一：OPD（稠密教师监督）──► 阶段二：RLVR（可验证奖励）──► 推理模型
                         │ 覆盖扩展                     │ 锐化
                         └──── 以 OPD 验证分数决定切换 ────┘
```

#### 各模块详细说明

**阶段一：On-Policy Distillation（OPD）**

- **功能**：让学生在自生成 rollout 上接收教师 token 级监督，扩大对教师支持解法的覆盖。
- **目标**：`$L_{\text{OPD}} = -\mathbb{E}_{x \sim \mathcal{D}, y \sim \pi_\theta}\sum_t \log \pi_\theta(y_t \mid x, y_{<t}) \cdot \mathbf{1}[\text{teacher agrees}]$`
- **作用**：这是 RL 的"冷启动"，为后续锐化提供一个足够宽的支撑集。

**阶段二：RLVR**

- **功能**：用可验证奖励做策略优化，在教师支撑的范围内锐化学生策略。
- **目标**：标准的优势最大化，奖励来自答案可验证性（如数学题答案对错）。
- **作用**：让模型在"已覆盖"的正确解空间里进一步收敛到高奖励区域。

**切换信号：OPD 验证分数**

- **机制**：监控第一阶段 OPD 的验证分数，当其趋于饱和（收益递减）时切换到 RL。
- **意义**：给出一个无需大量超参搜索的实用切换准则。

### 关键发现（机理）

作者通过 pass@k 行为、学习动力学与参数更新三个视角给出统一解释：

- **OPD 扩大覆盖**：学生能生成的"教师支持解法"数量随 OPD 增加。
- **RL 在支撑内锐化**：RL 提高已覆盖解法的奖励（正确性），但几乎不扩大覆盖。
- **联合优化互相干扰**：单步融合时，两个梯度方向冲突，导致覆盖与锐化都无法做好。

## 实验结果

### 实验目标

验证 OPD-then-RL 是否一致优于纯 OPD、纯 RLVR 及各类单步联合基线，并剖析其机理。

### 基准与评估

- **任务**：逻辑推理与数学推理基准（含 pass@k 评估）。
- **基线**：pure OPD、pure RLVR、加权相加联合、教师调制缩放联合。

### 主要结果

| 对比 | 结论 |
|------|------|
| OPD-then-RL vs 联合基线 | 在逻辑与数学基准上**一致更优** |
| OPD-then-RL vs pure OPD / pure RLVR | 均取得明显提升 |
| OPD vs SFT 作为 RL 冷启动 | **OPD 更优** |
| 切换信号 | OPD 验证分数是可靠的切换时机指标 |

![[fig_passk_three_panel_page1.png|600]]

> 图：pass@k 行为的三面板分析，说明 OPD 扩大覆盖、RL 在覆盖内锐化的互补关系。

## 深度分析

### 研究价值评估

#### 理论贡献

- **机理层面的清晰解释**：用"覆盖扩展 vs 支撑内锐化"这一框架，统一解释了为何两阶段优于联合。
- **对既有直觉的纠正**：挑战了"两个信号在单步内互补融合"的主流做法，主张"时间上分离"。

#### 实际应用价值

- **实用配方**：给出"先 OPD 后 RL + OPD 验证分数切换"这一可直接复现的训练流程。
- **成本友好**：无需额外模型或复杂损失设计，仅改变训练顺序。

### 方法优势详解

- **简单性**：两阶段方案实现成本极低，不引入新损失项或超参。
- **机理可解释**：通过 pass@k / 动力学 / 参数更新三个视角交叉验证。
- **强基线**：OPD 作为 RL 冷启动优于 SFT，具有独立价值。

### 局限性分析

- **推理基准范围**：主要在逻辑与数学推理上验证，代码 / 通用推理上的泛化性有待检验。
- **切换信号的经验性**：OPD 验证分数作为切换准则仍需更多场景验证其稳健性。

## 我的综合评价

### 价值评分

#### 总体评分

**8.5/10** — 观点简洁有力、机理清晰、实用性强，是 OPD×RLVR 组合问题上一份值得精读的工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 两阶段拆分的洞见简单但反直觉，纠正主流联合做法 |
| 技术质量 | 8/10 | 机理分析扎实，多视角交叉验证 |
| 实验充分性 | 8/10 | 逻辑+数学基准覆盖充分，对比基线齐全 |
| 写作质量 | 9/10 | 结构清晰、结论明确 |
| 实用性 | 9/10 | 训练流程可直接落地 |

## 相关论文

- [[Rethinking_On-Policy_Distillation_of_Large_Language_Models_II_One_Training_Example|Rethinking On-Policy Distillation II]] - 同期工作，从数据角度剖析 OPD（数据过喂、算法饥饿）
- 相关技术路线：RLVR、GRPO、On-Policy Distillation 系列

> [!tip] 关键启示
> 两个看似互补的监督信号，**未必适合在同一优化步内融合**；把它们拆成时间上互补的两阶段，往往既简单又更强。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐给关注推理模型后训练与 OPD/RL 组合的研究者。
