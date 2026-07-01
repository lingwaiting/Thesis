---
date: "2026-07-01"
paper_id: "arXiv:2606.31543"
title: "Modality-Driven Search with Holistic Trace Judging for ARC-AGI-2"
authors: "Johan Land"
domain: "大语言模型"
tags:
  - 论文笔记
  - ARC-AGI
  - Reasoning
  - Visual-Reasoning
  - Search-Operator
  - Multi-Modality
  - LLM-Judge
quality_score: "8.0/10"
created: "2026-07-01"
updated: "2026-07-01"
status: analyzed
---

# Modality-Driven Search with Holistic Trace Judging for ARC-AGI-2

## 核心信息
- **论文ID**：arXiv:2606.31543
- **作者**：Johan Land
- **机构**：--
- **发布时间**：2026-06-30
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2606.31543v1) | [PDF](https://arxiv.org/pdf/2606.31543v1)
- **分类**：cs.AI, cs.CL, cs.LG

## 摘要翻译

### 英文摘要
Large language models can produce fluent, internally coherent reasoning traces for abstract reasoning tasks while still being confidently wrong - making selection among candidates, not just generation, the central challenge. I present a solver for ARC-AGI-2, a few-shot visual reasoning benchmark, built around two principles: (i) treating reasoning modalities as search operators, generating diverse candidates independently across text, image, and code channels, and (ii) context-preserving holistic judging, in which a judge model jointly compares all candidate reasoning traces within a single long-context prompt. Unlike self-consistency or majority voting, this approach reliably recovers correct minority hypotheses on tasks where the modal answer is wrong. On the ARC Prize semi-private evaluation set, the solver achieves 72.9 percent at USD 38.99 per task - the highest score on the verified leaderboard at the time of writing, exceeding the best standalone frontier models, GPT-5.2 Pro at 54.2 percent and Gemini 3 Pro at 54.0 percent, by +18.7 percentage points. On the public evaluation set, it achieves 76.1 percent at USD 19.69 per task. I release the full source code and document extensive negative results, including the finding that prescriptive prompting templates and iterative refinement systematically reduce hypothesis diversity and degrade performance.

### 中文翻译
大语言模型可以为抽象推理任务生成流畅、内部自洽的推理 Trace，但仍可能自信地出错——这使得在候选解之间进行选择而非仅仅生成成为核心挑战。我提出一个 ARC-AGI-2（少样本视觉推理基准）求解器，基于两个原则：(i) 将推理模态（文本、图像、代码）作为搜索算子，独立生成多样化候选解；(ii) 上下文保留的全局评判（Holistic Judging），其中 Judge 模型在单个长上下文 Prompt 中联合比较所有候选推理 Trace。与自洽性（Self-Consistency）或多数投票不同，该方法在多数答案错误的任务上可靠地恢复了正确的少数假设。在 ARC Prize 半私有评估集上，求解器以每任务 $38.99 的成本达到 72.9%——这是撰写时已验证排行榜上的最高分，超越最佳独立前沿模型 GPT-5.2 Pro（54.2%）和 Gemini 3 Pro（54.0%）达 +18.7 个百分点。在公开评估集上，以每任务 $19.69 达到 76.1%。我发布了完整源代码并记录了广泛的负结果，包括预设 Prompt 模板和迭代精炼系统性降低假设多样性并损害性能的发现。

### 核心要点提炼
- **研究背景**：LLM 在抽象视觉推理上仍不可靠，关键挑战从"如何生成"转向"如何选择"
- **研究动机**：ARC-AGI-2 需要超越简单 LLM 调用，结合搜索和评判能力
- **核心方法**：(1) 多模态并行搜索——文本/图像/代码三种通道独立生成候选；(2) 全局上下文评判——Judge 在一个 Prompt 中比较所有候选
- **主要结果**：ARC Prize 排行榜第一（72.9%），超 GPT-5.2 Pro 18.7pp
- **研究意义**：展示了"搜索 + 评判"范式在抽象推理挑战上的巨大潜力

## 研究背景与动机

### 领域现状
ARC-AGI-2 是衡量 AI 抽象视觉推理能力的硬核基准。最强的独立前沿模型（GPT-5.2 Pro、Gemini 3 Pro）也仅能解决约 54% 的任务。这些模型可以生成流畅的推理 Trace，但经常自信地给出错误答案。这表明在复杂推理任务中，候选解选择（而非仅生成）才是核心瓶颈。

### 现有方法的局限性
1. **Self-Consistency / Majority Voting**：在多数答案错误时天然失败
2. **单一模态推理**：文本、图像或代码单独使用时各有盲区
3. **迭代精炼**：作者发现这系统性降低多样性，反而损害性能
4. **预设 Prompt 模板**：同样降低假设多样性

### 研究动机
需要一个能够：(1) 从多模态角度生成多样化候选解；(2) 在多数答案错误时仍能选出正确答案的求解框架。

## 研究问题

### 核心研究问题
**如何在 ARC-AGI-2 这样多数独立模型答案错误的情况下，可靠地找到正确答案？**

关键洞察：正确的少数假设存在于候选池中，挑战在于"选择"而非"生成"。

## 方法概述

### 核心思想
两个原则驱动整个方法：
1. **模态作为搜索算子**：文本、图像、代码三种模态独立生成候选解，最大化多样性
2. **全局上下文评判**：Judge 模型在一个长上下文 Prompt 中同时看到所有候选推理 Trace，做出全局最优选择

![[pipeline.png|800]]

> 图1：模态驱动搜索 + 全局评判的整体流水线

### 方法框架

#### 组件1：多模态候选生成
- **文本通道**：LLM 直接推理 ARC 任务
- **图像通道**：利用视觉理解能力分析网格模式
- **代码通道**：生成 Python 程序形式化推理过程
- **关键特性**：三通道独立并行，确保最大多样性

#### 组件2：全局上下文评判
- **输入**：所有候选推理 Trace 在一个 Prompt 中
- **机制**：Judge 模型在完整上下文中联合比较，而非单独评分
- **优势**：能够识别少数正确但逻辑更优的假设
- **与 Self-Consistency 的本质区别**：不依赖"多数正确"假设，而是依赖"正确假设在上下文中更可识别"

#### 负结果记录（重要！）
作者诚实地记录了以下负结果：
1. **预设 Prompt 模板**系统性降低多样性 → 损害性能
2. **迭代精炼**虽然改善表面连贯性，但减少多样性 → 性能反面下降
3. 这一发现对 Prompt Engineering 的当前实践有重要启示

## 实验结果

### 主要结果

| 方法 | 半私有集 | 公开集 | 成本 |
|------|----------|--------|------|
| **本文方法** | **72.9%** | **76.1%** | $38.99/$19.69 |
| GPT-5.2 Pro | 54.2% | -- | -- |
| Gemini 3 Pro | 54.0% | -- | -- |

- 超越最强独立模型 **+18.7 个百分点**
- 验证排行榜第一名（撰写时）

### 关键发现
1. **多样性至关重要**：三模态独立生成比单一模态多次采样更有效
2. **全局评判优于独立评分**：联合比较所有候选优于分别打分后投票
3. **少即是多**：复杂 Prompt 模板和迭代精炼反而有害
4. **开源完整代码**：含所有负结果

## 深度分析

### 研究价值评估

#### 理论贡献
1. **"生成 vs 选择"框架**：将推理问题从生成质量重新聚焦到选择质量
2. **模态作为搜索算子**：将多模态能力形式化为独立搜索通道
3. **反直觉发现**：预设模板和迭代精炼损害性能——挑战了 Prompt Engineering 的常识

#### 实际应用价值
- **ARC Prize 实现**：排行榜第一，展示实践可行性
- **开源可复现**：完整代码公开
- **方法论可迁移**：模态搜索 + 全局评判范式可用于其他推理任务

### 局限性分析
1. **成本较高**：$38.99/task 对于大规模部署不实用
2. **单作者论文**：未经过同行评审
3. **特定于 ARC**：方法是否适用于其他推理任务待验证
4. **依赖模型能力**：文本/图像/代码通道的质量受限于底层模型

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - ARC-AGI-2 排行榜第一的工程突破，方法直觉清晰，负结果记录极具价值

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 模态搜索 + 全局评判组合新颖有效 |
| 技术质量 | 7/10 | 方法简洁但依赖底层模型质量 |
| 实验充分性 | 7/10 | ARC 单一基准，缺乏其他推理任务评估 |
| 写作质量 | 9/10 | 开放完整代码 + 负结果记录，极其诚实 |
| 实用性 | 7/10 | 排行榜第一但成本高，需优化效率 |

### 重点关注
- "搜索 + 评判"范式的核心洞察
- 负结果：预设模板和迭代精炼损害多样性
- 全局评判 vs Self-Consistency 的本质区别

---

> [!tip] 关键启示
> 当模型经常自信地犯错时，答案在于"选择"而非"生成"——用多模态并行搜索生成多样候选，用全局上下文评判选出最佳。

> [!warning] 注意事项
> - 成本（$39/task）限制大规模应用
> - 单作者、未经同行评审
> - 负结果（模板有害）对 Prompt Engineering 实践有重要警示

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！排行榜第一 + 负结果诚实记录，对推理研究和 Prompt Engineering 都有重要启示。
