---
date: "2026-10-12"
paper_id: "arXiv:2610.13672"
title: "Inject a Turn, Not a Token: Learning Where to Redirect LLM Reasoning for Self-Correction"
authors: "Jiwon Jeon, Myungsik Cho, Jeonghye Kim, Youngchul Sung"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Self-Correction
  - Reasoning
  - Test-Time-Computation
  - LLM
quality_score: "8.3/10"
created: "2026-10-12"
updated: "2026-10-12"
status: analyzed
---

# Inject a Turn, Not a Token: Learning Where to Redirect LLM Reasoning for Self-Correction

## 核心信息
- **论文ID**：arXiv:2610.13672
- **作者**：Jiwon Jeon, Myungsik Cho, Jeonghye Kim, Youngchul Sung
- **机构**：Korea Advanced Institute of Science and Technology (KAIST)
- **发布时间**：2026-10-09
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2610.13672) | [PDF](https://arxiv.org/pdf/2610.13672)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models struggle to reliably identify and correct errors in their own reasoning. Unlike prior training-based approaches that fine-tune an LLM to generate revisions or feedback, we learn where to intervene in failed rollouts and determine how to redirect them. To choose where to intervene, we train a 130K-parameter MLP head on a frozen encoder using only final-answer correctness. For redirection, we find that under a fixed generation budget, the tested inline injections fail to reliably redirect reasoning, whereas presenting the same instruction in a new user turn changes the model's course. The resulting correction framework, UPTURN (User-Prompt TURN injection at a learned position), requires no LLM fine-tuning: it retains a selected prefix, injects an instruction as a new user turn, and regenerates the suffix. Across four reasoners and six math benchmarks, UPTURN improves average accuracy in every setting over a 32-rollout baseline. It outperforms self-correction and reflection-token injection baselines under matched correction budgets, transfers across reasoners and to GPQA-Diamond without retraining, and complements GRPO and self-correction methods.

### 中文翻译
大语言模型难以可靠地识别并纠正自身推理中的错误。与先前基于训练、通过微调 LLM 生成修订或反馈的方法不同，我们**学习在失败 rollout 中"在哪里干预"以及"如何重定向"**。为选择干预位置，我们在冻结编码器上训练一个 **130K 参数 MLP 头**，仅使用最终答案的正确性作为监督信号。在重定向方面，我们发现在固定生成预算下，所测试的"行内注入"（inline injection）无法可靠地改变推理方向，而**将相同指令作为新的用户轮次（user turn）呈现**则能改变模型的轨迹。由此得到的纠错框架 **UPTURN**（User-Prompt TURN injection at a learned position，在学到的位置注入用户提示轮）**无需对 LLM 做任何微调**：它保留选定的前缀、将指令作为新的用户轮注入、再重新生成后缀。在四个推理器和六个数学基准上，UPTURN 在每个设定中都相对 32-rollout 基线提升了平均准确率；在匹配的纠错预算下超越了自纠错（self-correction）与反射 token 注入基线；无需重新训练即可跨推理器迁移并推广到 GPQA-Diamond；且能与 GRPO 及自纠错方法互补。

### 核心要点提炼
- **研究背景**：LLM 推理出错后难以自我识别并纠正，自纠错能力不可靠。
- **研究动机**：已有方法靠微调 LLM 生成修订/反馈，成本高；需要一种无需微调的测试时纠错方案。
- **核心方法**：用 130K MLP 头（冻结编码器 + 最终答案正确性监督）学习"干预位置"，并在该位置以"新用户轮"注入重定向指令，再重新生成后缀。
- **主要结果**：4 推理器 × 6 数学基准全部提升平均准确率；超越 self-correction 与 reflection-token 基线；零微调迁移到 GPQA-Diamond；与 GRPO 互补。
- **研究意义**：揭示"新用户轮"优于"行内 token 注入"的关键机制，提供一种极低成本的测试时推理纠错范式。

## 研究背景与动机

### 领域现状
大语言模型在数学推理等任务上已表现出色，但"自我纠错"（self-correction）仍是一个公认难题：模型往往难以定位自己的错误，甚至会在"纠错"时把正确答案改错。现有提升推理正确性的路线主要有：训练侧（RL/GRPO、微调生成反馈）与测试时（多次采样投票、self-consistency、反思 token）。

### 现有方法的局限性
- **训练侧自纠错**：需要微调 LLM 生成修订或反馈，成本高、且对黑盒/API 模型不友好。
- **反思 token / inline 注入**：在推理过程中插入特殊 token 或提示，但论文发现这类"行内注入"在固定预算下难以可靠改变推理方向。
- **纯多次采样投票**：浪费预算，未针对性地修正已失败轨迹。

### 研究动机
把"纠错"拆成两个正交问题——**在哪里干预**（定位错误）与**如何重定向**（改变轨迹）——并分别用轻量学习头与"新用户轮注入"来解决，从而在无需微调 LLM 的前提下实现可靠的测试时纠错。

## 研究问题

### 核心研究问题
在固定生成预算下，如何以极低成本（无需微调 LLM）可靠地定位失败推理并重定向其轨迹，从而提升推理正确率？

## 方法概述

### 核心思想
UPTURN 把纠错建模为"位置选择 + 轮次注入"：一个 130K 参数的 MLP 头在冻结编码器的表示上、仅凭"最终答案对错"这一弱信号学习出应在推理链的哪个位置介入；介入时不是插入 token，而是把重定向指令作为一个全新的用户轮（user turn）注入，保留前缀、重新生成后缀。

### 方法框架

#### 整体架构
UPTURN 分三步：① 用冻结编码器表示推理前缀，MLP 头预测干预位置；② 在该位置把"重定向指令"作为新用户轮注入；③ 重新生成后缀完成推理。整个过程不更新 LLM 权重。

![[2610.13672_fig1.png|600]]

> 图1：UPTURN 概览——学习干预位置，并以"新用户轮"注入重定向指令后重新生成后缀。

#### 关键组件说明

**组件1：干预位置学习（130K MLP 头）**
- 编码器冻结，只训练一个轻量 MLP 头；监督信号仅为最终答案的正确性（无需 token 级标注）。
- 该头在推理链的各 token 表示上打分，选出最需要介入的位置。

**组件2：新用户轮注入（而非 token 注入）**
- 关键发现：在固定预算下，把指令作为"行内 token"注入无法可靠改变推理；而作为"新用户轮"注入能显著改变模型轨迹。
- 原因在于新轮次改变了模型的生成条件/角色设定，产生更强的转向信号。

**组件3：前缀保留 + 后缀重生成**
- 保留已正确生成的前缀，只重定向后缀，既避免浪费已有正确部分，又针对性地修正错误段。

## 实验结果

### 实验目标
验证 UPTURN 在数学推理上的纠错效果、与基线的对比、跨推理器迁移能力及与 RL 方法的互补性。

### 数据集与基线
- **推理器**：4 个（不同规模的 LLM）。
- **基准**：6 个数学推理基准（含 GPQA-Diamond 作分布外迁移）。
- **对比基线**：32-rollout 基线、self-correction、reflection-token 注入；另考察与 GRPO、self-correction 的组合。

### 主要结果
- **主结果**：4 推理器 × 6 基准的**每个设定**中，平均准确率均优于 32-rollout 基线。
- **对比**：在匹配纠错预算下，超越 self-correction 与 reflection-token 注入基线。
- **迁移**：无需重新训练即可跨推理器迁移，并推广到 GPQA-Diamond。
- **互补性**：与 GRPO、self-correction 方法叠加仍能带来增益。

### 结果分析
核心发现是"新用户轮"与"行内 token 注入"的机制差异——前者提供了更强的重定向信号。这一发现既解释了为何许多"反思 token"方法效果有限，也指出了一条更廉价的测试时纠错路径：只需一个 130K 参数的轻量头 + 一次用户轮注入，即可在不碰 LLM 权重的前提下稳定提升推理正确率。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：将"自我纠错"解耦为"在哪干预"（位置学习）与"如何重定向"（轮次注入）两个问题，并给出针对性解法。
  - 创新点：仅用"最终答案对错"这一弱信号训练轻量头定位错误，无需 token 级监督。
- **贡献2**：实证揭示"新用户轮注入"优于"行内 token 注入"的机制，对测试时计算研究有方法论启示。
  - 创新点：用受控实验（固定预算）验证两种注入方式的可靠性差异。

#### 实际应用价值
- **应用场景1**：黑盒/API LLM 的推理纠错——无需微调权重即可提升数学推理正确率。
- **应用场景2**：与 RL（GRPO）后训练方法叠加，作为正交的测试时增益。

### 局限性
- 干预位置学习依赖冻结编码器，对只有 API、拿不到隐藏表示的模型不直接适用。
- 主要在数学推理上验证，代码、常识等更开放任务的迁移性未充分探索。
- 相对多次采样会引入额外推理开销，预算分配（何时介入 vs 继续采样）仍需权衡。

### 未来工作
- 探索无需隐藏表示的纯黑盒干预位置估计方法。
- 扩展到代码、agent 等多步决策任务。
- 研究介入次数、时机与预算之间的最优策略。

### 与相关工作对比
- **vs 训练侧 self-correction / STaR / RISE 等**：这些需要微调 LLM；UPTURN 完全不更新权重，更轻量、更易部署。
- **vs Reflection token / 特殊 token 注入**：论文证明行内 token 注入在固定预算下不可靠，而 UPTURN 的"新用户轮"注入更有效。
- **研究路线归属**：属于"测试时计算（test-time computation）"与"LLM 自纠错"的交叉，强调无微调、可迁移。
