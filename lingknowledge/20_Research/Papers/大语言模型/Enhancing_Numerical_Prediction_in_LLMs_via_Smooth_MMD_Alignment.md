---
date: "2026-06-29"
paper_id: "arXiv:2606.27731"
title: "Enhancing Numerical Prediction in LLMs via Smooth MMD Alignment"
authors: "Zhuo Zuo, Li Yue, Wenhao Zheng, Chenpeng Wang, Xianggen Liu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Numerical-Reasoning
  - MMD-Alignment
  - Distribution-Matching
  - Model-Calibration
quality_score: "8.3/10"
created: "2026-06-29"
updated: "2026-06-29"
status: analyzed
---

# Enhancing Numerical Prediction in LLMs via Smooth MMD Alignment

## 核心信息
- **论文ID**：arXiv:2606.27731
- **作者**：Zhuo Zuo, Li Yue, Wenhao Zheng, Chenpeng Wang, Xianggen Liu
- **机构**：--
- **发布时间**：2026-06-26
- **会议/期刊**：arXiv preprint (cs.CL, cs.AI, cs.CE, cs.LG)
- **链接**：[arXiv](https://arxiv.org/abs/2606.27731) | [PDF](https://arxiv.org/pdf/2606.27731)
- **引用**：--

## 摘要翻译

### 英文摘要
Despite their strong general capabilities, large language models (LLMs) often remain unreliable when outputs must be numerically precise. A key reason is the training objective: standard cross-entropy treats numeric tokens as unstructured categories and ignores the metric structure of their values. We propose a training and fine-tuning framework, Smooth MMD Alignment, which augments the cross-entropy loss with a smooth Maximum Mean Discrepancy term that aligns the distribution of predicted numeric values with the reference distribution (e.g., the ground-truth histogram) while respecting their numerical order. By using a smooth kernel and robust statistics, the penalty is less sensitive to outliers and different distribution shapes than alternative distribution-matching losses. Experiments on numerical prediction benchmarks show that Smooth MMD Alignment substantially improves numerical accuracy over standard fine-tuning and existing alignment methods.

### 中文翻译
尽管大语言模型（LLM）具有强大的通用能力，但当输出需要数值精确时，它们通常仍不可靠。一个关键原因是训练目标：标准交叉熵将数值token视为无结构类别，忽略了其值的度量结构。我们提出一个训练和微调框架 Smooth MMD Alignment，在交叉熵损失的基础上增加平滑最大均值差异（MMD）项，将预测数值的分布与参考分布（如真实直方图）对齐，同时保留其数值顺序。通过使用平滑核和稳健统计，该惩罚项对离群值和不同分布形状的敏感度低于替代分布匹配损失。在数值预测基准上的实验表明，Smooth MMD Alignment 相比标准微调和现有对齐方法显著提升了数值精度。

### 核心要点提炼
- **研究背景**：LLM在需要数值精确性的任务中表现不可靠
- **研究动机**：标准交叉熵损失将数值视为无结构token，未利用数值的度量结构
- **核心方法**：在损失函数中加入平滑MMD项，对齐预测与真实数值分布
- **主要结果**：在数值预测基准上显著优于标准微调和现有对齐方法
- **研究意义**：为LLM的数值推理能力增强提供了通用的训练框架

## 研究背景与动机

### 领域现状
LLM在数学推理、科学计算、金融预测等需要精确数值的任务中仍表现不佳。现有改进方法包括：
1. 链式思维（Chain-of-Thought）推理
2. 工具使用（如调用计算器）
3. 专门的数值token化方法

### 现有方法的局限性
这些方法未解决根本问题：**标准交叉熵训练目标本身不适合数值预测**。交叉熵将数值token（如"42.5"）视为与任何其他token无异的类别，忽略了数值之间的度量关系（大小、距离、顺序）。

### 研究动机
从训练/微调层面修复这一系统性缺陷，通过分布对齐让模型"理解"数值的度量结构。

## 研究问题

### 核心研究问题
如何在训练/微调LLM时有效融入数值的度量结构信息，提升数值预测的精确性？

## 方法概述

### 核心思想
在标准交叉熵损失的基础上增加平滑MMD（Maximum Mean Discrepancy）正则化项，将模型预测的数值分布与真实分布对齐，使模型不仅学会"分类"数值token，更学会保持数值间的度量关系。

### 方法框架

#### 整体架构

![[overview_page1.png|800]]

> 图1：Smooth MMD Alignment 方法概览，展示交叉熵损失与平滑MMD项的联合优化框架

#### 技术细节

**损失函数设计**：
$$\mathcal{L} = \mathcal{L}_{CE} + \lambda \cdot \text{MMD}_{smooth}(P_{pred}, P_{ref})$$

其中：
- $\mathcal{L}_{CE}$ 为标准交叉熵损失
- $\text{MMD}_{smooth}$ 为平滑最大均值差异
- $\lambda$ 为平衡系数
- $P_{pred}$ 为模型预测的数值分布
- $P_{ref}$ 为参考分布（如真实标签的直方图）

**平滑MMD的优势**：
- 使用平滑核函数（如RBF核），对分布形状差异更稳健
- 对离群值和长尾分布不敏感
- 相比KL散度、Wasserstein距离等替代方案更稳定

## 实验结果

### 实验设置

#### 数据集
- 数值预测基准（包括回归和分类场景）
- 数学推理数据集（如GSM8K）

#### 基线方法
- 标准交叉熵微调
- 基于分布的替代对齐方法（KL散度、Wasserstein等）

### 主要结果

![[gsm8k_acc_at_rel_tol_page1.png|600]]

> 图2：GSM8K数据集上不同相对容差下的准确率对比

- Smooth MMD Alignment在所有数值预测基准上显著优于标准微调
- 相比其他分布匹配损失函数（KL散度、Wasserstein距离），平滑MMD更稳定且性能更好
- 在不同分布形态（正态、偏态、多峰）下均表现稳健

### 消融实验

![[lambda_page1.png|600]]

> 图3：不同λ值（平滑MMD权重）对性能的影响

## 深度分析

### 研究价值评估

#### 理论贡献
- 识别了LLM数值预测不可靠的根本原因：交叉熵损失的类别化本质
- 提出分布对齐的正则化解决思路
- 证明了平滑MMD在此任务中的优越性

#### 实际应用价值
- 数学推理：提升GSM8K等基准上的准确率
- 科学计算：数值预测任务
- 金融预测：需要精确数值的场景
- 方法即插即用，可与现有微调方案结合

### 方法优势详解

1. **理论优雅**：从损失函数层面解决数值度量问题
2. **通用性强**：适用于任何需要数值输出的LLM任务
3. **鲁棒性好**：平滑MMD对各类型分布差异不敏感

### 局限性分析

- 对离散分类任务（非数值型）不适用
- 需要计算批次内的数值分布，对极小批次场景可能不稳定
- λ超参数需要根据任务调整

## 我的综合评价

### 总体评分
**8.3/10** - 从训练目标层面优雅地解决了LLM数值不可靠问题，方法简洁有效

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 视角新颖，从损失函数出发解决数值度量问题 |
| 技术质量 | 9/10 | 方法简洁，理论基础扎实，平滑MMD选择有理有据 |
| 实验充分性 | 8/10 | 多基准验证，消融分析合理 |
| 写作质量 | 8/10 | 逻辑清晰 |
| 实用性 | 8/10 | 即插即用，适用范围广 |

## 相关论文

### 直接相关
- 数值推理增强相关论文
- MMD在机器学习中的应用

## 外部资源
- 项目页面：https://arxiv.org/abs/2606.27731

> [!tip] 关键启示
> LLM数值不可靠的根本原因不在推理能力，而在训练目标——修复损失函数比增加推理步骤更根本。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 值得精读！从训练目标层面解决LLM数值预测问题，方法简洁优雅且实用性强。
