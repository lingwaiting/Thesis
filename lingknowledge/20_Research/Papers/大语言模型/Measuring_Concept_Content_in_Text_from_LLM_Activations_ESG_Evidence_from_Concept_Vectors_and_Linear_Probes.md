---
date: "2026-08-10"
paper_id: "arXiv:2608.07208"
title: "Measuring Concept Content in Text from LLM Activations: ESG Evidence from Concept Vectors and Linear Probes"
authors: "Luc Hazenoot, Zhaochun Ren, Amirhossein Zohrehvand"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 可解释性
  - 线性探针
  - 概念向量
  - ESG
  - 激活表征
  - LLM-Activation
quality_score: "7.8/10"
created: "2026-08-10"
updated: "2026-08-10"
status: analyzed
---

# Measuring Concept Content in Text from LLM Activations: ESG Evidence from Concept Vectors and Linear Probes

## 核心信息
- **论文ID**：arXiv:2608.07208
- **作者**：Luc Hazenoot, Zhaochun Ren, Amirhossein Zohrehvand
- **机构**：Leiden Institute of Advanced Computer Science, Leiden University
- **发布时间**：2026-08-07
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2608.07208v1) | [PDF](https://arxiv.org/pdf/2608.07208v1)

## 摘要翻译

### 英文摘要
Existing measures of how much a text is about a concept read the surface of the text: dictionary word shares, topic proportions, embedding similarities. They score the words a text uses, not the judgment a reader forms about it. Recent work has shown that a gap exists in what LLMs know internally versus what they express in their response. This paper asks whether that internal knowledge, read by monitoring the activations of frozen, out-of-the-box LLMs, can stand in for task-specific fine-tuning when measuring concept content, and which extraction method reads it best.

### 中文翻译
现有的文本概念度量方法读取的是文本表面：词典词频、主题比例、嵌入相似度。它们评估的是文本使用的词汇，而非读者对文本形成的判断。近期研究表明，LLM 内部知道的内容与其外显回答之间存在差距。本文探讨了通过监控冻结的、开箱即用的 LLM 激活来读取这种内部知识能否替代任务特定微调来度量概念内容，以及哪种提取方法最优。

### 核心要点提炼
- **研究背景**：现有文本概念度量仅读取"表面"，LLM 内部激活可能携带更丰富的概念信息
- **核心方法**：比较线性探针和 RFM 概念向量两种激活读取方法，在 ESG 数据集上验证
- **主要结果**：最佳线性探针准确率仅比微调分类器低 0.6 个百分点；12 次比较中 11 次超越模型自身回答
- **研究意义**：证明了从冻结 LLM 激活中读取概念内容的可行性和有效性

## 研究背景与动机

### 领域现状
传统文本概念度量方法包括：词典方法（计数概念词）、主题模型（分配词到主题）、嵌入方法（比较文本与概念方向的相似度）。这些方法共同的问题是：评估文本中使用的词汇，而非读者对文本内容形成的判断。

### 核心发现
LLM 的内部激活包含了比其外显回答更丰富的概念信息。线性表征假说认为概念在激活空间中可作为方向被线性访问。但此前缺乏在应用基准上的受控对比研究。

## 方法概述

### 核心思想
直接从冻结 LLM 的隐藏层激活中读取概念信号，而非依赖模型的文本输出或微调分类器。比较两种提取方法：线性探针（简单但有效）和 RFM 概念向量（理论上更强大但实际表现反直觉）。

### 方法框架

![[environmental.png|800]]

> 图1：ESG 数据集——Environmental 维度的概念度量示意。三种方法（线性探针、RFM 概念向量、嵌入基线）与表面基线和微调分类器的对比框架。

#### 各模块详细说明

**两种激活提取方法**：

1. **线性探针（Linear Probe）**
   - 在冻结 LLM 特定层的激活上训练轻量级线性分类器
   - 找到最能区分概念存在与否的方向
   - 预测结果被解释为概念在该层中的表征强度
   - 使用岭回归或逻辑回归

2. **RFM 概念向量（Recursive Feature Machine）**
   - 计算 Average Gradient Outer Product (AGOP) 矩阵
   - 对 AGOP 矩阵进行特征分解，提取 top-n 特征向量作为概念向量
   - 原本用于分类，概念向量提取是其副产品
   - 迭代更新 AGOP 矩阵以捕获最重要的特征

**Token Pooling 策略**：
- Last-token activation（使用最后 token 的激活，因果注意力使其能"看到"全序列）
- Mean pooling（对所有 token 取均值）
- Max pooling（每个信号取最大值）

**实验模型**：Llama-3.1-8b-it、Qwen-3-8b-it、Qwen-3-14b-it
**基线对比**：嵌入模型（Qwen-3-embedding-8b）+ 逻辑回归、表面方法、模型自身回答、微调分类器

## 实验结果

### 主要发现

| 对比维度 | 线性探针 | RFM 概念向量 | 模型自身回答 | 微调分类器 |
|----------|---------|-------------|-------------|-----------|
| Environmental Acc | 接近微调 | 弱于探针 | 弱于探针 | 最强（差距 0.6pp） |
| Social Acc | 接近微调 | 弱于探针 | 弱于探针 | 最强（差距 1.0pp） |
| Governance Acc | 接近微调 | 弱于探针 | 弱于探针 | 最强（差距 2.1pp） |

### 关键发现
1. **反直觉结果**：预期 RFM 概念向量应更强，但简单的线性探针在所有比较中一致胜出
2. **接近微调**：线性探针在 Environmental 上仅差微调分类器 0.6pp
3. **超越输出**：12 次比较中 11 次，探针超越模型自身的直接回答——"激活知道但输出不说"
4. **RFM 的独特价值**：虽然分类准确率不如探针，但 RFM 产生的是连续分数，可以反映概念在文本中的强度，而非仅仅是二元分类

## 深度分析

### 研究价值评估

#### 理论贡献
- 首次对两种激活读取方法在应用基准上进行受控对比
- 验证了"LLM 激活比其输出包含更多概念信息"的假设
- 简单探针优于 RFM 的反直觉发现对后续研究有指导意义

#### 实际应用价值
- **金融/ESG 分析**：无需微调即可从 LLM 激活中读取 ESG 相关信息
- **低成本概念度量**：在单个 GPU 上一分钟内完成，无需昂贵微调
- **社会科学研究**：提供超出词典方法的概念度量新工具

### 局限性分析
1. **二元标签限制**：ESG 数据集仅有二元标签，无法验证 RFM 连续分数的质量
2. **单领域验证**：仅在金融 ESG 文本上测试，未扩展到其他领域
3. **特定模型选择**：仅测试了 Llama 和 Qwen 两个系列
4. **理论解释缺失**：未深入解释为何简单探针优于 RFM

## 技术路线定位

本文属于 **LLM 可解释性与激活工程** 路线，桥接了线性表征假说与实际应用：
```
线性表征假说 → 概念向量提取 → 激活监控应用 → 本文：实用化对比
```

## 我的综合评价

### 价值评分

#### 总体评分
**7.8/10** — 实用导向的激活工程论文，"简单探针 > RFM"的反直觉发现很有价值。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 不是新方法，但 head-to-head 对比有独特贡献 |
| 技术质量 | 7/10 | 实验设计合理，但缺乏对反直觉结果的理论解释 |
| 实验充分性 | 7/10 | ESG 三支柱覆盖完整，但领域和数据单一 |
| 写作质量 | 8/10 | 清晰流畅，motivation 有力 |
| 实用性 | 8/10 | 对金融 NLP 和社会科学研究有直接实用价值 |

### 重点关注
- "简单探针优于 RFM"的反直觉结论——可能改变后续研究的方法选择
- 激活比输出包含更多信息——对 LLM 安全、对齐研究方向有启示
- 几乎零成本的"激活即度量"范式——可能变革文本概念分析

> [!tip] 关键启示
> LLM 知道的比说出来的多——读取激活而非输出是获取概念信息的更好方式；而且最简单的方法（线性探针）往往最有效。

> [!warning] 注意事项
> - 目前仅验证了二元分类任务，连续度量的质量未知
> - RFM 反直觉落后的原因未被充分解释
> - 需要更多领域和语言的验证

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！对 LLM 可解释性、激活工程和金融 NLP 研究者有重要参考价值。
