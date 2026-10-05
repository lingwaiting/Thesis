---
date: "2026-10-02"
paper_id: "arXiv:2610.03190"
title: "Not Until the Evidence Says So: Teaching LLM Investigators When to Close a Case"
authors: "Tingzhu Bi, Ping Wang, Meng Ma"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Agent
  - Investigation
  - Evidence-Grounding
  - Calibration
quality_score: "8.0/10"
created: "2026-10-05"
updated: "2026-10-05"
status: analyzed
---

# Not Until the Evidence Says So: Teaching LLM Investigators When to Close a Case

## 核心信息
- **论文ID**：arXiv:2610.03190
- **作者**：Tingzhu Bi, Ping Wang, Meng Ma
- **机构**：--
- **发布时间**：2026-10-02
- **类别**：cs.CL / cs.AI / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.03190) | [PDF](https://arxiv.org/pdf/2610.03190)
- **推荐评分**：8.93 / 10

## 摘要翻译

### 英文摘要
Accident, defect and outage investigations end with a decision that ordinary question answering never faces: whether the evidence gathered so far is enough to close the case. We study this decision for LLM investigators, which request evidence from a case file, revise their hypotheses, and either close the case with a conclusion grounded in what they read or leave it open and name what is missing. This judgment does not come with capability: an untrained 9B model overstates its evidence in 97% of its answers, and a frontier model that identifies the right cause in 84% of cases still overstates in 91% and closes 17 of the 41 cases whose official finding is "cause undetermined". We build Nautil, 731 audited cases from aviation, rail, maritime, chemical-safety and vehicle-defect reports and production server incidents, with teacher trajectories, an out-of-distribution test set and counterfactual evidence versions.

### 中文翻译
事故、缺陷与宕机调查最终面临一个普通问答永远不会遇到的决策：目前已收集的证据是否足以结案。本文研究 LLM 调查员如何做出这一决策——它们从案件档案中请求证据、修正假设，然后要么以读到的内容为依据给出结论并结案，要么保持案件开放并指出缺失的信息。这一判断并不随能力自动出现：一个未训练的 9B 模型在 97% 的回答中夸大其证据；而一个在 84% 的案例中能识别正确原因的前沿模型，仍然在 91% 的回答中夸大证据，并在官方结论为「原因未定」的 41 个案例中错误结案了 17 个。作者构建了 Nautil——包含 731 个经过审计的案例，来自航空、铁路、海事、化学品安全、车辆缺陷报告以及生产服务器事故，并配有教师轨迹、分布外测试集和反事实证据版本。

### 核心要点提炼
- **研究背景**：LLM 用于事故调查等高风险决策场景，但「何时结案」这一判断能力被忽视。
- **研究动机**：现有 LLM 普遍夸大证据（overstatement），即便识别出正确原因也缺乏证据校准。
- **核心方法**：构建 Nautil 数据集，通过微调 + 强化学习教会模型在证据不足时保持案件开放。
- **主要结果**：微调使夸大率从 97% 降至 35%，正确非夸大结论从 3% 升至 43%；RL 进一步把平衡准确率从 69.2 提到 83.3。
- **研究意义**：为「证据依赖的结案决策」建立了可评估、可训练的任务定义。

## 研究背景与动机

### 领域现状
LLM 智能体被越来越多地用于信息检索、调查分析等任务。但在事故/缺陷调查这类高风险场景中，正确的判断不仅是「找到原因」，更是「知道证据是否足够」。普通问答假设总有答案，而真实调查必须能说「证据不足，不能结案」。

### 现有方法的局限性
- LLM 的过度自信导致证据夸大（overstatement），模型倾向于给出「有把握」的结论。
- 「识别正确原因」的高准确率掩盖了「错误结案」的严重问题。
- 缺少专门的基准来衡量「证据依赖的结案决策」质量。

### 研究动机
作者希望把「何时结案」作为独立能力来研究：模型能否在证据不足时克制地保持案件开放，并在证据充分时才给出有依据的结论。

## 研究问题

### 核心研究问题
LLM 调查员能否学会「仅当证据充分时才结案」？如何度量这一能力，又如何在训练中强化它？

## 方法概述

### 核心思想
把结案决策拆解为三个可测维度——结案准确率（closure accuracy）、证据依赖（evidence dependence）、结论与缺口质量（conclusion and gap quality）——并用微调轨迹与仅奖励结案决策的强化学习来训练模型。

### 方法框架

#### 整体架构
模型从案件档案中请求证据、修正假设，然后决策结案（给出有依据结论）或保持开放（指出缺失信息）。

![[fig_overview_pipeline_page1.png|800]]

> 图1：Nautil 调查员流水线概览，展示证据请求 → 假设修正 → 结案决策的整体流程。

#### 各模块详细说明

**模块1：证据请求与假设修正**
- **功能**：调查员从案件文件请求证据，并基于读到的内容修正其假设。
- **输入**：案件档案（case file）。
- **输出**：更新后的假设。

**模块2：结案决策**
- **功能**：决定是「结案」还是「保持开放」。
- **输入**：已收集证据 + 当前假设。
- **输出**：有依据的结论，或指出缺失信息的开放状态。

**模块3：三重评估**
- **功能**：度量结案质量。
- **处理流程**：
  1. **closure accuracy**：相对「仅读来源」的规则与各来源内的准确率。
  2. **evidence dependence**：移除结论依据后，检查模型是否停止结案。
  3. **conclusion and gap quality**：判断模型断言与缺失信息的清单质量。
- **关键技术**：反事实证据版本（counterfactual evidence）用于测试证据依赖。

## 实验结果

### 数据集
- **Nautil**：731 个经过审计的案例，来源涵盖航空、铁路、海事、化学品安全、车辆缺陷报告及生产服务器事故。
- 含教师轨迹、分布外测试集、反事实证据版本。

### 主要结果

| 指标 | 未训练 9B | 微调后 | RL 后 |
|------|-----------|--------|-------|
| 证据夸大率 | 97% | 35% | -- |
| 正确非夸大结论 | 3% | 43% | -- |
| 平衡准确率 | -- | 69.2 | **83.3** |
| 来源内准确率 | -- | 60.4 | **74.1** |

> 关键数字：移除结论依据使微调模型的结案率下降 26 个百分点（相对匹配对照），说明模型学会了证据依赖。RL 仅奖励结案决策，把平衡准确率提到 83.3，与教师水平相当，但证据依赖略有代价。

## 深度分析

### 研究价值评估

#### 理论贡献
- **新任务定义**：把「证据充分的结案决策」从一般问答中独立出来，提出三重评估。
- **校准视角**：揭示能力（找对原因）与校准（克制结案）是两个正交维度。

#### 实际应用价值
- **高风险决策**：适用于事故调查、医疗诊断、审计等「宁可不答，不可乱答」的场景。
- **可训练性**：证明通过微调 + RL 可显著提升证据校准。

### 方法优势详解

#### 优势1：反事实证据测试
- **描述**：通过移除结论依据检查模型是否停止结案，直接度量「证据依赖」。
- **技术基础**：counterfactual evidence 版本设计。
- **实验验证**：结案率下降 26 个百分点，证明模型真正依赖证据。

#### 优势2：多来源真实案例
- **描述**：Nautil 覆盖 5 类事故报告 + 生产服务器事故，接近真实部署。
- **技术基础**：审计后的真实案例 + 分布外测试集。

### 局限性
- RL 在提升结案准确率的同时，证据依赖略有下降，存在准确率与校准的权衡。
- 数据集规模（731 案例）相对有限，跨领域泛化仍有待验证。
- 前沿模型的「夸大」问题依然严重，本文方法主要针对可微调的 9B 模型。

### 相关论文对比
- 与 LLM 校准（calibration）研究呼应，但将校准从「置信度」细化为「证据依赖的结案决策」。
- 与 agentic 检索/调查工作（如 RAG agent）相关，但聚焦于「何时停止检索并结案」这一更难的元决策。

## 总结
本文把一个被忽视却至关重要的问题——LLM 调查员何时能基于证据结案——转化为可度量、可训练的任务。其「克制结案」的核心洞见对任何依赖 LLM 做高风险决策的系统都有借鉴意义。
