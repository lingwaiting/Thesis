---
date: "2026-07-17"
paper_id: "arXiv:2607.14385"
title: "MamaBench: Benchmarking LLM Robustness in Maternal and Child Health Diagnosis through Counterfactual Clinical Perturbation"
authors: "Thanni Adewuyi, Anuoluwa Sotome, Samuel Okoko, Angel Ezendu, Oluwafunke Akinbuwa, Oluwaseun Odunsi, Oluwasegun Oguntuase, Oluwadarasimi Oguntuase, Ifeoma Nwabueze, Abiodun Adereni"
domain: "大模型"
tags:
  - 论文笔记
  - 大模型
  - 医疗AI
  - 反事实评测
  - RAG
  - 鲁棒性
  - 临床诊断
quality_score: "7.5/10"
created: "2026-07-17"
updated: "2026-07-17"
status: analyzed
---

# MamaBench: Benchmarking LLM Robustness in Maternal and Child Health Diagnosis through Counterfactual Clinical Perturbation

## 核心信息
- **论文ID**：arXiv:2607.14385
- **作者**：Thanni Adewuyi, Anuoluwa Sotome, Samuel Okoko, Angel Ezendu, Oluwafunke Akinbuwa, Oluwaseun Odunsi, Oluwasegun Oguntuase, Oluwadarasimi Oguntuase, Ifeoma Nwabueze, Abiodun Adereni
- **机构**：--
- **发布时间**：2026-07-15
- **分类**：cs.CL, cs.LG
- **链接**：[arXiv](http://arxiv.org/abs/2607.14385v1) | [PDF](https://arxiv.org/pdf/2607.14385v1)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Large language models achieve strong scores on medical benchmarks, yet these benchmarks evaluate each question in isolation, providing no measure of whether a system can distinguish clinically similar presentations requiring different interventions. We introduce MamaBench, the first counterfactual benchmark for maternal and paediatric AI: 434 expert-authored clinical narratives in 217 pairs across 371 pathologies, evaluated via the Bias Trap Rate (BTR), the conditional probability that a model fails the counterfactual given success on the base case. We propose Evidence-Anchored RAG (EA-RAG), a three-stage retrieval method that replaces aggregate similarity with an evidence coverage objective through clinical parameter extraction, coverage auditing, and contrastive sub-queries. Across eight configurations of four frontier LLMs, base accuracy overstates robust accuracy by 16-28 percentage points in every model. EA-RAG achieves 20.3% BTR and 65.0% robust accuracy on Claude Sonnet 4.6, a 5.5 percentage point BTR reduction without degrading base accuracy. The residual 20% BTR confirms that counterfactual robustness in clinical AI remains an open challenge.

### 中文翻译
大语言模型在医学基准上取得了很高的分数，但这些基准是孤立地评估每个问题，无法衡量系统是否能区分临床表现相似但需要不同干预的情况。我们引入了 MamaBench，首个面向母婴和儿科 AI 的反事实基准：434 个由专家撰写的临床叙述组成 217 对反事实配对，覆盖 371 种病理，通过 Bias Trap Rate（BTR）——模型在基础案例成功的前提下在反事实案例上失败的条件概率——进行评估。我们提出了 Evidence-Anchored RAG（EA-RAG），一种三阶段检索方法，通过临床参数提取、覆盖审计和对比子查询，用证据覆盖目标替代聚合相似度。在 4 个前沿 LLM 的 8 种配置中，每个模型的基础准确率都比鲁棒准确率高 16-28 个百分点。EA-RAG 在 Claude Sonnet 4.6 上达到 20.3% BTR 和 65.0% 鲁棒准确率，降低了 5.5 个百分点 BTR，且不降低基础准确率。残存的 20% BTR 证实了临床 AI 中的反事实鲁棒性仍然是一个开放挑战。

### 核心要点提炼
- **研究背景**：LLMs 在医学基准上表现优异，但现有评测无法衡量对临床相似但需不同干预的情况的区分能力
- **研究动机**：医疗 AI 的首要要求是"不造成伤害"——需要确保模型在看似相似但关键的临床差异面前不犯错误
- **核心方法**：MamaBench 反事实基准 + BTR 指标 + EA-RAG 三阶段检索方法
- **主要结果**：所有模型的基础准确率比鲁棒准确率高 16-28pp，EA-RAG 将 BTR 从基线降低 5.5pp
- **研究意义**：揭示了医疗 LLM 评测中的一个根本性盲区——常规准确率严重高估了模型的临床鲁棒性

## 研究背景与动机

### 领域现状
LLMs 在 USMLE、MedQA 等医学考试基准上已达到专家水平，但这些基准存在根本性缺陷：
- **孤立评估**：每个问题独立评估，不考虑临床表现的细微差异
- **无分布偏移测试**：不会测试模型在临床相似但诊断不同的情况下的表现
- **过度自信的指标**：高准确率给人安全的错觉，但无法反映临床实际

### 现有方法的局限性
传统的医疗 AI 评测存在以下盲区：
- 无法区分"模型真的理解病理"还是"模型记住了统计相关性"
- 缺乏对临床决策中"相近但不同"情况的压力测试
- RAG 方法依赖语义相似度检索，可能检索到语义相似但临床无关的内容

### 研究动机
医疗诊断中，临床表现相似但需要完全不同治疗方案的案例非常普遍（如病毒性vs细菌性感染）。如果 LLM 无法区分这些细微差异，在临床部署中将造成严重后果。因此需要一个专门评测这种能力的方法。

## 研究问题

### 核心研究问题
**常规医学基准上的高准确率是否能真实反映 LLM 的临床鲁棒性？如果不能，如何设计更好的评测方法和缓解策略？**

## 方法概述

### 核心思想
MamaBench 通过构造**最小反事实对（minimal counterfactual pairs）**——仅改变少量关键临床细节但导致完全不同诊断的成对病例——来测试模型是否能真正"理解"病理而非依赖表面统计模式。

### 方法框架

#### MamaBench 基准构建
- 434 个专家临床叙述，217 对反事实配对
- 覆盖 371 种母婴/儿科病理
- 每对包含基础案例和反事实案例（仅关键临床特征不同）

![[rag_comparison_diagram.png|800]]

> 图：EA-RAG 与标准 RAG 的对比——EA-RAG 通过临床参数提取、覆盖审计和对比子查询替代聚合相似度检索

#### EA-RAG 方法
三阶段检索：
1. **临床参数提取（Clinical Parameter Extraction）**：从查询中提取结构化的临床参数
2. **覆盖审计（Coverage Auditing）**：检查检索到的证据是否覆盖所有关键临床参数
3. **对比子查询（Contrastive Sub-queries）**：生成对比性子查询，主动寻找区分性证据

### Bias Trap Rate (BTR)
$$\text{BTR} = P(\text{模型在反事实案例上失败} \mid \text{模型在基础案例上成功})$$

这个指标专门捕捉"模型在看似成功的情况下实际存在盲区"的问题。

## 实验与结果

### 主要结果

![[BTR_vs_Robustness.png|800]]

> 图：BTR vs 鲁棒准确率——展示不同方法在 BTR 和鲁棒准确率两个维度上的表现

![[outcome_distribution_page1.png|800]]

> 图：诊断结果分布——展示模型在不同病理类别上的表现差异

关键发现：
- 4 个前沿 LLM 的基础准确率比鲁棒准确率高 16-28pp
- EA-RAG 实现 20.3% BTR 和 65.0% 鲁棒准确率
- BTR 降低 5.5pp 且不损失基础准确率
- 20% 残差 BTR 表明问题远未解决

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：揭示了"准确率幻觉"问题**——首次系统性地量化了医学 LLM 评测中基础准确率与鲁棒准确率之间的差距（16-28pp）
- **贡献2：提出了 BTR 指标**——一种直观且严格的鲁棒性度量，可直接应用于其他医疗 AI 评测
- **贡献3：EA-RAG 方法**——用证据覆盖目标替代语义相似度的检索策略

#### 实际应用价值
- 为医疗 AI 的认证和部署提供了更严格的评测标准
- EA-RAG 可直接集成到现有的临床决策支持系统中
- BTR 可作为医疗 AI 产品的持续监控指标

### 局限性分析
- **领域限定**：目前仅覆盖母婴/儿科，向其他医学领域的扩展需要额外专家标注
- **BTR 的残差**：20% BTR 仍远高于可接受水平，EA-RAG 仅是部分解决方案
- **语言和文化**：临床叙述和反事实设计可能受地域医学实践影响

### 方法优势详解
1. **反事实设计精巧**：最小反事实对确保了评测的严格性
2. **BTR 指标直观**：条件概率的形式易于理解和沟通
3. **EA-RAG 有理论支撑**：证据覆盖目标比语义相似度更符合循证医学原则

## 我的综合评价

### 总体评分
**7.5/10** - 论文提出了一种重要且实用的医疗 AI 评测方法，BTR 指标填补了重要的方法论空白。主要不足在于领域限定较窄和残差 BTR 仍较高。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | BTR 指标和反事实配对设计在医疗 AI 评测中是新颖的 |
| 技术质量 | 7/10 | 方法设计严谨，但 EA-RAG 的技术创新程度中等 |
| 实验充分性 | 7/10 | 4 模型 × 多种配置覆盖充分，但缺少更多基线 RAG 方法对比 |
| 写作质量 | 8/10 | 问题定义清晰，BTR 指标的动机和设计阐述透彻 |
| 实用性 | 8/10 | 对医疗 AI 部署有直接的现实意义 |

---

> [!tip] 关键启示
> 常规准确率严重高估了 LLM 的临床鲁棒性（16-28pp 差距）。BTR 作为一种条件概率指标，能更好地捕捉模型在"看似相似但关键不同"的临床场景中的盲区。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！对关注医疗 AI 安全性和鲁棒性评测的研究者和从业者有重要参考价值。
