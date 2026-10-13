---
date: "2026-10-13"
paper_id: "arXiv:2610.13411"
title: "In-Context Learning for System Identification: Probabilistic Formulation, Long-Sequence Handling, and Benchmark Validation"
authors: "Matteo Rufolo, Dario Piga, Filippo Pura, Marco Forgione"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - In-Context-Learning
  - System-Identification
  - Meta-Learning
  - Transformer
quality_score: "8.1/10"
created: "2026-10-13"
updated: "2026-10-13"
status: analyzed
---

# In-Context Learning for System Identification

## 核心信息
- **论文ID**：arXiv:2610.13411
- **作者**：Matteo Rufolo, Dario Piga, Filippo Pura, Marco Forgione
- **机构**：SUPSI, IDSIA - Dalle Molle Institute for Artificial Intelligence, Lugano, Switzerland + USI Faculty of Informatics, Lugano, Switzerland
- **发布时间**：2026-10-09
- **会议/期刊**：Automatica 2026（in press）
- **链接**：[arXiv](https://arxiv.org/abs/2610.13411) | [PDF](https://arxiv.org/pdf/2610.13411)
- **引用**：--

## 摘要翻译

### 英文摘要
Traditional system identification typically involves two steps: initially, training data is processed to estimate a model of the system of interest; then, the model is used to predict outputs for new input queries. In contrast, recent in-context learning approaches learn a meta model that maps directly from training data and query inputs to outputs in a zero-shot fashion, bypassing explicit model estimation. In this work, we advance this meta-learning framework for system identification in three directions. First, we develop a unified probabilistic meta-identification architecture that combines a Transformer-based encoder-decoder with tailored mechanisms for handling long training sequences, enabling calibrated predictions over long horizons. Second, we show how a pre-trained meta model can act as an uncertainty-aware synthetic data generator for classical system identification, providing task-specific data that regularize parametric models when real data are scarce. Third, we systematically evaluate the framework on standard nonlinear system identification benchmarks and on a challenging out-of-distribution scenario, demonstrating competitive accuracy with state-of-the-art methods and strong generalization.

### 中文翻译
传统系统识别通常分两步：先用训练数据估计一个系统模型，再用该模型对新的输入查询进行预测。相比之下，近期的**上下文学习（in-context learning）**方法学习一个**元模型（meta model）**，以零样本的方式直接从训练数据与查询输入映射到输出，跳过了显式的模型估计。本研究从三个方向推进这一用于系统识别的元学习框架。**第一**，提出一个统一的**概率元识别架构**，将基于 Transformer 的 encoder-decoder 与针对长训练序列设计的机制相结合，实现长时程上的校准预测。**第二**，展示预训练元模型可作为**不确定性感知的合成数据生成器**，为经典系统识别提供任务特定数据，在真实数据稀缺时正则化参数化模型。**第三**，在标准非线性系统识别基准与一个具有挑战性的分布外（OOD）场景上系统评估，展现出与最先进方法相当的精度与强泛化能力。

### 核心要点提炼
- **研究背景**：系统识别依赖"先估计模型、再预测"两阶段流程，数据稀缺或分布偏移时泛化困难。
- **研究动机**：用上下文学习把系统识别转化为元学习问题，直接"数据+查询 → 预测"，免去显式模型估计。
- **核心方法**：Transformer encoder-decoder 概率元模型 + 长序列处理机制 + 不确定性感知合成数据生成。
- **主要结果**：标准非线性基准与 OOD 场景下达到 SOTA 竞争力，仅靠输入侧自适应即可应对显著分布偏移。
- **研究意义**：为系统识别提供"元学习 + 概率校准 + 数据增强"三位一体的新范式。

## 研究背景与动机

### 领域现状
系统识别（System Identification）是控制与信号处理的核心，经典方法需要"先处理数据估计模型、再用模型预测"，模型结构选择与估计质量直接影响泛化。深度学习的兴起带来了数据驱动的系统识别，但大多数方法仍遵循"训练一个特定任务的模型"的范式。

### 现有方法的局限性
- **两阶段分离**：先估计再预测，估计误差会累积到预测阶段。
- **数据依赖强**：真实系统数据往往稀缺，参数化模型容易过拟合。
- **分布偏移敏感**：训练与部署分布不一致时，固定模型泛化能力不足。

### 研究动机
借鉴大语言模型中的上下文学习（in-context learning），把系统识别重新表述为"给定一段训练序列与查询输入，直接输出预测"的元学习问题，用预训练元模型在零样本下模拟系统识别算法的行为。

## 研究问题

### 核心研究问题
如何构建一个统一的概率元识别框架，使其能够（1）处理长训练序列并给出校准预测，（2）在真实数据稀缺时充当不确定性感知的合成数据生成器，（3）在标准基准与分布外场景下都达到 SOTA 级的精度与泛化？

## 方法概述

### 核心思想
训练一个 Transformer 编码器-解码器元模型，直接完成"上下文训练数据 + 查询输入 → 输出预测"的映射。它不再显式估计系统参数，而是学会模拟一个系统识别算法：编码器消化长训练序列，解码器在给定查询时输出概率化（校准）的预测。

### 方法框架

#### 整体架构
统一概率元识别架构 = Transformer encoder-decoder + 长序列处理机制。三个方向的具体贡献分别对应：①概率化预测架构；②元模型作为合成数据生成器；③基准与 OOD 系统评估。

![[2610.13411_fig1.png|600]]

> 图1：概率元识别框架——Transformer 编码长训练序列，解码器对查询输出校准预测。

#### 关键组件说明

**组件1：Transformer encoder-decoder 元模型**
- 编码器：消化"训练数据序列"（历史输入-输出对），捕捉系统动态。
- 解码器：给定新查询输入，输出预测分布的参数（概率化输出）。

**组件2：长训练序列处理机制**
- 针对系统识别中训练序列较长的特点，设计专门机制（如长程注意力/分段处理），支持长时程校准预测。

**组件3：不确定性感知合成数据生成器**
- 预训练元模型对目标系统生成任务特定的合成数据，为经典参数化系统识别提供正则化，缓解真实数据稀缺问题。

## 实验结果

### 实验目标
验证概率元识别框架在标准基准与分布外场景下的精度、校准与泛化能力。

### 数据集与基线
- **标准非线性系统识别基准**：多组非线性动态系统。
- **分布外（OOD）场景**：训练与测试分布显著不同。
- **基线**：与最先进（SOTA）系统识别方法对比。

### 主要结果
- **标准基准**：与 SOTA 方法达到竞争性精度。
- **OOD 场景**：展现出强泛化能力，甚至仅靠输入侧自适应即可应对显著分布偏移。
- **数据稀缺场景**：元模型作为合成数据生成器，为参数化模型提供有效正则化。

### 结果分析
该框架的核心优势在于"元学习 + 概率校准"：概率化输出让预测自带不确定性估计，长序列处理让元模型能利用更长的上下文，而合成数据生成能力则把元模型的先验知识迁移到经典方法中，三者互补提升鲁棒性。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：提出统一的概率元识别架构，把系统识别从"估计-预测"两阶段改为端到端的上下文学习映射。
  - 创新点：用 Transformer 模拟系统识别算法，并输出概率化预测。
- **贡献2**：展示预训练元模型作为不确定性感知合成数据生成器的新用途。
  - 创新点：把元模型的先验转化为任务特定数据，用于正则化经典参数模型。

#### 实际应用价值
- **应用场景1**：工业过程控制与系统辨识，数据稀缺或分布漂移时的快速自适应。
- **应用场景2**：作为经典系统识别方法的"数据增强"前置模块。

### 局限性
- 元模型离线训练需要大规模合成数据，训练成本较高。
- 主要验证于仿真非线性系统，真实工业系统的噪声与非线性结构更复杂。
- 概率化输出的校准质量在更极端的分布偏移下仍需进一步检验。

### 未来工作
- 探索更轻量、更高效的元模型结构。
- 在真实物理系统（机器人、过程控制）上验证。
- 研究元模型与经典系统识别方法更紧密的融合方式。

### 与相关工作对比
- **vs 经典系统识别（PEM、子空间方法等）**：依赖显式模型估计与结构选择；本工作用元学习绕过模型估计。
- **vs 一般深度学习系统识别**：仍需针对特定任务训练；本工作利用上下文学习的零样本迁移能力。
- **研究路线归属**：属于"元学习 / 上下文学习"在科学计算与系统识别中的交叉应用，与 in-context learning 在 PDE、时间序列预测中的工作一脉相承。
