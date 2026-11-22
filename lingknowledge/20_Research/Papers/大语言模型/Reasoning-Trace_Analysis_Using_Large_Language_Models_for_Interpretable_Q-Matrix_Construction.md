---
date: "2026-11-22"
paper_id: "arXiv:2611.26422"
title: "Reasoning-Trace Analysis Using Large Language Models for Interpretable Q-Matrix Construction"
authors: "Jihong Zhang, Zijing Jin, Mingfeng Xue"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 诊断分类模型
  - Q矩阵
  - 思维链
  - 可解释性
  - Chain-of-Thought
  - Q-Matrix
quality_score: "7.87/10"
created: "2026-11-22"
updated: "2026-11-22"
status: analyzed
---

# Reasoning-Trace Analysis Using Large Language Models for Interpretable Q-Matrix Construction

## 核心信息
- **论文ID**：arXiv:2611.26422
- **作者**：Jihong Zhang, Zijing Jin, Mingfeng Xue
- **机构**：阿肯色大学（University of Arkansas）；北卡罗来纳大学格林斯伯勒分校（University of North Carolina at Greensboro）
- **发布时间**：2026-11-19
- **会议/期刊**：arXiv 预印本（cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2611.26422) | [PDF](https://arxiv.org/pdf/2611.26422)

## 摘要翻译

### 英文摘要
Large language models (LLMs) can generate Q-matrices for diagnostic classification models that approximate expert specifications, but evaluations have focused on the final matrices, leaving the basis of individual item-attribute assignments unexamined. This opacity limits the responsible use of AI in measurement. We propose Reasoning-Trace Analysis (RTA), a framework in which an LLM generates attribute-labeled reasoning steps through chain-of-thought (CoT) prompting and these steps are analyzed to reconstruct, compare, and inspect each assignment. RTA is intended to supplement expert review and empirical validation. In a simulation study with perturbed fraction subtraction items, four LLMs recovered the required attributes and withheld attributes the items did not require. In an empirical study with 11 fraction subtraction items, responses from 536 examinees, four LLMs, up to three temperatures, and 100 replications per condition, CoT-derived Q-matrices overlapped substantially with the reference Q-matrix (mean overlap .809) but less than directly generated Q-matrices (.877), and they varied more across replications. Inspection of the traces linked disagreements to item-type coding, labeling rules, and how operations were expressed as steps. Q-matrices with similar overlap differed in fit to examinee responses. RTA provides inspectable evidence that matrix-level agreement and fit do not.

### 中文翻译
大语言模型（LLM）能为诊断分类模型（DCM）生成近似专家规范的 Q 矩阵，但现有评测只关注最终的矩阵，而未检验单个题目-属性赋值的依据。这种不透明性限制了 AI 在心理测量中的负责任使用。本文提出推理轨迹分析（RTA）框架：LLM 通过思维链（CoT）提示生成带属性标签的推理步骤，再对这些步骤进行分析以重建、比较与审查每个赋值。RTA 旨在补充专家审查与经验验证。在扰动分数减法题目的模拟研究中，四个 LLM 能恢复所需属性并抑制题目不需要的属性。在一项包含 11 道分数减法题、536 名受试者、4 个 LLM、最多 3 个温度、每条件 100 次重复的经验研究中，CoT 派生的 Q 矩阵与参考 Q 矩阵有较高重叠（平均重叠 0.809），但低于直接生成的 Q 矩阵（0.877），且跨重复变异更大。轨迹审查将分歧关联到题目类型编码、标签规则以及运算如何被表达为步骤。相似重叠度的 Q 矩阵在拟合受试者响应方面存在差异。RTA 提供了矩阵级一致性与拟合无法提供的可审查证据。

### 核心要点提炼
- **研究背景**：LLM 可生成近似专家规范的 Q 矩阵，但"为何这样赋值"不可见，阻碍其在测量中的负责任使用。
- **研究动机**：现有评测只看最终 Q 矩阵，忽视了单个题目-属性赋值的依据。
- **核心方法**：RTA 框架——用 CoT 让 LLM 生成带属性标签的推理步骤，再分析这些轨迹来重建、比较、审查每个赋值。
- **主要结果**：CoT 派生的 Q 矩阵与参考矩阵重叠 0.809（低于直接生成的 0.877），但提供了可审查的赋值依据；相似重叠度的矩阵在拟合受试者响应上仍有差异。
- **研究意义**：为"AI 辅助测量"提供了可解释、可审查的中间证据，补充而非替代专家审查与经验验证。

## 研究背景与动机

### 领域现状
诊断分类模型（DCM）用于判断考生掌握了哪些技能/属性，其核心是 Q 矩阵——一个"题目 × 属性"的二进制赋值表。传统上 Q 矩阵由领域专家手工构建，过程劳动密集、耗时且昂贵，误指定的条目会扭曲题目参数估计与考生分类。

### 现有方法的局限性
LLM 已能生成接近专家规范的 Q 矩阵，但相关评估只聚焦于"最终矩阵与参考矩阵的重叠度"，从不检验"每一个题目-属性赋值背后的依据是什么"。这种黑箱式生成难以用于高利害的教育测量场景。

### 研究动机
为在测量领域负责任地使用 AI，需要一种能揭示 LLM 赋值推理过程的方法，使每个赋值都可被审查、比较与质询。

## 研究问题

### 核心研究问题
如何让 LLM 生成的 Q 矩阵不仅输出最终结果，还能提供每个题目-属性赋值的可审查推理依据，从而补充专家审查与经验验证？

## 方法概述

### 核心思想
RTA 的核心是"从最终矩阵转向推理轨迹"：让 LLM 通过 CoT 提示，在生成 Q 矩阵的同时输出带属性标签的推理步骤；随后对这些步骤进行结构化分析，重建、比较并审查每一个题目-属性赋值。

![[rta_framework.png|600]]

> 图：RTA 框架——LLM 经 CoT 生成带属性标签的推理步骤，这些步骤被分析以重建、比较并审查每个赋值，为 Q 矩阵构建提供可解释证据。

### 方法框架

#### 整体架构
- **CoT 生成**：对每道题目，LLM 输出逐条推理步骤，每条步骤标注其涉及的属性。
- **轨迹分析**：从推理步骤中重建 Q 矩阵，并与直接生成的 Q 矩阵、参考 Q 矩阵对比。
- **分歧定位**：审查轨迹中与参考不一致之处，定位到题目类型编码、标签规则、运算表达方式等源头。

#### 两种生成模式对比
- **直接生成**：LLM 直接输出最终 Q 矩阵（黑箱）。
- **CoT 派生**：LLM 输出推理步骤，再从步骤重建 Q 矩阵（可解释）。

## 实验结果

### 实验目标
验证 RTA 能否恢复所需属性、抑制无关属性，并揭示 CoT 派生矩阵与直接生成矩阵在重叠度、稳定性与拟合度上的差异。

### 数据集与设置
- **模拟研究**：扰动分数减法题目，检验四个 LLM 是否恢复所需属性并抑制不需要的属性。
- **经验研究**：11 道分数减法题，536 名受试者响应，4 个 LLM，最多 3 个温度，每条件 100 次重复。

### 主要结果

![[empirical_workflow_performance.png|600]]

> 图：经验研究中 CoT 派生 Q 矩阵与参考矩阵的重叠度及拟合表现。

- **重叠度**：CoT 派生 Q 矩阵与参考矩阵平均重叠 0.809，低于直接生成的 0.877。
- **稳定性**：CoT 派生矩阵跨重复变异更大。
- **属性恢复**：模拟研究中，四个 LLM 均能恢复所需属性、抑制题目不需要的属性。
- **拟合差异**：重叠度相似的 Q 矩阵，在拟合受试者响应上仍有差异——说明"矩阵级一致性"与"拟合度"并不等价。

### 分歧分析
轨迹审查将分歧关联到三类源头：题目类型编码、标签规则、运算如何被表达为步骤。

## 深度分析

### 研究价值评估

#### 理论贡献
- **可解释测量生成**：把"黑箱 Q 矩阵生成"转化为"可审查的推理轨迹分析"，为 AI 辅助测量引入中间证据层。
- **区分一致性与拟合度**：实证表明矩阵重叠度与响应拟合度是两个维度，单纯追求高重叠可能误导。

#### 实际应用价值
- **负责任的 AI 测量**：RTA 可补充专家审查，让教育测量中的 LLM 使用更透明、可审计。
- **质量诊断工具**：通过轨迹定位分歧源头，指导提示工程与标签规则改进。

### 方法优势详解
- **可解释性**：每个赋值都有据可查，从"信不信"变为"看得见"。
- **诊断能力**：能把分歧定位到具体的编码/标签/步骤表达问题，而非笼统的"不一致"。
- **补充定位明确**：RTA 明确作为专家审查与经验验证的补充，而非替代，定位审慎。

### 局限性分析
- **重叠度反而更低**：CoT 派生矩阵的重叠度（0.809）低于直接生成（0.877），可解释性以一定精度为代价。
- **跨重复变异大**：CoT 派生的稳定性差于直接生成，需要多次重复或温度控制。
- **任务单一**：仅以分数减法（小学数学）为对象，向更复杂、多属性、主观性的测量任务泛化未知。

### 适用性与场景分析
- **适用场景**：高利害教育测量中需要人工审查 LLM 生成的 Q 矩阵；提示设计、标签规则的诊断迭代。
- **不适用场景**：对重叠度/精度要求极致、且无需解释的批量低利害场景。

## 与相关论文对比

### 对比论文选择依据
- 直接生成 Q 矩阵的 LLM 工作（作为基线）与传统专家构建（作为参照）。

| 对比维度 | 直接生成 Q 矩阵 | 本文（RTA / CoT 派生） |
|----------|----------------|--------------------------|
| 可解释性 | 黑箱，只有最终矩阵 | 有逐条推理轨迹可审查 |
| 重叠度 | 0.877（更高） | 0.809（略低） |
| 稳定性 | 更稳定 | 跨重复变异更大 |
| 关系类型 | 基线 | 改进（引入可解释证据层） |

## 技术路线定位

### 所属技术路线
LLM 在教育/心理测量中的应用（LLM for Measurement），具体为**Q 矩阵自动构建的可解释性**方向，属于"可解释 AI（XAI）× 测量学"的交叉工作。
