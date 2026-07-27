---
date: "2026-02-13"
paper_id: "arXiv:2602.12670"
title: "SkillsBench: Benchmarking How Well Agent Skills Work Across Diverse Tasks"
authors: "42位作者（多机构合作）"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Agent-Skills
  - Benchmark
  - LLM-Agent
  - Paired-Evaluation
quality_score: "7.5/10"
created: "2026-07-27"
updated: "2026-07-27"
status: analyzed
---

# SkillsBench: Benchmarking How Well Agent Skills Work Across Diverse Tasks

## 核心信息
- **论文ID**：arXiv:2602.12670
- **作者**：42位作者（多机构合作）
- **机构**：Ohio State University, UC Davis, UC Berkeley, University of Oxford, UC San Diego
- **发布时间**：2026-02-13
- **链接**：[arXiv](https://arxiv.org/abs/2602.12670) | [PDF](https://arxiv.org/pdf/2602.12670)
- **引用**：152（Semantic Scholar）

## 摘要翻译

### 英文摘要
Agent Skills are structured packages of procedural knowledge that augment large language model (LLM) agents at inference time. Despite rapid adoption, there is no standard way to measure whether they actually help. We present SkillsBench, a benchmark whose current inventory contains 87 tasks across 8 domains paired with curated Skills and deterministic verifiers. Our latest aggregate evaluation runs the 87-task benchmark under matched no-Skills and curated-Skills conditions for 18 model-harness configurations. Curated Skills raise the average pass rate from 33.9% to 50.5% (+16.6 percentage points; 25.5% normalized gain), with configuration-level gains ranging from +4.1 to +25.7 pp. Focused Skills with at most three modules outperform larger or exhaustive bundles, and smaller models with Skills can match larger models without them. SkillsBench establishes paired evaluation as the foundation for rigorous measurement of Skill efficacy on agentic, expertise-heavy work.

### 中文翻译
Agent Skills 是结构化的过程性知识包，在推理时增强大语言模型（LLM）智能体的能力。尽管被快速采用，目前尚无标准方法来衡量它们是否真正有效。我们提出了 SkillsBench，一个包含 8 个领域 87 个任务的基准，每个任务配备精心设计的 Skills 和确定性验证器。我们最新的聚合评估在 18 种模型-框架配置下，对比了有无 Skills 的成对表现。精心设计的 Skills 将平均通过率从 33.9% 提升至 50.5%（+16.6 个百分点；标准化增益 25.5%），不同配置的增益从 +4.1 到 +25.7 个百分点不等。聚焦型 Skills（最多三个模块）优于大型或穷举式捆绑，且搭配 Skills 的小模型可以匹敌无 Skills 的大模型。SkillsBench 将成对评估确立为衡量 Skill 在智能体、专业密集任务上效用的严谨基础。

### 核心要点提炼
- **研究背景**：Agent Skills 被广泛采用但缺乏标准化评估手段
- **研究动机**：需要系统化评测 Skills 是否真正提升智能体表现
- **核心方法**：成对评估（paired evaluation）+ 确定性验证器 + 多领域任务
- **主要结果**：Skills 使平均通过率 +16.6pp，聚焦型 Skills 优于大捆绑
- **研究意义**：建立了 Agent Skills 效用测量的第一个标准化框架

## 研究背景与动机

### 领域现状
LLM Agent 的快速发展催生了大量 Agent Skills——结构化的过程性知识包，用于在推理时增强智能体能力。然而，该领域缺乏统一的评估标准，无法回答"Skills 是否真正有效"这一根本问题。

### 现有方法的局限性
- 评估方式分散，缺乏标准化
- 缺少有无 Skills 的对照实验
- 无法量化 Skills 的实际贡献
- 难以比较不同 Skills 设计的优劣

### 研究动机
建立系统化、可复现的评估框架，通过成对评估方法消除混淆变量，精确量化 Skills 的效用。

## 研究问题

### 核心研究问题
1. Agent Skills 在多大程度上真正提升了智能体的任务完成能力？
2. 什么样的 Skills 设计（规模、结构）最有效？
3. 不同模型-框架配置下 Skills 的效果有何差异？

## 方法概述

### 核心思想
通过**成对评估（Paired Evaluation）**——在同一任务上对比有/无 Skills 的表现——消除模型能力、框架选择等混淆变量，精确量化 Skills 的净贡献。

### 方法框架

#### 整体架构

![[method_page1.png|800]]

> 图1：SkillsBench 方法论概览——包含任务设计、Skills 策划、成对评估和结果分析的完整流程

#### 各模块详细说明

**模块1：任务设计**
- **功能**：构建覆盖多领域的标准化任务集
- **覆盖范围**：8 个领域（代码、数学、推理、知识等），87 个任务
- **关键设计**：每个任务配有确定性验证器，确保评估客观性

**模块2：Skills 策划**
- **功能**：为每个任务设计配套的 Skills
- **Skill 类型**：聚焦型（≤3 模块）、中型、大型/穷举式
- **设计原则**：Skills 应为过程性知识的结构化表达

**模块3：成对评估**
- **功能**：在同一任务上对比有无 Skills 的表现
- **配置**：18 种模型-框架组合
- **指标**：通过率（Pass Rate）、标准化增益

### 关键发现

- **聚焦型 Skills 最优**：≤3 模块的 Skills 表现最好
- **小模型大收益**：小模型 + Skills 可匹敌无 Skills 的大模型
- **增益范围大**：不同配置下增益从 +4.1 到 +25.7pp

## 实验结果

### 主要结果

| 指标 | 无 Skills | 有 Skills | 提升 |
|------|-----------|-----------|------|
| 平均通过率 | 33.9% | 50.5% | +16.6pp |
| 标准化增益 | -- | 25.5% | -- |
| 配置级增益范围 | -- | -- | +4.1 ~ +25.7pp |

### 关键发现
1. **聚焦型 Skills（≤3模块）优于大型/穷举式捆绑**：简洁设计的 Skills 反而更有效
2. **小模型 + Skills ≈ 大模型**：Skills 可弥补模型规模差距
3. **不同配置差异显著**：模型-框架组合的选择影响 Skills 效果

## 深度分析

### 研究价值评估

#### 理论贡献
- **成对评估方法论**：首次将对照实验思想系统化应用于 Agent Skills 评估
- **Skills 设计原则实证**：首次通过大规模实验验证"少即是多"的 Skills 设计原则
- **标准化基准**：87 个任务 + 确定性验证器，为后续研究提供可复现的平台

#### 实际应用价值
- 指导 Agent Skills 的设计和选择
- 帮助开发者理解 Skills 的投资回报
- 为 Agent 平台（如 OpenAI、Anthropic）提供评估参考

### 局限性
- **任务覆盖**：87 个任务仍有限，可能遗漏特定领域
- **Skills 质量依赖**："curated Skills"的质量影响评估结果
- **静态评估**：未考虑 Skills 的动态演化
- **可迁移性未验证**：Skills 在不同 Agent 框架间的迁移效果未知

## 我的综合评价

### 价值评分

#### 总体评分
**7.5/10** — 首个系统性 Agent Skills 评估基准，方法论严谨，具有重要的标准化意义，但任务覆盖和动态性有待扩展

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次提出成对评估方法论，填补空白 |
| 技术质量 | 7/10 | 方法严谨但复杂度适中 |
| 实验充分性 | 8/10 | 18种配置×87任务的大规模评估 |
| 写作质量 | 7/10 | 清晰有条理 |
| 实用性 | 8/10 | 对 Agent 开发有直接指导价值 |

> [!tip] 关键启示
> "少即是多"——聚焦型 Skills（≤3模块）优于大型捆绑，这一发现对 Agent Skills 的设计有直接指导意义。

> [!warning] 注意事项
> - 结果基于"curated Skills"，实际 Skills 质量可能差异很大
> - 仅覆盖 8 个领域，在特定专业领域可能不适用
> - 评估基于确定性验证器，对开放性任务的适用性有限

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！对于关注 Agent Skills 和 LLM Agent 评估的读者，这是必读的基准工作。

## 相关论文
- [[AgentBench]] — 通用 Agent 评估基准
- [[SWE-bench]] — 软件工程 Agent 评估
- [[WebArena]] — Web Agent 评估基准

## 外部资源
- 论文主页：https://arxiv.org/abs/2602.12670
- Semantic Scholar：https://www.semanticscholar.org/paper/1f09ae8491805852a18c386104ee2539d43e29d9
