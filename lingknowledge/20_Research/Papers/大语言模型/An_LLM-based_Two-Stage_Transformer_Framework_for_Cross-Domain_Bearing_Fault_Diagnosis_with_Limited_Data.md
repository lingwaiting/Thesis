---
date: "2026-06-24"
paper_id: "arXiv:2606.24459"
title: "An LLM-based Two-Stage Transformer Framework for Cross-Domain Bearing Fault Diagnosis with Limited Data"
authors: "Jinghan Wang, Feng Cheng, Wentao Wu, Hang Li, Gaoliang Peng, Tianchen Liu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 迁移学习
  - 故障诊断
  - Transformer
  - 预训练
  - 轴承故障
  - 工业4.0
quality_score: "7.5/10"
created: "2026-06-24"
updated: "2026-06-24"
status: analyzed
---

# An LLM-based Two-Stage Transformer Framework for Cross-Domain Bearing Fault Diagnosis with Limited Data

## 核心信息
- **论文ID**：arXiv:2606.24459
- **作者**：Jinghan Wang, Feng Cheng, Wentao Wu, Hang Li, Gaoliang Peng, Tianchen Liu
- **机构**：--
- **发布时间**：2026-06-23
- **类别**：cs.LG, cs.CL
- **链接**：[arXiv](http://arxiv.org/abs/2606.24459v1) | [PDF](https://arxiv.org/pdf/2606.24459v1)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Bearing fault diagnosis faces critical challenges when dataset heterogeneity, operating condition variations, and limited labeled data occur simultaneously in industrial environments. Existing approaches address these issues in isolation and rely on implicit feature alignment, limiting effectiveness under concurrent challenges. This paper proposes a knowledge-guided two-stage transfer learning framework that employs a lightweight GPT-2-style Transformer with causal self-attention for hierarchical feature extraction from vibration signals, establishing explicit pathways where pre-trained encoder weights and fault prototype embeddings serve as knowledge carriers from multi-source pre-training to target adaptation. The framework addresses the dual-shift challenge through multi-source learning for generalizable representations, prototype-based knowledge modulation for target adaptation, and taxonomy-adaptive classification for seamless transfer across heterogeneous fault categories. Experimental validation on four real-world datasets demonstrates 92.61% average accuracy with only 10% labeled target data, outperforming state-of-the-art methods by 17.24 percentage points, establishing a practical pathway toward cost-effective predictive maintenance in Industry 4.0 applications.

### 中文翻译
轴承故障诊断面临数据集异构性、工况变化和标注数据有限三重挑战同时发生的严峻局面。现有方法孤立地处理这些问题，依赖隐式特征对齐，限制了在并发挑战下的有效性。本文提出了一个知识引导的两阶段迁移学习框架，采用轻量级GPT-2风格Transformer结合因果自注意力对振动信号进行分层特征提取，建立了预训练编码器权重和故障原型嵌入作为知识载体的显式知识传递路径——从多源预训练到目标域适配。该框架通过多源学习获取可泛化表示、基于原型的知识调制实现目标适配、以及分类法自适应分类实现异构故障类别间的无缝迁移，全面应对双偏移挑战。在四个真实数据集上的实验验证表明，仅用10%目标域标注数据即可达到92.61%的平均准确率，超越最先进方法17.24个百分点，为工业4.0中的经济高效预测性维护建立了可行路径。

### 核心要点提炼
- **研究背景**：工业轴承故障诊断面临数据异构、工况变化、标注有限三重挑战
- **研究动机**：现有方法孤立处理单一问题，依赖隐式特征对齐，无法有效应对并发挑战
- **核心方法**：轻量GPT-2风格Transformer + 知识引导两阶段迁移学习框架（多源预训练→目标适配）
- **主要结果**：10%标注数据下92.61%准确率，超越SOTA 17.24个百分点
- **研究意义**：为工业4.0经济高效预测性维护提供可行路径

## 研究背景与动机

### 领域现状
轴承作为旋转机械的核心部件，其故障诊断对工业设备的安全运行至关重要。基于深度学习的故障诊断方法近年来取得了显著进展，但大多数方法假设训练和测试数据来自相同分布，而这在真实工业场景中很少成立。

### 现有方法的局限性
1. **问题孤立处理**：数据异构、工况变化、标注有限通常被分别研究，缺乏统一的解决方案
2. **隐式特征对齐**：现有域适应方法依赖隐式对齐，解释性差且鲁棒性不足
3. **标注依赖重**：目标域需要大量标注数据进行微调，标注成本高昂
4. **异构故障类别不兼容**：不同设备和场景的故障类别体系不同，限制了模型迁移

### 研究动机
工业4.0背景下，预测性维护对成本控制和生产效率至关重要。需要一种能在少量目标域标注下实现跨域迁移的轴承故障诊断方法。

## 研究问题

### 核心研究问题
1. 如何在仅10%目标域标注数据下实现高精度跨域轴承故障诊断？
2. 如何通过显式知识传递（编码器权重+原型嵌入）解决双偏移挑战（域偏移+类别偏移）？
3. 如何处理不同数据集间异构的故障类别体系？

## 方法概述

### 核心思想
将知识传递显式化——预训练编码器权重传递通用特征提取能力，故障原型嵌入传递类别语义知识。通过多源学习获取泛化表示，再通过原型知识调制适配目标域，最终由分类法自适应分类处理异构类别。

### 方法框架

#### 整体架构

```
阶段1：多源预训练
[多源振动信号] → [GPT-2风格Transformer编码器] → [通用特征提取器]
                        ↓
              [故障原型嵌入] → [类别知识库]

阶段2：目标域适配
[目标域少量标注] → [原型知识调制] → [分类法自适应分类] → [故障类别]
```

#### 各模块详细说明

**模块1：轻量GPT-2风格Transformer编码器**
- **功能**：从振动信号中分层提取特征
- **输入**：原始振动信号
- **输出**：分层特征表示
- **关键技术**：因果自注意力机制、轻量化设计（相比标准GPT-2参数量大幅减少）

**模块2：多源预训练**
- **功能**：在多个源域数据集上预训练通用特征提取器和故障原型
- **输入**：多个源域的标注轴承数据
- **输出**：预训练编码器权重 + 故障原型嵌入
- **处理流程**：
  1. 在多个源域联合训练Transformer编码器
  2. 为每个故障类别学习原型嵌入
  3. 建立显式的知识传递路径
- **关键技术**：多任务学习、原型学习

**模块3：原型知识调制**
- **功能**：利用预训练的故障原型嵌入指导目标域适配
- **输入**：目标域少量标注样本 + 源域故障原型
- **输出**：调制后的目标域特征表示
- **关键技术**：原型匹配、特征调制

**模块4：分类法自适应分类**
- **功能**：处理源域和目标域间异构的故障类别体系
- **输入**：调制后的特征
- **输出**：目标域故障类别预测
- **关键技术**：类别映射、自适应分类头

## 实验结果

### 主要结果
- **核心指标**：4个真实数据集，10%标注数据下平均准确率92.61%
- **对比SOTA**：超越现有最优方法17.24个百分点
- **数据效率**：仅需10%标注数据即可达到高标准准确率

### 结果分析
17.24个百分点的提升是非常显著的优势，这表明显式知识传递策略在跨域故障诊断中非常有效。轻量Transformer设计也使得方法适合工业边缘部署。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：提出知识引导显式传递的迁移学习范式——编码器权重传递通用能力，原型嵌入传递类别语义
  - 创新点：将隐式特征对齐升级为显式知识传递路径
  - 学术价值：中高。为迁移学习提供了新的显式范型，可推广到其他工业领域

#### 实际应用价值
- **应用场景1**：工业预测性维护
  - 适用性：高。直接解决工业场景的核心痛点（带标注少、跨域难）
  - 优势：仅需10%标注数据，极大降低标注成本
  - 潜在影响：加速工业4.0的预测性维护落地

#### 领域影响
- **短期影响**：为轴承故障诊断提供高性能、低成本方案
- **中期影响**：显式知识传递范式可扩展到齿轮、电机等其他旋转机械
- **长期影响**：推动工业AI从"数据驱动"向"知识驱动+数据驱动"演进

### 方法优势详解

#### 优势1：显式知识传递
- **描述**：区别于传统隐式特征对齐，显式分离通用特征和类别语义
- **对比分析**：相比Domain Adversarial等方法更加透明和可控

#### 优势2：极高数据效率
- **描述**：10%标注即可达到92.61%准确率
- **技术基础**：多源预训练的知识复用

### 局限性分析

#### 局限1：仅限轴承故障
- **描述**：方法针对轴承故障诊断设计，未验证在其他机械部件上的泛化性
- **可能的解决方案**：扩展到齿轮、电机等其他旋转机械故障诊断

#### 局限2：GPT-2风格Transformer的轻量设计可能限制性能上限
- **描述**：轻量设计适合部署但可能在大规模预训练数据下性能饱和
- **可能的解决方案**：探索自适应模型规模策略

### 适用性与场景分析

#### 适用场景
- **新设备快速部署**：少量标注即可获得高精度
- **多工厂知识迁移**：利用源工厂数据支持新工厂
- **数据稀缺场景**：标注困难的工业场景

## 我的综合评价

### 价值评分

#### 总体评分
**7.5/10** - 方法扎实、实验充分、应用价值明确，17.24个百分点的提升令人印象深刻。显式知识传递范式具有推广潜力。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 显式知识传递范式有亮点，轻量GPT-2 Transformer在振动信号上的应用有新意 |
| 技术质量 | 8/10 | 方法设计合理，双偏移问题的拆解清晰 |
| 实验充分性 | 8/10 | 4个真实数据集验证，数据效率实验有说服力 |
| 写作质量 | 7/10 | 技术描述清晰 |
| 实用性 | 8/10 | 直接面向工业4.0需求，10%标注要求极具实用价值 |

### 重点关注
- 显式知识传递的两条路径设计
- 仅10%标注数据的高效性
- 分类法自适应分类处理异构类别的方案

## 相关论文
- 暂无直接相关论文

> [!tip] 关键启示
> 显式分离通用特征和类别语义的知识传递策略，配合分类法自适应分类，实现了跨域故障诊断的高数据效率迁移，为工业AI的知识驱动路径提供了范例。

> [!warning] 注意事项
> - 方法仅验证于轴承故障，扩展性待验证
> - 轻量Transformer可能限制超大规模预训练的性能上限

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐工业AI和迁移学习研究者阅读，显式知识传递范式值得关注。
