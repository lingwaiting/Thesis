---
date: "2026-09-03"
paper_id: "arXiv:2609.01832"
title: "Interpretable Symptom Vectors for Depression in a Large Language Model"
authors: "Fangyi Zhu, Ajay Subramanian, Allison Constant, Camille Wang, Ravish Gupta, Corey J. Keller"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 可解释性
  - 机制可解释性
  - Mechanistic-Interpretability
  - 医疗健康
  - 抑郁检测
quality_score: "7.5/10"
created: "2026-09-03"
updated: "2026-09-03"
status: analyzed
---

# Interpretable Symptom Vectors for Depression in a Large Language Model

## 核心信息
- **论文ID**：arXiv:2609.01832
- **作者**：Fangyi Zhu, Ajay Subramanian, Allison Constant, Camille Wang, Ravish Gupta, Corey J. Keller
- **机构**：--
- **发布时间**：2026-09-01
- **会议/期刊**：--（arXiv 预印本，cs.CL / cs.AI / cs.LG / q-bio.NC）
- **链接**：[arXiv](http://arxiv.org/abs/2609.01832v1) | [PDF](https://arxiv.org/pdf/2609.01832v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Patients with depression present with diverse symptom profiles, yet clinical practice routinely reduces this variation to a single severity score. Large language models (LLMs) can potentially capture various symptoms and their severity from patient speech. However, how depressive symptoms are represented inside LLMs remains poorly understood, limiting clinical trust. To examine whether internal model activations match clinician judgment, we analyzed the residual stream of Gemma-3-27B-PT using mechanistic interpretability techniques. Recording activations across symptom descriptions drawn from validated clinical instruments, we found that symptom groups geometrically separated the most at layer 21 across multiple distance metrics. Using Semantic Projection, we then projected held-out naturalistic text onto Symptom Vectors constructed from these instruments. The resulting per-symptom coefficients preserved clinician-annotated rank ordering across mood, somatic, and suicidality axes. Furthermore, a single depression vector in Layer 21 separates held-out depressive from non-depressive text (AUC = 0.789), which can be used as an emotional valence gate that restricts symptom projection to depressive speech. These results reveal a decorrelated, clinician-aligned symptom signal readable directly from internal activations, offering a mechanistic foundation for interpretable depression-assessment tools.

### 中文翻译
抑郁症患者表现出多样化的症状谱系，然而临床实践通常将这种差异简化为单一的严重程度评分。大语言模型（LLM）有望从患者言语中捕捉各种症状及其严重程度，但抑郁症状如何在 LLM 内部被表征仍不清楚，这限制了临床信任。为检验模型内部激活是否与临床判断一致，作者使用机制可解释性技术分析了 Gemma-3-27B-PT 的残差流。通过记录来自经验证临床量表的症状描述所对应的激活，作者发现：在多种距离度量下，症状群在第 21 层几何分离程度最高。随后，作者使用 Semantic Projection，将留出的自然语言文本投影到由这些量表构建的 Symptom Vectors 上，得到的逐症状系数在情绪（mood）、躯体（somatic）与自杀意念（suicidality）轴上保持了临床医生标注的等级排序。此外，第 21 层的单一抑郁向量可将留出的抑郁文本与非抑郁文本区分开（AUC = 0.789），可作为"情绪效价门控"来限制症状投影仅作用于抑郁言语。这些结果揭示了一个去相关、与临床对齐的、可直接从内部激活中读取的症状信号，为可解释的抑郁评估工具提供了机制基础。

### 核心要点提炼
- **研究背景**：临床把抑郁症状简化为单一评分，LLM 内部如何表征症状未知。
- **研究动机**：检验 LLM 内部激活是否与临床判断对齐，从而提升临床信任。
- **核心方法**：机制可解释性 + Semantic Projection，在 Gemma-3-27B-PT 残差流中提取 Symptom Vectors。
- **主要结果**：症状群在第 21 层几何分离最明显；单一抑郁向量区分抑郁/非抑郁文本 AUC=0.789。
- **研究意义**：为可解释、机制驱动的抑郁评估工具奠定基础。

## 研究背景与动机

### 领域现状
LLM 在医疗健康领域展现出潜力，但"黑盒"性质限制了临床信任。机制可解释性（mechanistic interpretability）为理解模型内部表征提供了工具，但将其应用于临床症状维度仍属前沿。

### 现有方法的局限性
1. **症状被压缩**：临床将多样症状压缩为单一严重度分数，丢失信息。
2. **表征不透明**：LLM 内部如何编码抑郁症状不清晰。
3. **缺乏机制基础**：现有可解释评估工具缺乏对模型内部激活的机制性理解。

### 研究动机
作者希望检验 LLM 内部激活是否与临床判断对齐，从而为可解释抑郁评估提供机制依据。

## 研究问题

### 核心研究问题
抑郁症状是否以几何可分、与临床对齐的方式编码在 LLM 内部激活中？能否提取可解释的"Symptom Vectors"来区分抑郁/非抑郁文本？

## 方法概述

### 核心思想
在 Gemma-3-27B-PT 的残差流中，利用临床量表（clinical instruments）构建症状描述，通过机制可解释性技术定位症状表征所在的层，再用 Semantic Projection 提取逐症状向量，检验其是否保持临床等级排序。

### 方法框架

#### 整体架构
![[1.pipeline_page1.png|800]]

> 图1：方法流水线——从临床量表症状描述 → 残差流激活 → Symptom Vectors → Semantic Projection 系数。

![[2.separation_page1.png|600]]

> 图2：症状群几何分离分析，展示不同层上的分离程度（第 21 层最高）。

#### 各模块详细说明

**模块1：症状表征定位**
- 在 Gemma-3-27B-PT 各层记录症状描述对应的激活。
- 用多种距离度量计算症状群几何分离度，定位最优层（第 21 层）。

**模块2：Semantic Projection**
- 将留出的自然语言文本投影到由临床量表构建的 Symptom Vectors 上。
- 得到逐症状系数，检验 mood / somatic / suicidality 轴的等级排序。

**模块3：情绪效价门控**
- 单一抑郁向量（第 21 层）区分抑郁/非抑郁文本（AUC=0.789）。
- 作为 valence gate，限制症状投影仅作用于抑郁言语。

## 实验结果

### 数据集
经验证的临床量表（validated clinical instruments）构建症状描述 + 留出的自然语言文本。

### 主要结果
- 症状群在**第 21 层**几何分离程度最高（多距离度量一致）。
- Semantic Projection 得到的逐症状系数保持了临床医生标注的**等级排序**（mood、somatic、suicidality）。
- 单一抑郁向量区分抑郁/非抑郁文本 **AUC = 0.789**。

![[3.coefficient_abc_page1.png|600]]

![[3.coefficient_d_page1.png|600]]

> 图3：逐症状投影系数，展示与临床等级排序的对齐情况。

![[4.subspace_page1.png|600]]

> 图4：子空间分析，揭示去相关的症状信号结构。

![[5.contrastive_page1.png|600]]

> 图5：对比/对照实验，验证抑郁向量的区分能力。

## 深度分析

### 研究价值评估
- **理论贡献**：证明抑郁症状以"去相关、与临床对齐"的信号编码在 LLM 残差流中，为机制可解释性应用于临床开辟路径。
- **实际应用**：可解释抑郁评估工具、症状级（而非单一分数）诊断。

### 方法优势
1. **机制驱动**：直接定位到具体层（第 21 层）与可读向量。
2. **临床对齐**：等级排序与临床医生标注一致，增强信任。
3. **可解释性强**：Symptom Vectors 可读、可解释。

### 局限性
- 单一模型（Gemma-3-27B-PT），跨模型泛化性待验证。
- AUC=0.789 中等，距离临床部署仍有差距。
- 数据集规模与真实患者语料的覆盖未充分说明。

## 技术路线定位
本文属于 **LLM 可解释性 × 医疗健康** 交叉路线，与线性表征假设（linear representation hypothesis）、Semantic Projection 等工作一脉相承，特色在于将机制可解释性工具用于临床症状维度。

## 未来工作建议
1. 在更多模型家族上验证 Symptom Vectors 的稳健性。
2. 用真实临床对话语料（而非量表文本）验证泛化。
3. 探索多症状协同建模，构建可解释的症状级诊断系统。

## 我的综合评价

### 价值评分
**7.5/10** — 方向新颖、机制扎实，但临床落地仍有距离，需跨模型与真实数据验证。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 机制可解释性 × 临床症状，交叉新颖 |
| 技术质量 | 8/10 | Semantic Projection 方法严谨 |
| 实验充分性 | 6/10 | 单模型、AUC 中等 |
| 写作质量 | 8/10 | 清晰 |
| 实用性 | 7/10 | 临床价值高但落地尚早 |

> [!tip] 关键启示
> LLM 内部确实存在"去相关、与临床对齐"的症状信号，可解释性技术能为临床 AI 提供机制信任基础——这比单一黑盒分数更有价值。

> [!success] 推荐指数
> ⭐⭐⭐ 值得关注——可解释性 × 医疗健康交叉方向，机制分析视角有启发性。
