---
date: "2026-09-21"
paper_id: "arXiv:2609.21888"
title: "Detecting Pretraining Data in Large Language Models from a Free-Energy Perspective"
authors: "Chenye Ke, Zirui Liu, Qi Liu, Yan Zhuang, Jintao Zhang, Zhenya Huang, Shijin Wang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Membership-Inference
  - Pretraining-Data-Detection
  - Free-Energy
quality_score: "8.6/10"
created: "2026-09-21"
updated: "2026-09-21"
status: analyzed
---

# Detecting Pretraining Data in Large Language Models from a Free-Energy Perspective

## 核心信息
- **论文ID**：arXiv:2609.21888
- **作者**：Chenye Ke, Zirui Liu, Qi Liu（通讯）, Yan Zhuang, Jintao Zhang, Zhenya Huang, Shijin Wang
- **机构**：中国科学技术大学（认知智能国家重点实验室）、合肥综合性国家科学中心人工智能研究院、南京航空航天大学、科大讯飞
- **发布时间**：2026-09-18
- **会议/期刊**：arXiv 预印本
- **链接**：[arXiv](https://arxiv.org/abs/2609.21888) | [PDF](https://arxiv.org/pdf/2609.21888)
- **代码**：https://github.com/kecy03/ETD

## 摘要翻译

### 英文摘要
Detecting pretraining data in large language models is challenging because high likelihood can reflect either training exposure or strong generalization. In the joint space of prediction loss and predictive entropy, a likelihood-only detector uses a horizontal boundary and can mistake predictable non-members for members. The authors introduce an inclined boundary that evaluates prediction loss relative to predictive entropy, showing entropy correction preserves the expected membership signal while reducing its variance. This entropy-adjusted score admits a Helmholtz free-energy interpretation, leading to Energy Transfer Detection (ETD).

### 中文翻译
检测大语言模型的预训练数据具有挑战性，因为高似然既可能反映训练暴露，也可能反映强泛化。在预测损失与预测熵的联合空间中，仅依赖似然的检测器使用水平边界，容易将"可预测的非成员"误判为成员。作者引入一条倾斜边界，将预测损失相对于预测熵进行评估，并证明熵校正能在保留期望成员信号的同时降低其方差。这一熵校正分数具有亥姆霍兹自由能解释，由此提出"能量转移检测"（Energy Transfer Detection, ETD）。

### 核心要点提炼
- **研究背景**：LLM 预训练语料规模与来源难以审计，隐私、版权与 benchmark 污染问题突出。
- **研究动机**：似然只反映"预测得好"，无法区分"背过"与"泛化得好"。
- **核心方法**：用预测熵校正预测损失，构造倾斜边界，从自由能视角提出 ETD。
- **主要结果**：平均 AUROC 最高提升 3.5%，TPR@5%FPR 最高提升 5.1%，跨模型稳健。

## 研究背景与动机

### 领域现状
成员推理（Membership Inference / Pretraining Data Detection）是审计 LLM 预训练语料的关键手段。现有灰盒方法主要遵循"似然打分"范式：模型对训练过的文本通常赋予更高似然（PPL、Min-K% 等）。但自然语言存在大量重复与相似表达，模型可能仅凭泛化能力就对未见文本做出高似然预测。

### 现有方法的局限性
仅依赖似然的检测器使用**水平阈值**：低损失即判为成员。这会把"低损失、但并非训练数据"的可预测文本误判为成员，造成假阳性。

### 研究动机
作者观察到：在"预测损失—预测熵"联合空间中，随着训练暴露增加，成员文本沿对角方向移动，而非成员文本虽损失低但熵特征不同。因此，引入预测熵作为第二维，用**倾斜边界**区分两者。

## 研究问题

### 核心研究问题
如何用预测熵校正预测损失，从而在不牺牲成员信号的前提下，降低方差、提升成员—非成员的标准化可分性，并将其形式化为可解释的自由能框架？

## 方法概述

### 核心思想
将"检测预训练数据"类比为热力学中的"自由能转移"：成员文本在自回归预测过程中释放的残余自由能贡献应高于非成员文本。通过"损失—熵"联合打分的倾斜边界实现更清晰的分离。

### 方法框架

#### 整体架构

![[2609.21888_fig1.png|800]]

> 图1：动机示意。左：成员与非成员文本的 token 级损失分布可能重叠；右：训练过程中仅靠水平似然边界会误判可预测的非成员，而引入预测熵的倾斜边界能提供更清晰的分离。

**三个关键步骤**：
1. **熵校正打分**：在预测损失基础上减去预测熵项，构造倾斜边界。
2. **均值—方差分析**：证明熵校正可保留期望成员信号、同时降低方差，并推广到非零均值熵差的一般情形。
3. **自由能诠释**：分数代数形式恰为亥姆霍兹自由能，提出 ETD（能量转移检测）。

**模块1：熵校正分数构造**
- **功能**：对每个 token 计算预测损失与预测熵的联合贡献。
- **关键技术**：$s_j = \ell_j - \beta\, h_j$（损失减去熵的加权），形成倾斜判定面。

**模块2：自由能诠释**
- **功能**：将分数解释为"残余自由能转移"。
- **数学形式**：分数与统计热力学中平衡内能与熵的亥姆霍兹自由能 $F = U - TS$ 同构。

**模块3：ETD 聚合**
- **功能**：只在首次出现位置（first-occurrence）聚合贡献，得到序列级打分 $S_{\text{ETD}}(x)$，与阈值 $\tau$ 比较判定成员/非成员。

## 实验结果

### 数据集与模型
- **基准**：WikiMIA、StackMIA$_{sub}$、MIMIR
- **模型**：GPT-Neo-2.7B、Mamba-2.8B、OPT-6.7B、Pythia-12B、GPT-NeoX-20B（MIMIR 用 Pythia 家族 160M–12B）
- **基线**：7 个代表性灰盒方法（PPL、Min-K% 等）

### 主要结果
- **AUROC**：在 WikiMIA 与 StackMIA$_{sub}$ 上取得最佳平均 AUROC，StackMIA$_{sub}$ 上超过最强基线 3.5% 与 3.0%。
- **TPR@5%FPR**：同样最佳，WikiMIA 上 GPT-NeoX-20B 单模型增益高达 7.7% 与 8.7%；StackMIA$_{sub}$ 最大单模型增益 4.4%（GPT-NeoX-20B）与 2.2%（OPT-6.7B，改写设置）。
- **稳健性**：在改写（paraphrased）设置下依然稳健。

### 消融实验
在 GPT-NeoX 上进行组件消融，验证熵校正项与首次出现聚合等组件对 AUROC 的贡献。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：首次从自由能视角统一"似然 + 熵"的成员推理打分，给出均值—方差统计依据。
  - 创新点：把检测问题从"阈值调参"提升到"几何 + 热力学可解释框架"。
- **贡献2**：熵校正"保信号、降方差"的结论，为后续检测器的方差分析提供模板。

#### 实际应用价值
- **应用场景1**：LLM 预训练语料的隐私与版权审计。
  - 优势：无需训练参考模型，灰盒设置即可用。
- **应用场景2**：下游 benchmark 污染（data contamination）检测。
  - 优势：对可预测的污染文本更稳健，降低假阳性。

### 方法优势详解
- **优势1（可解释性）**：自由能诠释让检测分数有物理直觉，便于审慎解读结果。
- **优势2（稳健性）**：跨 GPT-Neo/Mamba/OPT/Pythia 多个架构与规模均取得最佳平均性能。

### 局限性分析
- **局限1（灰盒假设）**：需要访问模型输出 logits，对纯 API（仅返回文本）模型不可直接使用。
- **局限2（依赖熵估计）**：熵估计质量影响校正效果，长序列上计算成本上升。

## 我的综合评价

### 总体评分
**8.6/10** — 理论动机清晰、自由能诠释优雅、跨模型实验充分，是成员推理方向一篇兼具洞察与实用的工作。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 自由能视角 + 熵校正边界新颖 |
| 技术质量 | 9/10 | 均值—方差分析严谨 |
| 实验充分性 | 8/10 | 三基准、五架构、消融完备 |
| 写作质量 | 9/10 | 逻辑清晰、图示直观 |
| 实用性 | 8/10 | 隐私/版权/污染审计直接可用 |

> [!tip] 关键启示
> "预测得好"不等于"训练过"——用熵这一维去区分泛化与记忆，是成员推理迈向稳健检测的关键一步。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐：自由能视角是成员推理方向值得关注的范式，理论+实验俱佳。
