---
date: "2026-09-05"
paper_id: "arXiv:2609.04013"
title: "LLM4CKD: Large Language Models for Early Stage Chronic Kidney Disease Screening"
authors: "Muhammad Ashad Kabir, Sirajam Munira"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 医疗AI
  - 慢病筛查
  - 上下文学习
  - 表格基础模型
quality_score: "7.8/10"
created: "2026-09-05"
updated: "2026-09-05"
status: analyzed
---

# LLM4CKD: Large Language Models for Early Stage Chronic Kidney Disease Screening

## 核心信息

- **论文ID**：arXiv:2609.04013
- **作者**：Muhammad Ashad Kabir, Sirajam Munira
- **机构**：Charles Sturt University, NSW, Australia
- **发布时间**：2026-09-03
- **会议/期刊**：arXiv 预印本（cs.AI / cs.LG）
- **链接**：[arXiv](http://arxiv.org/abs/2609.04013v1) | [PDF](https://arxiv.org/pdf/2609.04013v1)
- **引用**：--

## 摘要翻译

### 英文摘要

Early screening of chronic kidney disease (CKD) is critical for timely intervention, yet most machine learning (ML) and deep learning (DL) approaches require labeled data and model training, limiting their use in real-world screening settings. This study evaluates the effectiveness of large language models (LLMs) for CKD screening under zero-shot and few-shot in-context learning settings and compares them with traditional ML and DL methods. We propose a framework that uses clinically selected tabular features and structured prompt templates to enable LLM-based inference without task-specific training. LLM performance is evaluated across multiple prompt styles, feature configurations, and data settings, and compared with standard ML, DL, and tabular foundation model (TFM) baselines, and existing CKD screening tools. The results show that LLMs can achieve competitive performance using only a small number of examples, often matching or outperforming traditional approaches in low-data settings. However, their performance remains model-dependent and less stable as input complexity increases. In contrast, ML, DL, and TFM models show more consistent improvement with larger training data. Overall, the findings highlight a trade-off between data efficiency and stability, suggesting that LLMs may serve as a flexible complementary approach for CKD screening when labeled data are limited.

### 中文翻译

慢性肾病（CKD）的早期筛查对于及时干预至关重要，然而大多数机器学习（ML）与深度学习（DL）方法都需要标注数据与模型训练，限制了它们在真实筛查场景中的使用。本研究评估了大语言模型（LLM）在零样本与少样本上下文学习设置下进行 CKD 筛查的有效性，并与传统 ML 和 DL 方法进行对比。作者提出了一个框架，使用临床选定的表格特征与结构化提示模板，在无需任务特定训练的情况下实现基于 LLM 的推理。研究在多种提示风格、特征配置与数据设置下评估了 LLM 性能，并与标准 ML、DL、表格基础模型（TFM）基线及现有 CKD 筛查工具对比。结果表明，LLM 仅用少量样本即可取得有竞争力的性能，在低数据场景下常能匹配甚至超越传统方法；但其性能依赖具体模型，且随输入复杂度增加而稳定性下降。相比之下，ML、DL 与 TFM 模型在训练数据增多时表现出更一致的提升。总体而言，研究揭示了**数据效率与稳定性之间的权衡**，表明在标注数据有限时，LLM 可作为 CKD 筛查的灵活补充方法。

### 核心要点提炼

- **研究背景**：CKD 早期筛查关键，但传统 ML/DL 需要标注数据与训练，落地受限。
- **研究动机**：LLM 的零样本 / 少样本上下文学习能力能否绕过"训练"这一前提？
- **核心方法**：临床选定表格特征 + 结构化提示模板，实现无任务训练的 LLM 推理。
- **主要结果**：低数据场景下 LLM 可匹配甚至超越传统方法，但稳定性与模型依赖性强。
- **研究意义**：揭示数据效率与稳定性权衡，LLM 可作为低数据下的补充筛查工具。

## 研究背景与动机

### 领域现状

CKD 的早期筛查能显著改善预后，但传统筛查依赖实验室指标与医生判断。ML / DL 方法在 CKD 风险预测上表现良好，但普遍需要大量标注数据与任务特定训练，阻碍了其在数据稀缺的基层场景中快速部署。

### 现有方法的局限性

- **数据依赖**：传统 ML / DL 需要标注数据与训练，低资源场景难落地。
- **灵活性差**：针对特定人群 / 指标训练好的模型难以快速迁移。
- **表格数据建模**：表格数据是医疗筛查的常见形态，但通用 LLM 对表格数值推理的稳定性存疑。

### 研究动机

LLM 的上下文学习（in-context learning）能力使其无需梯度更新即可适配新任务。若能证明 LLM 在零样本 / 少样本下即可胜任 CKD 筛查，就能为"低数据、无训练"的筛查场景提供一个灵活的补充方案。

## 研究问题

### 核心研究问题

1. LLM 在零样本 / 少样本上下文学习下做 CKD 筛查，性能能否匹配或超越传统 ML / DL？
2. 提示风格、特征配置与数据设置如何影响 LLM 的筛查表现？
3. LLM 方案与 ML / DL / TFM 方案在"数据效率 vs 稳定性"上如何权衡？

## 方法概述

### 核心思想

**LLM4CKD 框架**：将 CKD 筛查建模为基于结构化提示的推理任务——把临床选定的表格特征组织成提示模板，让 LLM 在零样本或少样本下直接给出筛查结果，从而绕开"任务特定训练"这一前提。

![[LLM4CKD-overview.png|600]]

> 图：LLM4CKD 框架概览——临床选定表格特征 → 结构化提示模板 → LLM 零样本 / 少样本推理 → 筛查结果，并与 ML / DL / TFM 基线对比。

### 方法框架

#### 整体架构

```
 临床表格特征 ──► 结构化提示模板 ──► LLM（零样本/少样本上下文学习）──► CKD 筛查结果
                                      │
                        对比：ML / DL / TFM / 现有筛查工具
```

#### 各模块详细说明

**模块1：临床特征选择**

- **功能**：从 CKD 相关指标中选定一组临床上有意义的表格特征作为输入。
- **意义**：特征选择直接影响提示质量与 LLM 推理的稳定性。

**模块2：结构化提示模板**

- **功能**：将表格特征组织成 LLM 可理解的提示（多种提示风格）。
- **多样式**：研究对比了不同提示风格，考察其对性能的影响。

**模块3：零样本 / 少样本推理**

- **功能**：LLM 无需训练，直接基于提示与少量示例做筛查判断。
- **对比**：与标准 ML、DL、表格基础模型（TFM）及现有筛查工具对比。

## 实验结果

### 实验目标

评估 LLM 在零样本 / 少样本下的 CKD 筛查性能，并与传统方法对比，剖析数据效率与稳定性权衡。

### 数据集与基线

- **任务**：早期 CKD 筛查（二分类 / 风险分层）。
- **基线**：标准 ML、DL、表格基础模型（TFM）、现有 CKD 筛查工具。
- **评估维度**：性能、数据效率、稳定性、模型依赖性。

### 主要结果

| 维度 | 结果 |
|------|------|
| 低数据场景 | LLM 可匹配甚至**超越**传统 ML/DL 方法 |
| 高数据场景 | ML/DL/TFM 随数据增加表现**更一致** |
| 稳定性 | LLM 随输入复杂度增加**稳定性下降** |
| 模型依赖性 | LLM 性能**依赖具体模型** |

![[mlp_pairwise_corr_spearman-v2_page1.png|600]]

> 图：MLP 特征两两 Spearman 相关分析，考察特征间相关性对 LLM 筛查稳定性的影响。

## 深度分析

### 研究价值评估

#### 理论贡献

- **实证权衡刻画**：系统揭示了 LLM 在医疗表格筛查中"数据效率 vs 稳定性"的权衡。
- **框架设计**：提出结构化提示 + 临床特征选择的 LLM 筛查范式。

#### 实际应用价值

- **低数据落地**：在标注稀缺的基层 / 早期场景，LLM 可作为无训练的快速筛查补充。
- **灵活性**：无需针对特定人群重新训练，提示即可适配。

### 方法优势详解

- **零训练部署**：无需梯度更新，即可对新任务做推理。
- **低数据高效**：少量示例即可取得竞争力性能。
- **补充定位清晰**：明确定位为"标注有限时的灵活补充"，而非全面替代。

### 局限性分析

- **稳定性不足**：输入复杂度增加时性能波动，临床部署需谨慎。
- **模型依赖**：不同 LLM 差异明显，缺乏统一保证。
- **可靠性风险**：医疗场景对误判代价敏感，LLM 的幻觉与不稳定性是隐患。

## 我的综合评价

### 价值评分

#### 总体评分

**7.8/10** — 实证扎实、定位务实，为 LLM 在医疗表格筛查中的适用边界提供了有价值的经验证据。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 框架设计合理，但核心是实证评估而非方法创新 |
| 技术质量 | 8/10 | 多提示风格 / 特征配置 / 数据设置系统对比 |
| 实验充分性 | 8/10 | 基线覆盖 ML/DL/TFM/现有工具 |
| 写作质量 | 8/10 | 结论清晰 |
| 实用性 | 8/10 | 低资源医疗场景有直接参考价值 |

## 相关论文

- 相关技术路线：LLM 医疗应用、表格基础模型（TFM）、上下文学习、临床决策支持

> [!tip] 关键启示
> LLM 在医疗表格筛查中的真正优势是**数据效率**而非绝对性能——在标注稀缺时作为无训练的补充，而非替代训练良好的专用模型。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐给关注 LLM 医疗落地与表格数据上下文学习的研究者。
