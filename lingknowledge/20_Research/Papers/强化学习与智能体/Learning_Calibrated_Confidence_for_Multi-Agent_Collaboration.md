---
date: "2026-10-30"
paper_id: "arXiv:2610.39927"
title: "Learning Calibrated Confidence for Multi-Agent Collaboration"
authors: "Chenxiao Fan, Chongming Gao, Gangyi Zhang, Yaxin Gong, Leyang Shen, Jiakai Wang, Dong Wang, Yang Liu, Haoyan Liu, Xinting Hu, Fuli Feng, Xiangnan He"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 多智能体系统
  - 置信度校准
  - 强化学习
  - LLM-Agent
  - 多跳推理
quality_score: "7.5/10"
created: "2026-10-30"
updated: "2026-10-30"
status: analyzed
---

# Learning Calibrated Confidence for Multi-Agent Collaboration

## 核心信息
- **论文ID**：arXiv:2610.39927
- **作者**：Chenxiao Fan, Chongming Gao, Gangyi Zhang, Yaxin Gong, Leyang Shen, Jiakai Wang, Dong Wang, Yang Liu, Haoyan Liu, Xinting Hu, Fuli Feng, Xiangnan He
- **机构**：中国科学技术大学、阿里巴巴 Qwen 事业部、新加坡国立大学
- **发布时间**：2026-10-29
- **类别**：cs.LG, cs.AI, cs.MA
- **链接**：[arXiv](https://arxiv.org/abs/2610.39927) | [PDF](https://arxiv.org/pdf/2610.39927)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Complex information seeking on the Web often requires collecting evidence from multiple documents and reasoning across them, as in multi-hop question answering. Multi-agent systems address such tasks by dividing work among specialized agents, where upstream agents produce intermediate answers and supporting evidence that downstream agents build upon. However, upstream answers vary in correctness, and most systems provide no quantitative signal about it, leaving the downstream agent to judge whether to trust or verify what it receives based solely on the textual evidence. In this paper, we propose MASCAL. Each agent learns to produce a correctness probability for its answer by jointly optimizing task performance and confidence calibration in reinforcement learning; the upstream agent passes this probability at handoff, giving the downstream agent a quantitative basis for its decisions, and the final answer also carries a calibrated probability, quantifying the reliability of the system output. To guide the downstream agent in using this signal during training, we introduce confidence-aware weighting, which increases the learning weight at positions responsive to the upstream probability, so that the agent learns more from decisions where it actively uses the signal. Across three collaboration architectures spanning Web retrieval and reasoning tasks, MASCAL improves task accuracy and calibration quality for both agents, and generalizes to deeper collaboration chains and different model scales.

### 中文翻译
复杂信息检索往往需要从多个文档收集证据并跨文档推理（如多跳问答）。多智能体系统通过角色分工来应对这类任务：上游智能体产出中间答案与支持证据，下游智能体在此基础上继续处理。然而，上游答案的正确性参差不齐，且大多数系统并不提供关于正确性的量化信号，导致下游智能体只能依据文本证据来判断是信任还是核实所接收的内容。本文提出 MASCAL：每个智能体在强化学习中联合优化任务性能与置信度校准，从而学会为自身答案产出一个正确性概率；上游智能体在交接（handoff）时传递这一概率，为下游智能体的决策提供量化依据；最终答案同样携带校准后的概率，用于量化系统输出的可靠性。为引导下游智能体在训练中利用这一信号，作者提出 confidence-aware weighting，提高对"对上游概率敏感"位置的学权重，使智能体从真正使用该信号的决策中学到更多。在三种协作架构（覆盖 Web 检索与推理任务）上，MASCAL 同时提升了任务准确率与置信度校准质量，并泛化到更深的协作链与不同模型规模。

### 核心要点提炼
- **研究背景**：多智能体系统中上游智能体的中间答案质量参差，但缺少量化的置信度信号，下游只能盲猜该信任还是核实
- **核心方法**：MASCAL——在 RL 中联合优化任务奖励与置信度校准奖励，让每个智能体学会输出校准后的正确性概率；再用 confidence-aware weighting 引导下游智能体利用上游置信度
- **关键目标**：把"校准置信度的学习—传递—利用"贯穿整个协作流水线
- **主要结果**：在三种协作架构上准确率全面最高，置信度校准质量提升；最终置信度支持选择性回答与定向复查

## 研究背景与动机

### 领域现状
LLM 驱动的多智能体系统（MAS）被广泛用于需要多步推理与证据收集的 Web 任务（多跳问答、跨文档事实验证等）。典型流水线中，上游智能体产出中间答案与支持证据，并把报告交接给下游智能体继续处理。

### 现有方法的局限性
1. **缺少量化置信度信号**：大多数 MAS 只传递答案和文本证据，下游智能体无法区分可靠与不可靠的报告，只能基于文本自行判断
2. **置信度学习与利用割裂**：现有工作要么在辩论/讨论中表达置信度，要么做跨角色不确定性传播，但没有人把"置信度的学习、传递、利用"完整贯通到协作流水线中
3. **下游不会用信号**：即便给出置信度，下游智能体也没有被训练如何据此决策

### 研究动机
作者提出：能否让每个智能体在学任务的同时学会表达"校准后"的答案置信度，并让下游智能体学会依据这一信号做决策？这需要把校准目标嵌入 RL 训练，并显式引导下游对信号的利用。

## 研究问题

### 核心研究问题
如何在多智能体协作中，让上游智能体学习产出**校准后的正确性概率**，并将其传递、让下游智能体学会利用，从而同时提升任务准确率与系统输出的可靠性评估能力？

具体分解为三个子问题：
1. 每个智能体能否在学任务的同时学会表达校准后的置信度？
2. 下游智能体能否学会依据上游置信度信号做决策？
3. 最终答案的置信度能否支撑选择性回答与定向复查？

## 方法概述

### 核心思想
把置信度校准直接嵌入强化学习的目标中，让"任务做得对"与"对答案有多确定"两个目标在同一训练循环里联合优化；同时通过 confidence-aware weighting 放大下游对上游置信度信号敏感的位置，从而让下游学会"用"这个信号。

### 方法框架

![[fig1_handoff_page1.png|800]]

> 图1：智能体交接处的置信度。没有置信度信号时，下游无法区分可靠与不可靠报告；MASCAL 为每份报告和最终答案附加校准后的置信度，引导下游决策并支持系统输出的可靠性评估。

#### 各模块详细说明

**模块1：置信度学习（上游智能体）**
- **功能**：让上游智能体在为子任务产出答案的同时，输出一个校准后的正确性概率
- **训练方式**：在 RL 中引入校准目标，任务奖励与置信度校准联合优化
- **关键机制**：任务策略梯度（task reward 引导推理与工具调用）+ 置信度策略梯度（calibration reward 衡量输出概率与真实正确率的一致程度）

**模块2：置信度传递（handoff）**
- **功能**：上游在交接时把正确性概率随中间结论一起传给下游
- **效果**：下游获得量化依据，可判断"采纳"还是"核实"

**模块3：confidence-aware weighting（下游训练）**
- **功能**：度量每个生成位置对上游概率的响应程度，提高最敏感位置的学权重
- **效果**：下游智能体从"真正使用上游信号"的决策中学到更多

**模块4：最终答案校准**
- **功能**：下游同样输出校准后的最终概率
- **用途**：支持选择性回答（withhold 不确定输出）与定向复查（direct rechecking）

### 协作架构
MASCAL 在三种架构下验证：
- **Sequential handoff（顺序交接）**：planner 分解问题，上游检索并回答子问题后交接，下游继续检索并回答原问题
- **Report fusion（报告融合）**：两个上游独立回答同一子问题并各交一份报告，下游综合两份
- **Answer review（答案审查）**：上游回答完整问题并给出候选答案，下游审查并保留或修正

## 实验结果

### 实验设置
- **模型**：所有智能体基于 Qwen3-8B 初始化
- **数据集**：多跳 QA（MuSiQue、HotpotQA、2Wiki，用于 sequential/fusion）、逻辑推理（LogiQA2、ReClor、AR-LSAT，用于 answer review）、数学推理（MATH500、AMC&AIME、DeepScaleR）
- **指标**：任务准确率（Acc.）、置信度质量（Brier score、AUROC）
- **基线**：单智能体（base + Task-RL）、无置信度 MAS（base + Task-RL + MAGRPO + MHGPO）、置信度类 MAS（ConfMAD、DMAD）

### 主要结果

| 架构 | Task-RL MAS | MAGRPO | ConfMAD | DMAD | **MASCAL** |
|------|------------|--------|---------|------|-----------|
| Sequential handoff（avg） | 47.49 | 48.87 | 46.78 | 49.79 | **59.00** |
| Report fusion（avg） | 54.78 | 56.30 | 48.35 | 50.09 | **61.45** |
| Answer review-逻辑（avg） | 73.85 | 68.75 | 63.38 | 69.50 | **76.59** |
| Answer review-数学（avg） | 72.91 | 70.39 | 52.37 | 65.30 | **78.69** |

> MASCAL 在四种评估场景下准确率全面最高；在 report fusion 上相较最强基线 Task-RL MAS（54.78）提升 +6.67 个百分点。

### 关键发现
- **RQ1（准确率）**：三种架构下 MASCAL 均最优，置信度传输带来稳定提升
- **RQ2（上游置信度）**：上游置信度校准良好，干预分析显示上游置信度显著影响下游决策
- **RQ3（选择性回答）**：最终置信度支撑 coverage-accuracy 控制，选择性回答大幅降低已交付输出的错误率，定向复查将算力导向最需要处
- **RQ4（消融）**：confidence-aware weighting 与校准目标各有独立贡献
- **RQ5（泛化）**：两跳架构训练可直接迁移到更深的 Web 检索链，小规模模型同样有效

## 深度分析

### 研究价值评估

#### 理论贡献
- 首次把"置信度学习—传递—利用"完整贯通到多智能体协作流水线，填补了 MAS 中置信度通信的空白
- 提出 confidence-aware weighting，从"响应敏感度"角度解决下游如何利用上游信号的问题

#### 实际应用价值
- 面向真实 Web 检索/推理类 MAS，可直接提升协作准确率与系统可靠性
- 最终置信度支持选择性回答（低置信不交付）与定向复查（算力按需分配），对成本敏感的 agent 部署很有价值

### 局限性
- 基于 Qwen3-8B 单一基座，更大/闭源模型的适用性待验证
- 置信度校准与任务 RL 的耦合增加了训练复杂度，超参（校准损失权重 α、weighting 强度 λ）需要按数据集调整
- 上游需先冻结再训下游，训练流水线较长

## 与相关论文对比

### [[MAGRPO]] / [[MHGPO]]（协作 MARL）
- **关系**：MASCAL 的基线之一
- **差异**：二者通过协作 MARL / 终端奖励传播优化多角色性能，但都不传输置信度信号；MASCAL 增加校准置信度通信这一维度

### ConfMAD / DMAD（置信度类 MAS）
- **关系**：同为"置信度用于 MAS"路线
- **差异**：ConfMAD 用序列级概率 + 事后校准器，DMAD 用正确性奖励训练口头化置信度表达；MASCAL 直接在 RL 中联合学习校准置信度并用 weighting 引导利用，更端到端

## 技术路线定位

本文属于**多智能体协作 + 置信度校准**路线：从"角色分工 + 终端奖励"的 MAS 优化，走向"在协作中显式传递并利用校准置信度"的新范式。承上继承了 MARL/MAS 优化，启下为 MAS 的可信协作（可靠性评估、选择性交付）提供基础。

## 我的综合评价

### 价值评分

#### 总体评分
**7.5/10** - 问题定位清晰、机制完整、实验扎实；置信度通信是 MAS 可信协作的自然方向，具有实用价值。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 置信度用于 MAS 非全新，但"学习—传递—利用"贯通 + weighting 机制是清晰增量 |
| 技术质量 | 8/10 | RL 联合校准目标 + 三架构验证，方法严谨 |
| 实验充分性 | 8/10 | 四场景、多基线、消融、干预分析、泛化实验全面 |
| 写作质量 | 7/10 | 结构清晰、动机充分 |
| 实用性 | 8/10 | 直接适用于 Web 检索/推理类 agent 系统 |

## 相关论文
- [[MAGRPO]] - 协作 MARL 基线
- [[MHGPO]] - 终端奖励传播基线
- [[ConfMAD]] - 序列级概率置信度
- [[DMAD]] - 口头化置信度表达

> [!tip] 关键启示
> 让智能体在学任务的同时学会"表达对自己答案有多确定"，并把这一校准信号贯穿协作流水线，是提升 MAS 可信度的关键一步。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读——多智能体可信协作与置信度校准方向的清晰范例。
