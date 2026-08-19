---
date: "2026-08-19"
paper_id: "arXiv:2608.16627"
title: "When Do Explanations Help In-Context Learning? A Comparative Study of Natural Language Explanation Types and Faithfulness"
authors: "Mahdi Dhaini, Adam Dejl, Juraj Vladika, Volkan Özer, Barbara Plank, Gjergji Kasneci"
domain: "大语言模型"
tags:
  - 论文笔记
  - In-Context-Learning
  - 自然语言解释
  - 忠实度
  - Prompting
  - LLM
quality_score: "8.0/10"
created: "2026-08-19"
updated: "2026-08-19"
status: analyzed
---

# When Do Explanations Help In-Context Learning?

## 核心信息
- **论文ID**：arXiv:2608.16627
- **作者**：Mahdi Dhaini, Adam Dejl, Juraj Vladika, Volkan Özer, Barbara Plank, Gjergji Kasneci
- **机构**：LMU Munich（MaiNLP lab / MCML）、Imperial College London、Technical University of Munich（MCML）
- **发布时间**：2026-08-17
- **分类**：cs.CL / cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2608.16627) | [PDF](https://arxiv.org/pdf/2608.16627)

## 摘要翻译

### 英文摘要
Natural language explanations (NLEs) are increasingly used as few-shot rationales that influence model behavior in in-context learning (ICL), but it remains unclear how different NLE types compare. The authors evaluate across six benchmarks and four instruction-tuned models, studying how NLE source (human-written, self-generated, external-LLM-generated) and NLE selection (random vs faithfulness-based filtering) affect downstream utility. On classification benchmarks, adding NLEs to few-shot prompts often improves accuracy; externally generated LLM-NLEs often provide strong utility and remain competitive with human rationales, whereas self-NLEs are more sensitive to the selection strategy. On math reasoning, effects are more model- and source-dependent. Faithfulness-based selection of self-NLEs yields small average gains but can improve or reduce performance depending on metric, task, and model; different faithfulness metrics can disagree substantially. Robustness tests with randomly swapped and out-of-distribution rationales indicate partial robustness, suggesting semantic alignment contributes to gains.

### 中文翻译
自然语言解释（NLE）越来越多地被用作影响上下文学习（ICL）模型行为的少样本 rationale，但不同类型的 NLE 效果如何比较尚不清楚。作者在 6 个基准、4 个指令微调模型上，研究 **NLE 来源**（人工编写、自生成、外部 LLM 生成）与 **NLE 选择**（随机 vs 基于忠实度过滤）对下游效用的影响。在分类基准上，给少样本提示加入 NLE 通常能提升准确率；外部 LLM 生成的 NLE 往往提供很强效用，且与人工 rationale 保持竞争力；自生成 NLE 则对选择策略更敏感。在数学推理上，效果更依赖具体模型与来源。基于忠实度选择自生成 NLE 平均收益很小，且随指标、任务、模型不同而时好时坏；不同忠实度指标之间可能显著不一致。随机交换与分布外 rationale 的鲁棒性测试表明存在部分鲁棒性，说明语义对齐对性能提升有所贡献。

### 核心要点提炼
- **研究背景**：NLE 作为少样本 rationale 日益普及，但类型与选择策略的影响缺乏系统比较。
- **研究动机**：指导实践者在 prompting 流水线中如何选择与报告解释。
- **核心方法**：跨 6 基准 4 模型，系统对比 NLE 来源 × 选择策略 × 忠实度过滤。
- **主要结果**：分类任务上外部 LLM-NLE 强且接近人工 rationale；自 NLE 对选择策略敏感；数学推理依赖模型与来源。
- **研究意义**：为"是否/如何给 ICL 加解释"提供实证依据。

## 研究背景与动机

### 领域现状
解释增强 prompting（explanation-augmented prompting）被广泛使用，NLE 作为少样本 rationale 能改变模型行为，但缺乏系统研究回答"何种解释在何时有效"。

### 现有方法的局限性
- 来源混淆：人工、自生成、外部 LLM 生成的 NLE 效果未被分离。
- 选择策略不明：随机选择 vs 基于忠实度的过滤孰优孰劣缺乏一致结论。
- 忠实度度量不一致：不同忠实度指标可能指向不同的 NLE 选择。

### 研究动机
为实际 prompting 流水线提供可操作的选型建议，并厘清忠实度过滤的真实价值。

## 研究问题

**核心研究问题**：不同类型的自然语言解释（按来源与选择策略划分）在上下文学习中何时、如何影响下游模型性能？

## 方法概述

### 核心思想
通过受控对比实验，把 NLE 的影响拆解为**来源**（human / self / external-LLM）与**选择**（random / faithfulness-filtered）两个维度，并在分类与数学推理两类任务上测量下游效用。

### 方法框架

#### 实验设计
1. **基准**：6 个 benchmark，覆盖分类与数学推理。
2. **模型**：4 个指令微调模型。
3. **变量**：NLE 来源（3 类）× NLE 选择（2 类）。
4. **鲁棒性**：随机交换 rationale、分布外 rationale 测试。

![[setups-overview_page1.png|600]]

> 图1：NLE 来源与选择策略的对比实验设定（来自论文 figures/setups-overview）。

## 实验结果

### 主要结果
- **分类任务**：加入 NLE 常优于无解释少样本；外部 LLM-NLE 效用强，且接近人工 rationale；自 NLE 对选择策略更敏感。
- **数学推理**：效果更依赖模型与来源。
- **忠实度过滤**：对自 NLE 平均收益小，且随指标/任务/模型时好时坏；不同忠实度指标可能显著不一致。
- **鲁棒性**：随机交换与 OOD rationale 显示部分鲁棒性 → 语义对齐贡献了部分收益。

![[plots-all-results-all-models_1_page1.png|600]]

> 图2：各模型在各 benchmark 上的整体结果对比（来自论文 figures）。

## 深度分析

### 创新点
1. **系统化对比**：首次把 NLE 来源与选择策略两维度解耦，跨任务大规模评测。
2. **实用结论**：外部 LLM-NLE 是性价比高的选择，可替代人工 rationale。
3. **忠实度的局限性**：揭示忠实度过滤收益不稳定、指标间不一致。

### 局限性
- 仅 4 个指令微调模型，模型规模/家族覆盖有限。
- 数学推理任务上的结论较混杂，缺乏统一规律。
- 忠实度度量本身的可信度未被充分质疑。

### 未来工作
- 扩展模型规模与家族，验证结论稳定性。
- 深入研究"语义对齐"如何具体转化为性能收益。
- 设计更可靠的忠实度度量与 NLE 选择策略。

### 与相关工作对比
- 相对单点研究"解释是否有效"，本工作提供跨来源、跨选择、跨任务的系统画像。
- 与 CoT / rationale 生成、忠实度度量等研究路线互补，共同刻画"解释增强 ICL"的边界条件。
