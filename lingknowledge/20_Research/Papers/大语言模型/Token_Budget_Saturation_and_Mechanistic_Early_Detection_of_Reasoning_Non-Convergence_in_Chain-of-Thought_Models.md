---
date: "2026-07-26"
paper_id: "arXiv:2607.21433"
title: "Token Budget Saturation and Mechanistic Early Detection of Reasoning Non-Convergence in Chain-of-Thought Models"
authors: "Renuka Oladri, Niveda Jawahar, Abdirisak Mohamed"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Chain-of-Thought
  - Reasoning
  - Early-Detection
  - Mechanistic-Interpretability
  - Linear-Probes
  - Inference-Efficiency
quality_score: "7.8/10"
created: "2026-07-26"
updated: "2026-07-26"
status: analyzed
---

# Token Budget Saturation and Mechanistic Early Detection of Reasoning Non-Convergence in Chain-of-Thought Models

## 核心信息
- **论文ID**：arXiv:2607.21433
- **作者**：Renuka Oladri, Niveda Jawahar, Abdirisak Mohamed
- **机构**：--
- **发布时间**：2026-07-23
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2607.21433v1) | [PDF](https://arxiv.org/pdf/2607.21433v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Chain-of-thought reasoning models such as DeepSeek-R1-Distill-Qwen-7B exhibit a bimodal convergence pattern: generations either terminate within a token budget (converged) or exhaust it without reaching a conclusion (non-converged). We characterize this phenomenon empirically, showing that converged generations achieve 90.3% accuracy on AIME 1983-2024 while non-converged ones achieve only 6.6%, with an overall convergence rate of 62.0%.

We then ask whether this outcome is detectable early in the thinking chain using internal model representations. Training linear probes on hidden-state activations at token positions 50-300, we find that layer-20 activations at token 150 achieve AUC 0.608 (±0.080, 5-fold CV), reliably above chance even at token 50. Activation probes consistently outperform behavioral baselines derived from token entropy and repetition statistics. A sweep-level permutation test yields p=0.063 (100,000 permutations), consistent with a modest signal that our sample size cannot confirm at conventional thresholds.

These findings suggest that convergence fate is partially encoded in intermediate representations well before the generation ends, opening a path toward early-exit inference and adaptive compute allocation.

### 中文翻译
Chain-of-thought推理模型（如DeepSeek-R1-Distill-Qwen-7B）展现出双峰收敛模式：生成要么在token预算内终止（收敛），要么耗尽预算而未达成结论（不收敛）。我们从经验上刻画了这一现象，显示收敛的生成在AIME 1983-2024上达90.3%准确率，而不收敛的仅达6.6%，整体收敛率为62.0%。

我们接着探讨这一结果是否可以通过内部模型表征在推理链的早期被检测到。在token位置50-300的隐藏状态激活上训练线性探针，我们发现token 150处的层20激活达到AUC 0.608（±0.080, 5折CV），即使在token 50处也可靠地高于随机水平。激活探针始终优于基于token熵和重复统计的行为基线。扫描级排列检验得到p=0.063（100,000次排列），与存在适度信号一致，但我们的样本量无法在常规阈值上确认。

这些发现表明收敛命运在生成结束前已部分编码在中间表征中，为early-exit推理和自适应计算分配开辟了道路。

### 核心要点提炼
- **研究背景**：CoT推理模型的"思考链"消耗大量token，但并非所有思考都能收敛到正确答案
- **研究动机**：如果能提前检测不收敛的推理链，可以节省大量计算资源
- **核心方法**：使用线性探针在隐藏状态上预测推理链的收敛/不收敛命运
- **主要结果**：收敛率62.0%，收敛准确率90.3% vs 不收敛6.6%；token 150时AUC 0.608可提前检测
- **研究意义**：为early-exit推理和自适应计算分配提供了机制可解释性基础

## 研究背景与动机

### 领域现状
Chain-of-Thought推理已成为提升LLM复杂推理能力的关键技术。DeepSeek-R1等模型通过生成长推理链来解决问题，但这也带来了推理成本飙升的问题。一个核心观察是：**并非所有推理链都能成功收敛到答案**——许多推理链在token预算耗尽后仍无结论。

### 现有方法的局限性
- **固定token预算**：大多数CoT推理使用固定的最大token数，浪费在不收敛的推理链上
- **缺乏早期诊断**：无法在推理过程中判断当前路径是否会收敛
- **行为信号不足**：token熵、重复率等行为信号预测能力有限

### 研究动机
如果可以在推理链的早期（如token 50-150，而非等token预算耗尽）就检测出不收敛的信号，就可以：
1. **节省计算**：提前终止不收敛的推理，释放资源
2. **自适应分配**：将更多计算分配给有希望的推理链
3. **理解推理机制**：通过探针分析揭示CoT推理的内部表征

## 研究问题

### 核心研究问题
1. CoT推理的收敛/不收敛现象是否可以系统地量化？
2. 推理链的最终命运（收敛/不收敛）是否在早期token的隐藏状态中就已编码？
3. 内部表征探针是否优于行为信号基线？

## 方法概述

### 核心思想
将CoT推理的收敛/不收敛视为一个二分类问题，使用线性探针（linear probe）在中间层隐藏状态上预测推理链的最终命运。关键洞察是：**如果收敛信号在早期就存在于隐藏状态中，说明模型内部已经"知道"当前推理路径的质量，尽管还需要更多token来显式表达**。

### 方法框架

#### 整体架构

```
CoT推理链生成 → 提取隐藏状态（token 50-300） → 训练线性探针 → 预测收敛/不收敛
                                                      ↓
                                              对比行为基线（熵、重复率）
```

**两阶段分析**：
1. **现象刻画**：在AIME数学竞赛题上量化双峰收敛模式
2. **探针训练**：在隐藏状态激活上训练线性分类器预测收敛命运

#### 各模块详细说明

**模块1：双峰收敛现象刻画**
- **实验设置**：
  - 模型：DeepSeek-R1-Distill-Qwen-7B
  - 数据集：AIME 1983-2024
  - 评估指标：准确率、收敛率
- **核心发现**：
  - 收敛率62.0%：多数推理链能在预算内收敛
  - 收敛样本准确率90.3%：收敛几乎等同于正确
  - 不收敛样本准确率6.6%：不收敛几乎等同于错误（接近随机猜测水平）
  - 这意味着**收敛与否是一个极强的质量信号**

**模块2：线性探针训练**
- **目标**：从中间层隐藏状态预测推理链是否收敛
- **探针位置**：token位置50-300，重点分析token 150
- **探针层**：各层激活，重点分析层20
- **训练方法**：5折交叉验证的逻辑回归
- **关键发现**：
  - Token 150、层20：AUC 0.608（显著高于随机0.5）
  - Token 50已有预测能力（AUC略低但可靠）
  - 激活探针始终优于行为基线

**模块3：行为基线对比**
- **Token熵基线**：基于生成token的熵值预测收敛
- **重复统计基线**：基于token重复率预测收敛
- **结果**：激活探针AUC显著高于行为基线，证明内部表征包含行为信号无法捕获的信息

### 关键实验设计

![[checkpoint_sweep_page1.png|800]]

> 图1：不同checkpoint位置的探针性能扫描。横轴为token位置，展示了从早期到后期的AUC变化趋势。

![[layer_sweep_page1.png|800]]

> 图2：不同层的探针性能扫描。展示了哪些层的激活对收敛命运的编码最丰富。

## 实验结果

### 主要结果

#### 双峰收敛现象
| 类别 | 比例 | AIME准确率 |
|------|------|-----------|
| 收敛 | 62.0% | 90.3% |
| 不收敛 | 38.0% | 6.6% |
| 总体 | 100% | ~58.7% |

> 收敛样本的准确率约等于模型能解决问题的比例，不收敛样本几乎是随机猜测。

#### 探针预测性能

| 方法 | AUC | 说明 |
|------|-----|------|
| 激活探针（token 150, 层20） | 0.608±0.080 | 最佳设置 |
| Token熵基线 | <0.608 | 始终劣于激活探针 |
| 重复统计基线 | <0.608 | 始终劣于激活探针 |
| Token 50（早期） | >0.5 | 已可靠高于随机 |

#### 统计显著性
- 排列检验：p=0.063（100,000次排列）
- 未达常规阈值（p<0.05），但接近显著
- 作者诚实承认样本量不足，信号存在但需要更大规模验证

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：系统刻画CoT双峰收敛现象**
  - 创新点：首次量化CoT推理中收敛与准确率的强关联
  - 学术价值：为推理效率优化提供了明确的优化目标（收敛vs不收敛）
  - 关键数据：90.3% vs 6.6%的准确率差距极具说服力

- **贡献2：证明收敛命运在早期表征中编码**
  - 创新点：使用线性探针揭示中间表征包含未来推理质量的信息
  - 学术价值：为机制可解释性研究提供了新的分析维度
  - 方法学贡献：线性探针+行为基线对比的实验设计可复用

#### 实际应用价值
- **应用场景1：Early-Exit推理**
  - 适用性：中——AUC 0.608尚不足以直接用于生产系统
  - 优势：如果探针性能进一步提升，可节省38%的不收敛推理计算
  - 潜在影响：显著降低CoT推理的平均成本

- **应用场景2：自适应计算分配**
  - 适用性：中高——结合探针信号与预算管理
  - 优势：为"更多计算给更有希望的推理链"提供了信号基础
  - 潜在影响：提升推理效率而不损失准确率

### 方法优势详解

#### 优势1：机制可解释性视角
- **描述**：从内部表征而非行为信号分析推理质量
- **技术基础**：线性探针是机制可解释性的标准工具
- **实验验证**：激活探针始终优于行为基线
- **对比分析**：行为信号（熵、重复）只反映表面现象；激活探针捕捉深层推理质量

#### 优势2：诚实的统计分析
- **描述**：p=0.063未达显著，但论文诚实呈现并未夸大
- **技术基础**：100,000次排列检验提供可靠的显著性评估
- **实验验证**：5折交叉验证确保结果的稳健性
- **对比分析**：许多论文在p>0.05时仍宣称"显著"，本文的诚实值得肯定

### 局限性分析

#### 局限1：AUC 0.608的实用价值有限
- **描述**：0.608的AUC仅略高于随机（0.5），离可直接部署（通常需0.8+）有较大差距
- **原因**：收敛命运信号可能在更深层或更晚的token位置更清晰
- **影响**：现阶段不能直接用于生产系统的early-exit决策
- **可能的解决方案**：使用非线性探针、更大模型、更多token位置

#### 局限2：单一模型和数据集
- **描述**：仅在DeepSeek-R1-Distill-Qwen-7B和AIME上验证
- **原因**：初步概念验证研究
- **影响**：不清楚发现的普遍性

#### 局限3：未达到统计显著性
- **描述**：p=0.063未达常规0.05阈值
- **原因**：样本量不足或信号本身较弱
- **影响**：发现需要更大规模验证才能确认
- **可能的解决方案**：扩大AIME数据集或使用更多推理任务

## 未来工作建议

### 基于分析的未来方向
1. **方向1：改进探针架构**
   - 动机：线性探针可能欠拟合非线性信号
   - 可能的方法：使用MLP探针、attention探针或probing classifiers
   - 预期成果：AUC提升至0.7+，接近实用阈值

2. **方向2：多模型跨任务验证**
   - 动机：确认现象的普遍性
   - 可能的方法：在Llama、Qwen、DeepSeek等多个CoT模型上复现
   - 预期成果：建立收敛预测的通用框架

3. **方向3：Early-Exit策略的实际部署**
   - 动机：从"可检测"到"可应用"
   - 可能的方法：结合探针信号设计自适应token预算策略
   - 预期成果：实际计算节省而不显著损失准确率

## 我的综合评价

### 价值评分

#### 总体评分
**7.8/10** — 提出了一个重要且实际的问题（CoT推理浪费计算在不收敛的链上），实验设计严谨（双峰现象刻画+探针分析+行为基线对比+排列检验），发现具有启发性。主要扣分在于AUC仅0.608、未达统计显著性，离实用还有距离。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次从机制可解释性角度研究CoT收敛预测，问题定义清晰且有实际价值 |
| 技术质量 | 7/10 | 实验设计规范（5折CV、排列检验、行为基线对比），但线性探针方法较基础 |
| 实验充分性 | 6/10 | 仅单一模型、单一数据集，p=0.063未达显著，限制了结论的可靠性 |
| 写作质量 | 8/10 | 结构清晰，诚实地呈现了局限性（特别是p值和样本量限制） |
| 实用性 | 7/10 | 方向正确但AUC 0.608离实用有距离，提供了有价值的分析框架但尚未产生可部署方案 |

### 重点关注
- 90.3% vs 6.6%的准确率差距——收敛与否是极强的质量信号
- 激活探针优于行为基线——证明了内部表征分析的价值
- 论文的诚实性——不夸大不显著的结果，这在当今学术界难得

> [!tip] 关键启示
> 推理链的"命运"在早期token中已编码——这暗示CoT推理可能具有"自评估"能力，只是目前无法在行为层面读出。如果能有效解码这一信号，就能实现显著的计算节省。

> [!warning] 注意事项
> - AUC 0.608远未达到生产可用水平，不要急于工程化
> - 单一模型的结果可能不具有普遍性
> - p=0.063提示需要更大规模验证

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！问题定义清晰，实验设计规范，为推理效率优化提供了机制可解释性的新视角。虽然结果距离实用还有距离，但方向和框架本身具有重要价值。

## 相关论文

### 直接相关
- [[DeepSeek-R1]] - 本文研究的CoT推理模型
- [[Chain-of-Thought]] - CoT推理范式的基础

### 背景相关
- [[Linear_Probes]] - 机制可解释性中线性探针方法
- [[Early-Exit_Inference]] - Early-exit推理的相关研究

## 外部资源
- AIME 1983-2024: 美国数学邀请赛历年试题
