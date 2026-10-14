---
date: "2026-10-14"
paper_id: "arXiv:2610.17426"
title: "Reinforcement Learning In Partially Verifiable Environments for Colon Cancer Treatment Planning"
authors: "Li-Ching Chen, Andrés Franco Valiente, Yuta Ishiyama, Zachary R. Chalmers, William Chen, Kelsey Natsuhara, Taema Brinjikji, Madhumita Sushil, Travis Zack, Julian C. Hong"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 医学AI
  - 临床决策
  - RLHF
  - 部分可观测
  - 肿瘤治疗规划
quality_score: "8.0/10"
related_papers: []
created: "2026-10-14"
updated: "2026-10-14"
status: analyzed
---

# Reinforcement Learning In Partially Verifiable Environments for Colon Cancer Treatment Planning

## 核心信息
- **论文ID**：arXiv:2610.17426
- **作者**：Li-Ching Chen、Andrés Franco Valiente、Yuta Ishiyama、Zachary R. Chalmers、William Chen、Kelsey Natsuhara、Taema Brinjikji、Madhumita Sushil、Travis Zack、Julian C. Hong
- **机构**：University of California, San Francisco (UCSF)、Vokram Inc.
- **发布时间**：2026-10-12
- **会议/期刊**：ICLR 2027 投稿（cs.LG / cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2610.17426) | [PDF](https://arxiv.org/pdf/2610.17426)
- **引用**：--

## 摘要翻译

### 英文摘要
Clinical decision-making is a sequential process of information collection, clinical inference, and forecasting under partial observability, often without a uniquely correct treatment decision. Large language models (LLMs) have demonstrated strong medical question-answering capabilities but extending these advances to clinical reasoning remains challenging because many clinical tasks are only partially verifiable. Recent clinical RL work has therefore largely focused on diagnostic endpoints, where correctness can be more directly verified. We formulate oncology treatment planning as an interactive RL problem under partial observability and construct patient-specific, process-aware rewards from decision processes observed in real-world clinical records. The agent must learn to sequentially acquire information from a structured patient representation before committing to a first-line treatment recommendation, and is evaluated on information gathering, treatment selection, and reasoning alignment. Using GRPO and GDPO to optimize Llama-3.1-8B-Instruct within this environment, RL substantially improves the base policy beyond frontier model capabilities. The GDPO-trained policy increases Mean@3 scalar clinical reward by 79.60% over base Llama and exceeds GPT-5.2 by 18.82% under the same interactive environment, while reducing query count during training.

### 中文翻译
临床决策是在部分可观测条件下的一个信息采集、临床推断与预测的序贯过程，且往往没有唯一正确的治疗决策。大语言模型（LLM）已展现出强大的医学问答能力，但将这些能力扩展到临床推理仍具挑战，因为许多临床任务仅部分可验证。因此，近期临床强化学习（RL）工作多聚焦于诊断终点——其正确性更易直接验证。作者将肿瘤治疗规划建模为部分可观测下的交互式 RL 问题，并从真实世界临床记录中观察到的决策过程构造「患者特异、过程感知」的奖励。智能体必须在提交一线治疗推荐之前，学会从结构化的患者表征中序贯地采集信息，并在信息采集、治疗选择、推理对齐三个维度上接受评估。作者用 GRPO 和 GDPO 在该环境中优化 Llama-3.1-8B-Instruct，RL 显著提升了基础策略、超越了前沿模型能力。GDPO 训练的策略在 Mean@3 标量临床奖励上比基础 Llama 提升 **79.60%**，在同一交互环境下超越 GPT-5.2 **18.82%**，同时降低了训练期间的查询次数。

### 核心要点提炼
- **研究背景**：LLM 医学能力强，但临床推理（尤其治疗规划）多为「部分可验证」，难以直接对齐。
- **研究动机**：把临床治疗规划建模为部分可观测下的交互式 RL 问题，用真实决策过程构造过程感知奖励。
- **核心方法**：结构化患者表征 + 序贯信息采集 + GRPO/GDPO 优化 Llama-3.1-8B-Instruct。
- **主要结果**：GDPO 策略 Mean@3 临床奖励较基座 +79.60%，超 GPT-5.2 +18.82%。
- **研究意义**：证明「患者特异、部分可验证」的奖励能为交互式临床 RL 提供有效学习信号。

## 研究背景与动机

### 领域现状
- LLM 在医学问答（medical QA）上表现强劲，但这些多为「单轮、闭式、信息已给出」的任务。
- 临床推理（clinical reasoning）要求多轮、开放式、部分可观测下的信息采集与推断。

### 现有方法的局限性
- 近期临床 RL 工作多聚焦**诊断终点**（diagnostic endpoints），因为其正确性可直接验证；而**治疗规划**的正确性往往无法唯一判定，难以用简单奖励对齐。
- 多个治疗轨迹对同一部分观测的患者状态都可能「临床可辩护」，单一正确答案假设不成立。

### 研究动机
作者提出：部分可观测下的治疗规划是 RL 的天然场景，但需要「过程感知」的奖励——不只奖励最终推荐，也奖励「是否正确采集并使用了必要信息」。

## 研究问题

### 核心研究问题
如何用部分可观测的交互式 RL 训练一个临床智能体，使其能在提交一线治疗推荐前，序贯采集必要信息，并做出与临床医生推理对齐的决策？

## 方法概述

### 核心思想
把肿瘤治疗规划形式化为**部分可观测下的交互式 RL**，并用从真实电子病历（EHR）中提取的临床决策过程构造「患者特异、过程感知」的奖励信号，指导智能体在结构化患者表征上序贯采集信息。

### 方法框架

#### 整体架构
1. **数据集**：950 例结直肠癌患者（UCSF 去标识化 EHR），提取一线治疗计划及其临床推理依据（oncologist 的信息采集过程）。
2. **环境**：将纵向患者轨迹重构为「现病史 + 肿瘤画像 + 生理数据」的结构化表征；智能体须先采集信息、再提交治疗推荐。
3. **奖励**：过程感知奖励模型，从真实临床决策过程监督而来。
4. **训练**：用 GRPO 和 GDPO 优化 Llama-3.1-8B-Instruct。

![[2610.17426_fig1.png|600]]

> 图1：部分可观测环境下的临床 RL 训练框架示意（论文 Fig.1，展示从 EHR 到结构化表征、信息采集与治疗推荐的流程）

### 关键创新

1. **部分可观测 + 治疗规划（而非诊断）** - 首次把「无唯一正确答案」的治疗规划建模为交互式 RL 问题。
2. **过程感知、患者特异的奖励构造** - 从真实临床决策过程提取奖励，而非仅依赖最终正确性。
3. **GRPO/GDPO 有效超越前沿模型** - 小模型（8B）经 RL 训练后在同一交互环境下超过 GPT-5.2。

## 实验结果

### 数据集
- **950 例成人结直肠癌患者**（UCSF EHR），按患者划分训练/验证/测试（665/143/142）。

### 主要结果
- **GDPO 训练策略**：Mean@3 标量临床奖励较基座 Llama 提升 **+79.60%**，超 GPT-5.2 **+18.82%**（同一交互环境）。
- **训练效率**：同时降低了训练期间的查询次数。
- **评估维度**：信息采集、治疗选择、推理对齐三方面均提升。

## 深度分析

### 研究价值
- **理论贡献**：把「部分可验证性」从诊断扩展到治疗规划，拓展了临床 RL 的问题边界。
- **实际应用**：为肿瘤治疗决策支持系统提供了可训练、可对齐的范式。
- **领域影响**：展示「小模型 + 过程感知 RL」在专业临床任务上超越通用前沿模型的路径。

### 优势
- 问题设定真实、贴近临床实际（治疗规划无唯一答案）。
- 奖励构造有真实临床依据，方法严谨。
- 8B 模型超越 GPT-5.2，实证价值高。

### 局限性
- 单一癌种（结直肠癌）、单一机构（UCSF）数据，泛化性待验证。
- 奖励模型依赖 GPT-5.2 判官，存在评估偏置风险。
- 临床落地仍需前瞻性验证与监管考量。

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 把临床治疗规划严谨地建模为部分可观测 RL，并以过程感知奖励实现有效对齐，是临床 LLM 从「诊断」走向「治疗决策」的重要一步。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 部分可验证 + 治疗规划的组合新颖 |
| 技术质量 | 8/10 | 奖励构造与 RL 方法严谨 |
| 实验充分性 | 7/10 | 单一癌种/机构，但指标多维 |
| 写作质量 | 8/10 | 清晰、充分 |
| 实用性 | 8/10 | 临床决策支持潜力大 |

### 重点关注
- **值得关注的技术点**：过程感知奖励如何从 EHR 决策过程提取。
- **需要深入理解的部分**：GDPO 与 GRPO 在部分可观测场景下的差异。

## 相关论文
- 临床 RL（诊断终点）；GRPO/GDPO；医学 LLM 推理。

## 外部资源
- arXiv: https://arxiv.org/abs/2610.17426

> [!tip] 关键启示
> 治疗规划的正确性「部分可验证」，用「过程感知、患者特异」的奖励对齐，能让小模型在专业临床任务上超越前沿通用模型。

> [!warning] 注意事项
> - 单一癌种、单一机构，泛化性待验证
> - 奖励判官依赖 GPT-5.2，存在评估偏置
> - 临床落地需前瞻性验证与合规审查

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐——临床 LLM 从诊断走向治疗决策的代表性 RL 工作。
