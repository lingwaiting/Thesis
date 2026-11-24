---
date: "2026-11-24"
paper_id: "arXiv:2611.29972"
title: "When 1+1 ≠ 2: Benchmarking Civil-Time Reasoning in Large Language Models"
authors: "Melika Shirian, Kianoosh Vadaei, Arshia Hemmat, Mohammad Hossein Shaker Ardakani, Ali Mamanpoosh, Matin Moqadas, Amirmasoud Rismanchian, Afsaneh Fatemi"
domain: "大语言模型"
tags:
  - 论文笔记
  - 时间推理
  - 基准测试
  - 民用时间
  - 日历算术
  - 泛化能力
  - Temporal-Reasoning
  - Benchmark
quality_score: "7.5/10"
created: "2026-11-24"
updated: "2026-11-24"
status: analyzed
---

# When 1+1 ≠ 2: Benchmarking Civil-Time Reasoning in Large Language Models

## 核心信息
- **论文ID**：arXiv:2611.29972
- **作者**：Melika Shirian, Kianoosh Vadaei, Arshia Hemmat, Mohammad Hossein Shaker Ardakani, Ali Mamanpoosh, Matin Moqadas, Amirmasoud Rismanchian, Afsaneh Fatemi
- **机构**：--
- **发布时间**：2026-11-23
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.29972) | [PDF](https://arxiv.org/pdf/2611.29972)

## 摘要翻译

### 英文摘要
Calendar arithmetic is non-linear. When 1:59 AM + 1 minute occurs during daylight saving spring-forward, the result is 3:00 AM, not 2:00 AM, as the hour is skipped. We introduce CivilTime-Bench, a benchmark for civil-time reasoning covering daylight saving transitions, non-hour offsets, date-line crossings, and ISO week boundaries. With 60,011 training and 10,500 evaluation examples under strict output-contract evaluation, we benchmark seven LLMs (six instruction-tuned models and Llama-3.1-8B base) and find: zero-shot exact match varies substantially across models, contract-aware prompting often improves format compliance but yields inconsistent accuracy gains, few-shot in-context learning provides model-dependent improvements, and fine-tuning achieves 74.6–86.6% EM on synthetic evaluation but only 36.6–45.2% on HumanTime-500 for models with valid results on both splits, highlighting substantial limitations in generalization.

### 中文翻译
日历算术是非线性的。当凌晨 1:59 加上 1 分钟、恰逢夏令时"spring-forward"跳变时，结果是 3:00 而非 2:00——因为这一小时被跳过了。作者提出 CivilTime-Bench，一个面向"民用时间推理"的基准，覆盖夏令时切换、非整小时偏移、国际日期变更线跨越以及 ISO 周界等情形。在 60,011 个训练样本与 10,500 个评估样本、严格输出契约评估下，作者对 7 个 LLM（6 个指令微调模型 + Llama-3.1-8B 基座）进行评测，发现：零样本精确匹配（EM）在不同模型间差异显著；契约感知提示（contract-aware prompting）常能提升格式合规性，但精度增益并不一致；少样本上下文学习带来的改进与具体模型相关；微调在合成评估集上达到 74.6–86.6% 的 EM，但在 HumanTime-500 上仅 36.6–45.2%（只统计在两个划分上均有有效结果的模型），凸显出明显的泛化局限。作者已公开基准与代码。

### 核心要点提炼
- **研究背景**：日历/民用时间推理涉及大量"非直觉"的边界规则（夏令时、时区、日期线、周界），LLM 在此类任务上表现脆弱。
- **研究动机**：现有时间推理基准多聚焦于简单日期计算，缺少对民用时间复杂边界的系统覆盖。
- **核心方法**：CivilTime-Bench——60,011 训练 + 10,500 评估样本，覆盖夏令时、非整时偏移、日期线跨越、ISO 周界四类边界，采用严格输出契约评估。
- **主要结果**：微调在合成集 74.6–86.6% EM，但在真实分布 HumanTime-500 上骤降至 36.6–45.2%，暴露严重的分布外泛化缺陷。
- **研究意义**：为 LLM 的"民用时间推理"能力提供了系统性的评测框架与泛化警示。

## 研究背景与动机

### 领域现状
大语言模型在数学推理、代码生成等结构化任务上取得长足进展，但"民用时间（civil time）"推理——涉及时区、夏令时、日期变更线、ISO 周规则等人类约定——仍是一个被低估的薄弱环节。这类规则是非线性的：`1:59 + 1 分钟` 在夏令时 spring-forward 跳变时不是 `2:00` 而是 `3:00`。

### 现有方法的局限性
- **覆盖不足**：现有时间推理基准（如日期算术类数据集）多聚焦于简单、线性的日期加减，缺少对夏令时切换、非整小时 UTC 偏移、日期线跨越、ISO 周界等真实边界的系统性覆盖。
- **评估单一**：多数基准只测最终答案对错，未区分"格式合规"与"数值正确"，无法定位模型具体在哪一步出错。

### 研究动机
引入 CivilTime-Bench，用一个严格输出契约（strict output contract）的评估协议，系统刻画 LLM 在民用时间推理上的能力与泛化边界。

## 研究问题

### 核心研究问题
LLM 是否真正掌握了民用时间的非线性规则？在分布内（合成）与分布外（真实 HumanTime-500）评估之间，模型的民用时间推理能力会退化多少？提示策略（契约感知、少样本）与微调各自能带来怎样的增益？

## 方法概述

### 核心思想
构造一个覆盖民用时间四大边界类别的合成基准，并配套一个真实分布的人类标注评测集（HumanTime-500），在"严格输出契约"下统一评估。这样既能大规模、可控地测试边界规则，又能衡量模型向真实分布的泛化。

### 方法框架

#### 基准覆盖的四类边界
1. **夏令时切换（DST transitions）**：spring-forward / fall-back 导致的"跳过/重复"小时。
2. **非整小时偏移（non-hour offsets）**：如 +5:30、+5:45 等非整点 UTC 偏移。
3. **日期线跨越（date-line crossings）**：跨国际日期变更线导致的日期跳跃。
4. **ISO 周边界（ISO week boundaries）**：ISO 8601 周起始/年界等边界。

#### 评估协议
- **规模**：60,011 训练 + 10,500 评估样本。
- **严格输出契约**：要求模型按固定格式输出，区分"格式合规"与"数值正确"，避免格式错误被误记为数值错误。
- **HumanTime-500**：500 条人工构建的真实场景样本，用于测分布外泛化。

![[2611.29972_dataset_page1.png|600]]

> 图1：CivilTime-Bench 数据集概览。展示基准覆盖的民用时间边界类别与样本结构。

### 关键设计
- **边界驱动构造**：以"边界规则"而非"表面格式"为采样维度，确保每条规则都被充分覆盖。
- **双评估集**：合成集测规则掌握，HumanTime-500 测真实泛化，两者对比揭示过拟合。
- **严格契约**：将格式错误与数值错误分离，给出更细粒度的诊断。

## 实验结果

### 实验目标
验证 7 个 LLM 在民用时间推理上的绝对水平、提示策略的增益，以及微调从合成集到真实分布的泛化程度。

### 数据集与基线
- **模型**：6 个指令微调模型 + Llama-3.1-8B 基座（共 7 个）。
- **协议对比**：零样本、契约感知提示（contract-aware prompting）、少样本上下文学习（few-shot ICL）、微调（fine-tuning）。

### 主要结果
- **零样本**：各模型 EM 差异显著，整体偏低，说明民用时间规则未被模型可靠掌握。
- **契约感知提示**：通常提升格式合规性，但数值精度的增益不一致——格式对了不代表算对了。
- **少样本 ICL**：带来模型依赖的改进，收益不稳定。
- **微调**：
  - 合成评估集：74.6–86.6% EM。
  - HumanTime-500：仅 36.6–45.2% EM（仅统计两划分均有有效结果的模型）。

![[fig2_error_heatmap_page1.png|600]]

> 图2：错误热力图。展示不同边界类别与错误类型的分布，定位模型的主要失败模式。

![[fig3_macro_em_page1.png|600]]

> 图3：宏观 EM 对比。对比不同模型在不同评估划分上的精确匹配率。

### 结果分析
最核心的发现是**巨大的分布外泛化鸿沟**：微调后模型在合成集上可达 80%+ 的 EM，但迁移到真实 HumanTime-500 时几乎腰斩。这强烈暗示模型在合成集上学到的是表面的模式匹配，而非可泛化的时间推理规则。契约感知提示虽然改善了格式合规，却未能带来一致的精度提升，说明"输出格式正确"与"推理正确"是两回事。

## 深度分析

### 研究价值评估
- **理论贡献**：系统性地揭示了民用时间推理中的"非线性规则"挑战，以及合成训练到真实分布的泛化鸿沟。
- **实践价值**：为需要可靠时间推理的下游应用（日程、金融、跨时区协作）提供了评测与警示。
- **领域影响**：把"时间推理"从"日期算术"细化为"民用时间推理"，推动更精细的能力评估。

### 方法优势
- **边界覆盖全面**：四类边界 + ISO 周界，系统性。
- **双评估集设计**：合成 vs 真实分布对比，能有效暴露过拟合。
- **严格契约**：分离格式与数值错误，诊断更精细。

### 局限性
- **英文/单一文化视角**：民用时间规则随地区差异大，当前基准可能未覆盖非西方时区/历法。
- **HumanTime-500 规模较小**：500 条真实样本，统计力度有限。
- **合成集与真实集的分布差异未充分消解**：泛化鸿沟的根因（规则 vs 模式）尚未彻底验证。

## 我的综合评价

### 总体评分
**7.5/10** — 选题精准、评测设计严谨，用"合成 vs 真实分布"的对比揭示了一个重要的泛化缺陷，对时间推理能力的评估具有示范意义。

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 边界驱动的民用时间推理基准是有价值的增量，但本质仍是"构造基准 + 评测"范式 |
| 技术质量 | 8/10 | 四类边界 + 严格契约 + 双评估集，设计严谨 |
| 实验充分性 | 7/10 | 7 模型 × 多协议对比充分，但真实集规模偏小 |
| 写作质量 | 8/10 | 问题明确，动机清晰 |
| 实用性 | 7/10 | 对时间敏感型应用有直接参考价值 |

> [!tip] 关键启示
> 合成基准上的高分数可能是"表面模式匹配"而非"规则掌握"——必须用分布外的真实评估集来检验泛化，否则会严重高估模型能力。

> [!warning] 注意事项
> - 微调带来的合成集高 EM 未必能迁移到真实分布。
> - 契约感知提示改善格式合规 ≠ 改善推理正确性。
> - 基准视角可能偏西方时区，非西方民用时间规则覆盖待验证。

## 相关论文
- [[20_Research/Papers/大语言模型/Decision_Models_for_Multilingual_Emotion_Classification_A_Comparative_Study_Against_LLMs_and_Encoders|Decision Models for Multilingual Emotion Classification]]
