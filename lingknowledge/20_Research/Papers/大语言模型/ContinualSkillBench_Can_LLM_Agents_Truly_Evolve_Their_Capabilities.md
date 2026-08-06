---
date: "2026-08-06"
paper_id: "arXiv:2608.03874"
title: "ContinualSkillBench: Can LLM Agents Truly Evolve Their Capabilities?"
authors: "Tianyi Guan, Yiding Wang, Haotong Yang, Siyuan Cao, Shirui Liu, Yi Hu, Jiaqi Li, Muhan Zhang"
domain: "大语言模型"
tags:
  - 论文笔记
  - Agent-Skills
  - Continual-Learning
  - LLM-Agent
  - Benchmark
quality_score: "8.0/10"
created: "2026-08-06"
updated: "2026-08-06"
status: analyzed
---

# ContinualSkillBench: Can LLM Agents Truly Evolve Their Capabilities?

## 核心信息
- **论文ID**：arXiv:2608.03874
- **作者**：Tianyi Guan, Yiding Wang, Haotong Yang, Siyuan Cao, Shirui Liu, Yi Hu, Jiaqi Li, Muhan Zhang
- **机构**：北京大学人工智能研究院 (PKU)；北京通用人工智能研究院 (BIGAI)
- **发布时间**：2026-08-04
- **链接**：[arXiv](http://arxiv.org/abs/2608.03874) | [PDF](https://arxiv.org/pdf/2608.03874)
- **代码**：https://github.com/gtynnn060110-hash/continual-skill-bench-final

## 摘要翻译

### 英文摘要
Modern agent frameworks equip large language models with external skill libraries to solve complex tasks. However, it remains unclear whether these systems can effectively evolve their skills and whether the resulting skills improve task-solving capabilities. To bridge this gap, we introduce ContinualSkillBench, a dynamic evaluation framework for in-context continual skill learning. It covers five representative domains, each containing 100 interconnected subtasks ordered by increasing difficulty and opportunities for cross-task skill reuse. Our experiments show that sequential execution generally improves performance, but the gains vary substantially across models and domains. Moreover, in-context learning performs comparably to explicit skill maintenance on average, suggesting that much of the improvement arises from adaptation to prior context and feedback rather than reusable skill abstraction alone.

### 中文翻译
现代 Agent 框架为 LLM 配备外部技能库以解决复杂任务。然而，这些系统能否有效演化其技能、以及生成的技能是否真正提升了任务解决能力，仍是一个未解的问题。为此，我们提出了 ContinualSkillBench，一个用于评估上下文持续技能学习的动态框架。该基准覆盖五个代表性领域，每个领域包含 100 个按难度递增和技能复用机会排序的互联子任务。实验表明序列执行普遍提升性能，但增益因模型和领域显著不同。此外，上下文学习与显式技能维持在平均表现上可比，说明大部分提升来自对先前上下文和反馈的适应，而非可复用技能的抽象。

### 核心要点提炼
- **研究背景**：LLM Agent 依赖外部技能库，但技能多为静态、由人类专家编写
- **研究动机**：Agent 能否在实际部署中自主演化技能以持续提升任务表现？
- **核心方法**：构建技能依赖链驱动的序列任务基准，评估 Agent 的在上下文持续学习能力
- **主要结果**：序列执行在 14/15 种模型-领域组合中提升表现（平均 +16.9%），但显式技能维护在平均上并未超越纯上下文学习
- **研究意义**：揭示了当前 Agent 技能演化机制的瓶颈——提升多来自表面适应而非深层可复用技能抽象

## 研究背景与动机

### 领域现状
LLM Agent 正从简单问答系统演化为能解决复杂实际问题的自主 Agent。为弥补预训练权重的不足，"Agent 技能"（结构化文档形式的领域知识）被广泛采用，已被 Claude Code、Codex 等主流平台集成。

### 现有方法的局限性
- 现有技能库多为静态，依赖人类专家手动编写，成本高且难以覆盖所有场景
- 大多数 Agent 评估基准假设固定技能集，不考察 Agent 持续获取新技能的能力
- 参数化持续学习（微调）会导致灾难性遗忘

### 研究动机
在真实部署场景中，用户仅提供任务描述和反馈。Agent 能否自主从交互中合成和演化技能？现有工作在评估这一能力方面存在系统性缺失。

## 研究问题

### 核心研究问题
1. LLM Agent 能否通过序列任务交互持续演化其技能？
2. 演化后的技能是否真正提升了后续任务的表现？
3. 技能提升的本质是深层可复用技能抽象还是表面的格式适应？

## 方法概述

### 核心思想
构建基于技能依赖链的序列任务基准，让 Agent 按序执行互联子任务，通过反馈持续更新技能库，从而系统评估其持续学习能力。

### 方法框架

#### 整体架构

![[pipeline_preview_vector_page1.png|800]]

> 图1：ContinualSkillBench 三阶段流水线：Phase 1 从五个领域收集约 30,000 个任务；Phase 2 通过 LLM 辅助筛选、技能依赖分析和人工审核构建序列任务流；Phase 3 通过指令-执行-反思三回合协议评估 Agent。

#### 各模块详细说明

**Phase 1：基准构建**
- 从 Finance、Healthcare、Law、Math、Office 五个领域收集原始任务
- 来源覆盖三类：经典数据集（OlympiadBench, LawBench）、开放 Agent 数据集（GAIA, ClawBench）、复杂人类评估基准（OMBench）
- 初始任务池约 30,000 个独立任务

**Phase 2：任务筛选与排序**
- LLM 技能标注：识别每个任务所需的核心技能并给出难度评级
- 成对依赖评估：对任务对进行技能迁移依赖判断，构建有向依赖图
- 图排序：在难度约束下对图进行拓扑排序，将简单且基础的任务前置
- 人工审核：确保依赖链的逻辑合理性和任务质量

**Phase 3：Agent 评估协议**
- 三回合协议：任务介绍（含当前技能库快照）→ 任务执行 → 反思与技能更新
- 技能维护通过 Create Skill 和 Modify Skill 元技能完成
- 评估器：精确匹配/F1、数值评估、Rubric 裁判、程序化测试

### 结构验证
- 跨领域平均 69.5% 的任务复用了序列中早期出现的至少一项核心技能
- 目标任务平均 35.5% 的核心技能在历史中有语义对应项
- 排序序列在所有领域和窗口尺寸下均优于随机排列

## 实验结果

### 实验设置
- **模型**：GPT-4o、GPT-5.3-Codex、Claude 4.7 Opus
- **Harness**：基于 Codex CLI 和 Claude Code 的序列 Agent
- **设置**：Independent（每任务独立、重置技能库）vs Sequential（持续累积技能库）

### 主要结果

#### 发现1：序列执行普遍提升任务表现
- 15 种模型-领域组合中，13 种原始奖励提升，14 种归一化奖励提升
- 平均提升：原始奖励 +0.071，归一化奖励 +0.078（相对 +16.9%）

#### 发现2：提升幅度因模型差异显著
- GPT-5.3-Codex 获得最大平均归一化提升 (+0.098)
- Opus 4.7 的独立基线最强但序列增益并非最大
- GPT-4o 从较低基线获得了可观的相对提升

#### 发现3：领域依赖性
- Healthcare 提升最大 (+0.149)，Law/Office/Mathematics 较小 (+0.052~0.058)
- Opus 4.7 在 Mathematics 上归一化奖励出现唯一下降 (-0.008)

#### 发现4：显式技能维护 vs 纯上下文学习 (ICL)
- 独立、ICL、技能维护的平均归一化奖励分别为 0.466、0.605、0.602
- ICL 在 Rubric 评分任务上更强，显式技能在精确匹配和程序化任务上更强
- **关键洞察**：大部分 Sequential 收益来自保留的上下文和反馈，而非显式技能创建

### 技能库动态分析

![[skill_calls_frequency_openai_page1.png|600]]

- GPT-4o 在 5 个领域共积累 384 个技能，GPT-5.3-Codex 仅 205 个
- GPT-4o 的技能复用频率更低、质量评分更差——倾向于积累碎片化的任务特定技能
- 模型能力越弱，技能碎片化越严重

## 深度分析

### 研究价值评估

#### 理论贡献
- **首次系统评估 Agent 在上下文技能演化**：填补了从静态技能评估到动态技能演化的空白
- **揭示技能演化的真实瓶颈**：表面适应 vs 深层技能抽象的区分具有重要理论意义
- **技能依赖链构建方法学**：LLM 辅助的成对依赖评估和图排序方法可复用于其他基准

#### 实际应用价值
- 为 Agent 平台的技能管理策略提供实证指导
- 提示技能演化功能需要更强的参数化方法，而非仅依赖上下文

### 局限性分析

1. **仅限上下文学习**：未探索参数化持续学习方法（微调、LoRA 等）在技能演化中的作用
2. **技能依赖由 LLM 判断**：LLM 判定的"技能迁移依赖"可能不完全反映真实认知依赖
3. **领域限于文本/代码任务**：未覆盖多模态或具身 Agent 场景
4. **评估指标可能不完美**：Rubric Judge 本身是 LLM，存在评估偏差

### 方法优势

1. **生态效度高**：任务来自真实数据集，非合成环境
2. **多层级评估**：四种评估器覆盖不同输出类型
3. **结构验证严谨**：通过语义相似度分析和随机排列对比验证了任务序列的技能连续性

## 我的综合评价

### 总体评分
**8.0/10** — 系统、严谨的 Agent 持续学习评估框架，揭示了重要的能力边界，但方法论上有改进空间。

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 首次系统评估 Agent 技能演化，但核心思路建立在前人工作之上 |
| 技术质量 | 8/10 | 基准构建严谨，多层级评估，结构验证充分 |
| 实验充分性 | 8/10 | 三种模型、两种 Harness、ICL 消融、技能库动态分析 |
| 写作质量 | 8/10 | 结构清晰，论述充分 |
| 实用性 | 9/10 | 直接指导 Agent 技能管理实践，基准和代码开源 |

### 重点关注
- **技能提升的本质**：表层格式适应而非深层推理增强——这是最重要的发现
- **技能碎片化与模型能力的关系**：较弱模型更容易积累低质量碎片化技能
- **ICL vs 显式技能**：上下文和反馈本身可能就是最有效的"技能"

> [!tip] 关键启示
> 当前 Agent 的技能演化机制更像是"格式记忆"而非"能力提升"——我们需要更强的技能抽象和迁移方法。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐！对理解 LLM Agent 技能管理的真实能力边界至关重要。
