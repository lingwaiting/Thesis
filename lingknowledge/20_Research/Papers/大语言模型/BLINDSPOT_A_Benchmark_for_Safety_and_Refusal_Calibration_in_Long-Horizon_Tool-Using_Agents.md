---
date: "2026-09-16"
paper_id: "arXiv:2609.16305"
title: "BLINDSPOT: A Benchmark for Safety and Refusal Calibration in Long-Horizon Tool-Using Agents"
authors: "Sadia Asif, Mohammad Mohammadi Amiri, Momin Abbas, Tejaswini Pedapati, Prasanna Sattigeri"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM智能体
  - 安全评测
  - 工具调用
  - 拒绝校准
  - 长程交互
quality_score: "8.3/10"
created: "2026-09-16"
updated: "2026-09-16"
status: analyzed
---

# BLINDSPOT: A Benchmark for Safety and Refusal Calibration in Long-Horizon Tool-Using Agents

## 核心信息
- **论文ID**：arXiv:2609.16305
- **作者**：Sadia Asif, Mohammad Mohammadi Amiri, Momin Abbas, Tejaswini Pedapati, Prasanna Sattigeri
- **机构**：IBM Research（作者 Tejaswini Pedapati、Prasanna Sattigeri 为 IBM 研究人员）
- **发布时间**：2026-09-14
- **会议/期刊**：cs.AI / cs.CE / cs.CL / cs.LG / cs.MA
- **链接**：[arXiv](https://arxiv.org/abs/2609.16305) | [PDF](https://arxiv.org/pdf/2609.16305)

## 摘要翻译

### 英文摘要
Large language model (LLM) agents increasingly operate over long-horizon interactions involving tool use, persistent state, evolving authorization, and external environment feedback. In such settings, safety failures may emerge only after multiple turns, yet existing evaluations often reduce agent behavior to task or attack success, obscuring whether an agent acts, refuses, or remains appropriately calibrated as the interaction evolves. We introduce Blindspot, a benchmark for trajectory-level safety calibration of long-horizon tool-using agents. Blindspot evaluates complete user-agent-environment trajectories through adaptive adversarial interaction, stateful tool execution, and execution-grounded adjudication. Its current instantiation contains 22 attack families and 35 scenarios across seven domains, yielding more than 2,500 long-horizon trajectories with an average interaction length of 14.7 turns. Each trajectory is assigned one of five outcomes: Safe Completion, Correct Refusal, Unsafe Completion, Over-Refusal, or Indeterminate. Unlike fixed attack datasets, Blindspot is an extensible live-simulation framework in which attacks, scenarios, tools, policies, domains, and agent configurations can be added without redesigning the evaluation pipeline. We evaluate 13 proprietary and open-weight LLMs using eight metrics covering unsafe completion, appropriate refusal, benign utility, over-refusal, repeated-run robustness, and post-refusal failure. Preliminary results reveal substantial differences in safety-utility calibration across models and show that failures can emerge only after several initially safe interaction steps. These findings motivate treating agent safety as a trajectory-level property rather than a single-turn or binary success criterion.

### 中文翻译
大语言模型（LLM）智能体越来越多地在涉及工具调用、持久状态、动态授权与外部环境反馈的长程交互中运行。在此类场景下，安全失败可能只在多轮之后才出现，而现有评测常把智能体行为简化为「任务成功」或「攻击成功」，掩盖了智能体在交互演化过程中究竟是行动、拒绝、还是保持恰当校准。作者提出 Blindspot，一个面向长程工具调用智能体的「轨迹级安全校准」基准。Blindspot 通过自适应对抗交互、有状态工具执行与执行落地裁决，评估完整的「用户-智能体-环境」轨迹。其当前实例包含 7 个领域、22 个攻击族、35 个场景，产出 2500+ 条长程轨迹，平均交互长度 14.7 轮。每条轨迹被赋予五种结局之一：安全完成、正确拒绝、不安全完成、过度拒绝、或不确定。与固定的攻击数据集不同，Blindspot 是一个可扩展的实时仿真框架，攻击、场景、工具、策略、领域与智能体配置均可添加而无需重构评测流水线。作者用 8 个指标（覆盖不安全完成、恰当拒绝、良性效用、过度拒绝、重复运行鲁棒性、拒绝后失败）评测了 13 个闭源与开源 LLM。初步结果显示，各模型在「安全-效用」校准上差异巨大，且失败可能只在若干步初始安全的交互之后才出现。这些发现促使将智能体安全视为**轨迹级属性**，而非单轮或二元的成功标准。

### 核心要点提炼
- **研究背景**：LLM 智能体转向长程、工具调用、多轮交互。
- **研究动机**：现有评测只看单轮/二元成功，掩盖了「多轮后才出现的」安全失败。
- **核心方法**：轨迹级安全校准基准，自适应对抗交互 + 有状态工具执行 + 执行落地裁决。
- **主要结果**：13 个模型在 8 指标上安全-效用校准差异巨大；失败可在若干安全步骤后才出现。
- **研究意义**：推动「智能体安全是轨迹级属性」这一范式转变。

## 研究背景与动机

### 领域现状
LLM 智能体正从「单轮问答」走向「长程任务执行」——调用外部工具、维护持久状态、随交互演化授权、接收环境反馈。与之相对，安全评测大多仍是静态攻击数据集 + 单轮成功/失败判断。

### 现有方法的局限性
1. **简化行为判定**：把智能体行为归结为「任务成功」或「攻击成功」，忽略了「正确拒绝」「过度拒绝」「拒绝后失败」等关键中间态。
2. **单轮视角**：长程交互中，安全失败可能潜伏在多轮之后，单轮评测无法捕获。
3. **静态数据集**：攻击与场景固定，无法适应不断演化的威胁模型。

### 研究动机
需要一个**轨迹级**、**可扩展**、**执行落地**的评测框架，刻画智能体在长程交互中的安全-效用校准。

## 研究问题

### 核心研究问题
如何系统评测长程工具调用智能体在完整交互轨迹上的「安全-效用校准」，从而揭示单轮评测无法捕获的安全失败？

## 方法概述

### 核心思想
把智能体安全从「单轮/二元成功」提升为「轨迹级属性」，通过三个关键机制实现：自适应对抗交互、有状态工具执行、执行落地裁决。

### 方法框架

#### 整体架构

![[blindspot_overview.png|800]]

> 图1：Blindspot 基准概览——通过自适应对抗交互生成用户-智能体-环境轨迹，经有状态工具执行与执行落地裁决，为每条轨迹赋予五类结局之一。

#### 各模块详细说明

**模块1：自适应对抗交互（Adaptive Adversarial Interaction）**
- **功能**：生成对抗性的用户输入，并随智能体行为动态调整攻击策略。
- **输入**：攻击族 + 场景定义。
- **输出**：完整的用户-智能体交互轨迹。

**模块2：有状态工具执行（Stateful Tool Execution）**
- **功能**：维护交互过程中的持久状态与动态授权，模拟真实工具调用环境。
- **输入**：智能体的工具调用。
- **输出**：环境状态更新与反馈。

**模块3：执行落地裁决（Execution-Grounded Adjudication）**
- **功能**：基于实际执行结果（而非语义猜测）判定轨迹结局。
- **输出**：五类结局之一——安全完成 / 正确拒绝 / 不安全完成 / 过度拒绝 / 不确定。

### 方法架构图

![[failure_over_turns.png|800]]

> 图2：失败随轮次的演化——说明安全失败可能在若干步初始安全交互后才出现。

## 实验结果

### 实验目标
用 8 个指标评测 13 个 LLM 在长程工具调用中的安全-效用校准，揭示单轮评测无法捕获的失败模式。

### 数据集
- 22 攻击族、35 场景、7 领域
- 2500+ 长程轨迹，平均 14.7 轮

### 主要结果

#### 主实验结果
- 13 个模型在「安全-效用」校准上差异巨大。
- 失败可仅在若干初始安全步骤后才出现，印证「轨迹级安全」的必要性。

#### 结果分析
- 单轮评测会系统性低估长程智能体的安全风险。
- 「过度拒绝」与「拒绝后失败」等中间态，是理解安全-效用权衡的关键。

### 实验结果图

![[attack_family_runs.png|800]]

> 图3：各攻击族的评测结果分布。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出「智能体安全是轨迹级属性」的框架性主张，推动评测范式从「单轮二元」向「轨迹级校准」转变。
- 五类结局的细粒度刻画，为安全-效用权衡提供了可量化语言。

#### 实际应用价值
- **安全评测**：为 LLM 智能体部署前的安全审计提供可扩展基准。
- **策略调优**：帮助开发者定位「过度拒绝」或「拒绝后失败」等具体缺陷。

### 方法优势详解

#### 优势1：轨迹级视角
- **描述**：捕获多轮后才出现的失败，这是单轮评测的盲区。
- **实验验证**：失败在若干安全步骤后才出现的现象被实证。

#### 优势2：可扩展实时仿真
- **描述**：攻击/场景/工具/策略均可热插拔，无需重构流水线。

#### 优势3：执行落地裁决
- **描述**：基于实际执行结果判定，减少语义评判的主观性。

### 局限性分析

#### 局限1：环境仿真保真度
- **描述**：有状态工具执行是对真实部署环境的近似。

#### 局限2：领域覆盖
- **描述**：7 领域虽广，但特定高风险场景（如金融、医疗）仍需扩展。

## 我的综合评价

### 价值评分

#### 总体评分
**8.3/10** - 在「智能体安全评测」这一快速升温的方向上，给出轨迹级、可扩展、执行落地的基准，实用性强。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 「轨迹级安全」范式主张清晰 |
| 技术质量 | 8/10 | 三机制设计完整 |
| 实验充分性 | 8/10 | 13 模型 × 8 指标 × 2500+ 轨迹 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 9/10 | 可直接用于安全审计 |

## 相关论文

### 直接相关
- [[K-Bench_a_clinically_calibrated_benchmark_for_evaluating_large_language_models_in_high-risk_mental_health_conversations|K-Bench]] - 同为 LLM 安全评测基准（昨日推荐）
- [[Universal_Defenses_for_Tool-Integrated_LLM_Agents_Against_Adversarial_Attacks|Universal Defenses for Tool-Integrated LLM Agents]] - 工具集成智能体的对抗攻击防御

> [!tip] 关键启示
> 智能体安全是「轨迹级属性」而非「单轮二元成功」——多轮后才浮现的失败，只有轨迹级评测才能捕获。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 对 LLM 智能体安全、安全评测方向感兴趣的读者强烈推荐。
