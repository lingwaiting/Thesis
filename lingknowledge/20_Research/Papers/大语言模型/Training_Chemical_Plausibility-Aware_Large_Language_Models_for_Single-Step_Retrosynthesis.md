---
date: "2026-08-21"
paper_id: "arXiv:2608.18940"
title: "Training Chemical Plausibility-Aware Large Language Models for Single-Step Retrosynthesis"
authors: "Bogdan Zagribelnyy, Ivan Ilin, Nikita Bondarev, Maksim Kuznetsov, Mathieu Reymond, Vladimir Aladinskiy, Alex Aliper, Alex Zhavoronkov"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM
  - 化学
  - Retrosynthesis
  - 科学AI
quality_score: "8.0/10"
related_papers: []
created: "2026-08-21"
updated: "2026-08-21"
status: analyzed
---

# Training Chemical Plausibility-Aware Large Language Models for Single-Step Retrosynthesis

## 核心信息
- **论文ID**：arXiv:2608.18940
- **作者**：Bogdan Zagribelnyy, Ivan Ilin, Nikita Bondarev, Maksim Kuznetsov, Mathieu Reymond, Vladimir Aladinskiy, Alex Aliper, Alex Zhavoronkov
- **机构**：--（作者多来自 AI 制药/化学信息学团队）
- **发布时间**：2026-08-19
- **会议/期刊**：arXiv（cs.LG / cs.AI / cs.CE / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2608.18940) | [PDF](https://arxiv.org/pdf/2608.18940)
- **引用**：--

## 摘要翻译

### 英文摘要
Single-step retrosynthesis is a central component of computer-aided synthesis planning, yet its intrinsically one-to-many nature is poorly captured by single-answer evaluation and benchmarking protocols. To address this, we introduce Top-K prompting as a robust training and inference paradigm to better capture diverse, plausible reaction predictions. We compile CREED-CCV-2+USPTO-XL, an ultra-large-scale dataset of ~45.6 million verified reactions to train the C3LM (Chemistry Constraint-Consistent Language Model). By integrating fine-tuning with ChemCensor-based and novelty-oriented rewards, our model achieves state-of-the-art performance on the OOD URSA-expert-2026 benchmark. Further analysis of reaction uniqueness shows that LLMs and conventional models explore complementary reaction spaces, motivating ensemble-based retrosynthesis systems.

### 中文翻译
单步逆合成是计算机辅助合成规划的核心环节，但其"一对多"的本质特性，与当前单一答案的评估和基准协议存在错配。为此，本文引入 **Top-K 提示（Top-K prompting）** 作为稳健的训练与推理范式，以更好地刻画多样、合理的反应预测。作者构建了超大规模数据集 CREED-CCV-2+USPTO-XL（约 4560 万个已验证反应）来训练 C3LM（化学约束一致性语言模型）。通过将微调与基于 ChemCensor 和新颖性导向的奖励相结合，该模型在 OOD 的 URSA-expert-2026 基准上取得最先进性能。对反应唯一性的进一步分析表明，LLM 与常规模型探索的是互补的反应空间，这为基于集成的逆合成系统提供了依据。

### 核心要点提炼
- **研究背景**：逆合成是"一对多"问题，但当前评估只看单一答案，低估了模型价值。
- **研究动机**：需要一种能刻画多样化合理反应的训练与评估范式。
- **核心方法**：Top-K 提示 + 超大规模反应数据 + 化学约束一致性训练（ChemCensor + 新颖性奖励）。
- **主要结果**：在 OOD URSA-expert-2026 上取得 SOTA。
- **研究意义**：确立了"Top-K、合理感知"训练作为 LLM 合成规划的新方向。

## 研究背景与动机

### 领域现状
计算机辅助合成规划（CASP）依赖逆合成（retrosynthesis）将目标分子逆向拆解为可购原料。单步逆合成是其中的基础模块，深度学习模型（模板、图、Transformer、LLM）已广泛使用。

### 现有方法的局限性
- **一对多被忽略**：一个目标分子常有多种合法反应路径，但单一答案评估只奖励"命中参考答案"，压制了模型的多样性。
- **评估协议错配**：现有基准衡量的是"命中率"，而非"是否给出化学上合理的一组合成路线"。
- **数据规模不足**：训练数据的覆盖度与验证质量限制了泛化。

### 研究动机
逆合成的价值在于**给出多样且化学合理的候选**，供下游规划筛选。因此需要用 Top-K 范式重新对齐训练目标与评估方式。

## 研究问题

### 核心研究问题
如何训练一个能**感知化学合理性、并输出多样化 Top-K 候选**的单步逆合成 LLM，使其在分布外（OOD）场景下仍保持领先？

## 方法概述

### 核心思想
用 **Top-K 提示**让模型在训练和推理时都显式输出多个候选反应，配合**化学约束一致性（ChemCensor）**与**新颖性**双重奖励，引导模型生成"既合理又多样"的路线。

![[CREED-CCV_page1.png|700]]

> 图：CREED-CCV-2 数据集的规模与构建示意（约 4560 万已验证反应）。

### 方法框架

#### 数据：CREED-CCV-2 + USPTO-XL
- 超大规模：约 **4560 万** 已验证反应，远超常规公开基准。
- 用于训练 C3LM，提供广覆盖与高验证质量。

#### 模型：C3LM（Chemistry Constraint-Consistent Language Model）
- 在反应数据上微调，注入化学约束。
- **ChemCensor 奖励**：对生成路线做化学合理性约束/校验。
- **新颖性导向奖励**：鼓励模型探索参考集之外的新颖合理路线。

#### 范式：Top-K 提示
- 训练与推理统一采用 Top-K 输出，直接对齐"一对多"本质。
- 评估上不再只看单一命中，而看 Top-K 内是否包含合理/正确候选。

## 实验结果

### 实验设置
- **基准**：OOD URSA-expert-2026（专家标注、分布外，考验泛化）。
- **指标**：Top-K 命中、化学合理性等。
- **分析**：反应唯一性（uniqueness）分析，比较 LLM 与常规模型探索的反应空间。

### 主要结果
- C3LM 在 OOD URSA-expert-2026 上取得 **SOTA** 性能。
- 反应唯一性分析表明：LLM 与常规模型探索**互补**的反应空间 → 支撑集成（ensemble）路线。
- Top-K、合理感知训练被证明是稳健的新范式。

## 深度分析

### 研究价值
- **理论贡献**：从"单答案命中"转向"多样合理候选"的评估/训练范式，更贴合逆合成本质。
- **实际应用**：为 CASP 系统提供更强、更稳健的单步逆合成引擎，可直接支撑下游合成规划。
- **领域影响**：推动"AI for Chemistry"从模型竞赛走向数据规模 + 合理感知 + 集成协同。

### 方法优势
1. **范式对齐**：Top-K 直击一对多本质，避免多样性被评估协议压制。
2. **数据规模**：4560 万反应带来强泛化，OOD 表现突出。
3. **合理感知**：ChemCensor + 新颖性奖励让生成既合理又多样。

### 局限性
- 单步逆合成仍是"单步"，多步规划的端到端价值需进一步验证。
- 大规模数据与训练成本高，可复现门槛不低。
- 集成（ensemble）方向被提出但未充分展开。

## 我的综合评价

### 价值评分
- **综合评分**：**8.0/10**
- **分项评分**：
  - 创新性：7/10（Top-K 范式 + 合理感知训练，针对性强但非全新概念）
  - 技术质量：8/10（数据、奖励、约束设计扎实）
  - 实验充分性：8/10（OOD 基准 + 互补性分析）
  - 写作质量：8/10
  - 实用性：8/10（制药/CASP 直接价值）

### 突出亮点
- Top-K 范式重新对齐了逆合成"一对多"本质与评估方式。
- 4560 万反应数据的规模带来 OOD 泛化优势。
- "LLM 与常规模型互补"的发现为集成系统指明方向。

### 重点关注
- 化学约束（ChemCensor）的具体实现与可迁移性；集成系统的构建。

## 相关论文
- 待补充

## 外部资源
- [arXiv](https://arxiv.org/abs/2608.18940) | [PDF](https://arxiv.org/pdf/2608.18940)

> [!tip] 关键启示
> 对"一对多"问题，训练与评估范式必须显式拥抱多样性（Top-K），而非用单答案指标去压制它。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐给关注 AI for Science / 化学信息学的读者。
