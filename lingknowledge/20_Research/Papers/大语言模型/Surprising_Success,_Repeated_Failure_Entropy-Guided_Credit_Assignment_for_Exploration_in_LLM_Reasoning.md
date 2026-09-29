---
date: "2026-09-29"
paper_id: "arXiv:2609.33781"
title: "Surprising Success, Repeated Failure: Entropy-Guided Credit Assignment for Exploration in LLM Reasoning"
authors: "Woongyeong Yeo, Minki Kang, Chanuk Lee, Sangwoo Park, Jinheon Baek, Sung Ju Hwang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 强化学习
  - RLVR
  - Credit-Assignment
  - Exploration
  - Reasoning
quality_score: "8.8/10"
created: "2026-09-29"
updated: "2026-09-29"
status: analyzed
---

# Surprising Success, Repeated Failure: Entropy-Guided Credit Assignment for Exploration in LLM Reasoning

## 核心信息
- **论文ID**：arXiv:2609.33781
- **作者**：Woongyeong Yeo, Minki Kang, Chanuk Lee, Sangwoo Park, Jinheon Baek, Sung Ju Hwang
- **机构**：KAIST
- **发布时间**：2026-09-27
- **链接**：[arXiv](https://arxiv.org/abs/2609.33781) | [PDF](https://arxiv.org/pdf/2609.33781) | [项目页](https://eapo-explore.github.io) | [代码](https://github.com/wgcyeo/EAPO)
- **分类**：cs.LG, cs.AI, cs.CL

## 摘要翻译

### 英文摘要
Reinforcement learning with verifiable rewards (RLVR) enhances reasoning in large language models (LLMs) through outcome-level feedback, yet recent approaches to finer-grained credit assignment often require auxiliary models, additional sampling, or privileged information. Although policy entropy provides a readily available signal, prioritizing uncertain positions under both reinforcement and penalization concentrates penalties where failed responses still retain alternatives for recovery, which can suppress opportunities for exploration. To address this, we introduce Entropic Advantage Policy Optimization (EAPO), an entropy-guided credit assignment method that treats success and failure asymmetrically. Specifically, motivated by the observation that success under uncertainty is less repeatable while confident failures tend to recur, EAPO couples normalized policy entropy with the sign of the response advantage to reinforce surprising success and correct repeated failure. It assigns stronger reinforcement to high-entropy decisions in successful responses and stronger penalties to low-entropy decisions in failed responses, while attenuating penalties at uncertain positions to preserve opportunities for recovery. By redistributing the response advantage across tokens, EAPO derives token-level credit directly from existing rollout signals without additional supervision.

### 中文翻译
带可验证奖励的强化学习（RLVR）通过结果级反馈增强大语言模型（LLM）的推理能力，但近期面向更细粒度信用分配的方法往往需要辅助模型、额外采样或特权信息。尽管策略熵提供了一个现成可用的信号，但在强化与惩罚两个方向上同时优先处理"不确定"位置，会把惩罚集中在那些失败响应仍保留恢复替代方案的位置上，从而抑制探索机会。为此，作者提出 **Entropic Advantage Policy Optimization（EAPO）**，一种以熵引导的信用分配方法，非对称地对待成功与失败。具体而言，基于"不确定性下的成功难以复现，而自信的失败倾向复发"这一观察，EAPO 将归一化策略熵与响应优势（advantage）的符号耦合，从而**强化意外成功、纠正重复失败**：对成功响应中的高熵决策施加更强强化，对失败响应中的低熵决策施加更强惩罚，同时在不确定位置衰减惩罚以保留恢复机会。通过把响应级优势重新分配到 token 上，EAPO 直接从现有 rollout 信号中推导出 token 级信用，无需额外监督。

### 核心要点提炼
- **研究背景**：RLVR 已证明能通过结果级反馈增强 LLM 推理，但细粒度（token 级）信用分配困难。
- **研究动机**：现有细粒度方法依赖辅助模型/额外采样/特权信息，且"对不确定位置一律强化/惩罚"会抑制探索。
- **核心方法**：用"策略熵 × 优势符号"做非对称信用分配，提出 EAPO。
- **主要结果**：在多种推理任务、base 与 reasoning 两类骨干上取得整体最优性能，并促进更有效的探索。
- **研究意义**：无需额外监督即可从现有 rollout 信号得到 token 级信用，为 RLVR 信用分配提供了简洁有效的新范式。

## 研究背景与动机

### 领域现状
RLVR（如 GRPO、RLOO 等）通过结果级奖励信号显著提升了 LLM 的数学、代码等推理能力。其核心局限在于：结果级反馈只告诉"整条响应是否正确"，无法区分单个 token 的贡献，导致信用分配（credit assignment）粗糙。

### 现有方法的局限性
为获得更细粒度的信用，近期工作要么引入辅助模型（如过程奖励模型 PRM、价值模型）、要么增加采样、要么依赖特权信息（如 ground-truth 中间步骤）。这些方法普遍面临成本高、可扩展性差的问题。此外，一些工作利用策略熵作为信号，但往往在"强化"与"惩罚"两个方向都优先处理不确定位置——作者指出这会带来副作用：**失败响应中"仍可恢复"的不确定位置被重点惩罚，反而压制了后续探索空间**。

### 研究动机
作者观察到两个关键规律：
1. **意外成功（surprising success）**：高熵（不确定）状态下仍成功的 token，其成功难以复现，是宝贵的"探索红利"，应被强力强化。
2. **重复失败（repeated failure）**：低熵（自信）状态下仍失败的 token，其失败倾向复发，应被强力纠正。

据此，成功与失败应当被**非对称地**处理，而策略熵正好提供了现成可用的区分信号。

## 研究问题

如何在 RLVR 框架下，**无需辅助模型或额外监督**，仅利用现有 rollout 信号（策略熵与响应优势），实现有效的 token 级信用分配，从而在提升推理性能的同时增强探索？

## 方法概述

### 核心思想
EAPO 的关键是把"策略熵"与"响应优势的符号"耦合，做**成功/失败非对称**的 token 级信用分配：强化"不确定下的成功"，纠正"自信下的失败"，并在不确定位置衰减惩罚以保护探索。

### 方法框架

#### 整体架构
EAPO 在标准 RLVR 流程上改造了信用分配环节，无需新增网络或采样：

```
rollout 响应 → 结果奖励 r → 响应级优势 A(success/failure 符号)
     +
策略熵 H(π|token)（归一化）
     ↓
非对称 token 级信用：
  · 成功响应：高熵 token 强化 ↑（惊喜成功）
  · 失败响应：低熵 token 惩罚 ↑（重复失败）
  · 不确定位置：惩罚衰减 ↓（保留恢复机会）
     ↓
策略梯度更新
```

![[concept_page1.png|600]]

> 图1：EAPO 的核心概念示意，展示如何根据"策略熵 × 优势符号"非对称地分配 token 级信用。

#### 关键机制说明

**机制1：成功/失败非对称处理**
- 成功（优势为正）：把响应优势按归一化熵加权分配到各 token，**高熵 token 获得更强强化**——奖励"意外成功"。
- 失败（优势为负）：同样按熵加权分配惩罚，但**低熵 token 获得更强惩罚**——纠正"重复失败"。

**机制2：不确定位置惩罚衰减**
- 对失败响应中高熵（不确定）的 token，衰减其惩罚，因为这些位置"仍有恢复的替代方案"，过度惩罚会关闭探索通道。

**机制3：零额外监督**
- EAPO 完全复用 rollout 中已有的策略熵与响应优势，不引入 PRM/价值模型、不加采样、不依赖特权信息。

### 方法架构图

![[exploration_page1.png|600]]

> 图2：EAPO 对探索行为的影响示意，展示其如何扩大问题覆盖、生成更多样化的候选答案。

## 实验结果

### 实验目标
验证 EAPO 在推理任务上的整体性能，以及其对探索行为的促进作用。

### 数据集与骨干
- **任务**：覆盖多种数学/推理基准（paper 在 base 与 reasoning 两类骨干上验证）。
- **骨干**：同时测试 base 模型与 reasoning（已强化）模型，证明方法的通用性。

### 主要结果
- **整体性能**：EAPO 在多个推理任务上取得**最佳整体性能**，优于基线 RLVR 方法。
- **探索效果**：EAPO 促进更有效的探索，**扩大问题覆盖**、生成**更多样化的候选答案**。

### 实验结果图

![[training_dynamics_page1.png|600]]

> 图3：训练动态对比，展示 EAPO 与基线的性能随训练的变化。

![[word_entropy_correct_page1.png|600]]

> 图4：正确响应中的词级熵分布，说明高熵（不确定）决策在成功中的角色。

![[word_entropy_incorrect_page1.png|600]]

> 图5：错误响应中的词级熵分布，支撑"自信失败倾向复发"的观察。

## 深度分析

### 研究价值评估

#### 理论贡献
- **信用分配新范式**：提出"熵 × 优势符号"的非对称信用分配，把直觉（意外成功 vs 重复失败）形式化为可直接优化的目标。
- **零成本细粒度信号**：证明 token 级信用可从现有 rollout 信号免费导出，无需 PRM/价值模型等额外组件。
- **探索理论视角**：把探索问题与信用分配统一起来，指出"不确定性下的成功是探索红利"这一洞见。

#### 实际应用价值
- **RLVR 训练改进**：可作为 RLVR 的即插即用信用分配替代方案，降低细粒度监督成本。
- **通用性**：在 base 与 reasoning 骨干上都有效，适用范围广。

### 方法优势详解
- **简单**：不需要新网络、新采样或特权信息，仅改动信用分配公式。
- **有效**：整体性能最优，且显式促进探索。
- **可解释**：非对称处理背后有清晰的直觉支撑（surprising success / repeated failure）。

### 局限性分析
- **依赖熵质量**：策略熵作为不确定性的近似，其归一化与估计方式会影响效果，对熵估计的稳定性有一定要求。
- **任务范围**：主要验证于推理类任务（数学/代码），在更开放生成任务上的效果待验证。
- **超参数敏感**：非对称衰减的强度（惩罚衰减幅度）需要调节。

## 技术路线定位

### 所属技术路线
本文属于 **RLVR / 强化学习微调** 与 **信用分配（credit assignment）** 交叉方向，核心特点是：用熵信号做无监督的细粒度信用分配。

### 本文在技术路线中的位置
- **承上**：继承 GRPO/RLOO 等 RLVR 的结果级反馈框架，以及"熵作为探索/不确定性信号"的思路。
- **启下**：为"零额外监督的 token 级信用分配"提供了可扩展的新基线。

## 我的综合评价

### 价值评分

#### 总体评分
**8.8/10** — 思路简洁、直觉清晰、无需额外监督即取得整体最优，是 RLVR 信用分配方向的高质量工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "熵×优势符号"非对称分配是清晰的新组合 |
| 技术质量 | 9/10 | 公式化严谨，直接复用现有信号 |
| 实验充分性 | 8/10 | 覆盖 base/reasoning 两类骨干与多任务 |
| 写作质量 | 9/10 | 摘要与动机表述清晰 |
| 实用性 | 9/10 | 即插即用，零额外成本 |

### 重点关注
- **非对称信用分配的公式细节**：如何把响应优势按归一化熵加权分配。
- **惩罚衰减的幅度与影响**：不确定位置衰减多少是最优的。

## 相关论文

### 直接相关
- [[20_Research/Papers/大语言模型/Do_We_Really_Need_KL_Divergence_for_On-Policy_Distillation_of_Large_Language_Models|Do We Really Need KL Divergence for On-Policy Distillation]] - 同为 RLVR/策略优化方向的近邻工作
- [[20_Research/Papers/大语言模型/Diffusion_Reward_Models|Diffusion Reward Models]] - 同为奖励建模/对齐方向

### 背景相关
- [[20_Research/Papers/大语言模型/On_the_Token_Value_Inequality_in_Efficient_Reasoning|On the Token Value Inequality in Efficient Reasoning]] - 同样关注 token 级价值的非均匀性

## 外部资源
- 项目页：https://eapo-explore.github.io
- 代码：https://github.com/wgcyeo/EAPO

> [!tip] 关键启示
> 失败不等于没有监督信号——即便是失败的轨迹也蕴含着"哪个 token 该被纠正"的信息；用"策略熵 × 优势符号"就能零成本地把它挖掘出来。

> [!success] 推荐指数
> ⭐⭐⭐⭐☆ 强烈推荐：RLVR 与信用分配方向的读者必读，方法论简洁且可迁移。
