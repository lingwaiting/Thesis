---
date: "2026-08-27"
paper_id: "arXiv:2608.25231"
title: "Output Dilution: Redundant but Fragile Representations in MoE Models"
authors: "Orion Reblitz-Richardson"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Mixture-of-Experts
  - 可解释性
  - 激活操控
quality_score: "8.0/10"
created: "2026-08-27"
updated: "2026-08-27"
status: analyzed
---

# Output Dilution: Redundant but Fragile Representations in MoE Models

## 核心信息
- **论文ID**：arXiv:2608.25231
- **作者**：Orion Reblitz-Richardson
- **机构**：Distiller Labs
- **发布时间**：2026-08-25
- **会议/期刊**：arXiv 预印本（cs.LG, cs.AI, cs.CL）
- **链接**：[arXiv](http://arxiv.org/abs/2608.25231v1) | [PDF](https://arxiv.org/pdf/2608.25231v1)
- **推荐评分**：10.0/10

## 摘要翻译

### 英文摘要
Mixture-of-Experts (MoE) models appear to encode moral content as robustly as dense models, yet prove far more fragile in their encoding. In OLMoE-1B-7B, linear probes recover moral valence from nearly every expert-layer combination, with mean peak-layer accuracy above 90%. But these representations collapse under levels of activation noise that a dense model of matched size easily tolerates, with a 4.2-fold difference in robustness.

We trace this to output dilution. Because the MoE block averages across active experts before contributing to the residual stream, the feedforward signal reaching downstream layers is nearly two orders of magnitude smaller than in a dense MLP. Moral information, our interest, survives aggregation intact but at a scale trivially overwhelmed by perturbation. Routing itself remains stable under noise while the vulnerability originates entirely in the diluted aggregate.

Checkpoint trajectories confirm this is architectural, not learned. Experts never specialize and accuracy saturates within the first few thousand steps. In sparse architectures, redundant encoding does not imply robust encoding.

### 中文翻译
混合专家（MoE）模型表面上与稠密模型一样能稳健地编码道德内容，但其编码实际上脆弱得多。在 OLMoE-1B-7B 中，线性探针几乎能从每一层专家组合中恢复道德效价信息，平均峰值层准确率超过 90%。然而这些表征在稠密同尺寸模型能轻易容忍的激活噪声水平下就会崩溃，鲁棒性差异高达 4.2 倍。

作者将原因追溯到"输出稀释"（output dilution）。由于 MoE 块在贡献到残差流之前会对激活专家进行平均，到达下游层的前馈信号几乎比稠密 MLP 小两个数量级。道德信息（本文关注的对象）在聚合后保持完整，但其尺度极小，极易被扰动淹没。路由本身在噪声下保持稳定，脆弱性完全源自被稀释的聚合结果。

检查点轨迹证实这是架构性的而非学习得到的：专家从不专门化，准确率在最初几千步内就饱和。在稀疏架构中，冗余编码并不意味着鲁棒编码。

### 核心要点提炼
- **研究背景**：MoE 架构因其计算效率被大规模采用，但其可解释性和表征鲁棒性研究不足。
- **研究动机**：探明 MoE 中看似冗余的表征为何在实际扰动下异常脆弱。
- **核心方法**：线性探针 + 激活噪声扰动 + 检查点轨迹分析，定位脆弱性根源为"输出稀释"。
- **主要结果**：MoE 道德表征冗余但不鲁棒，鲁棒性比稠密模型差 4.2 倍，源于专家输出的平均化稀释。
- **研究意义**：揭示稀疏架构的一个被忽视的机制性缺陷，对 MoE 的安全对齐和可解释性有重要启示。

## 研究背景与动机

### 领域现状
混合专家（Mixture-of-Experts, MoE）架构通过稀疏激活部分参数实现计算与模型规模的解耦，已成为训练万亿级模型的主流方案。与此同时，模型可解释性（mechanistic interpretability）领域常依赖"线性表征假设"——即概念可在激活空间中以线性方向编码，并通过探针恢复。

### 现有方法的局限性
- 可解释性研究大多集中在稠密模型，MoE 的表征结构研究相对空白。
- 已有工作倾向于用"冗余性"作为鲁棒性的代理指标，本文证明这一假设在稀疏架构中不成立。
- MoE 的输出聚合机制对下游信号尺度的系统性影响未被量化。

### 研究动机
作者试图回答一个反直觉的问题：为什么在 MoE 中，探针能高精度恢复的信息，却在实际扰动下不堪一击？这指向一个更根本的问题——稀疏架构中"编码存在"与"编码鲁棒"是两回事。

## 研究问题

### 核心研究问题
1. MoE 模型中的概念表征（以道德效价为案例）是否与稠密模型同样鲁棒？
2. 若更脆弱，其机制根源是什么——是学习动态还是架构本身的产物？

## 方法概述

### 核心思想
用"线性探针 + 噪声鲁棒性"双重视角审视 MoE 表征：探针证明信息"在"，噪声扰动实验证明信息"是否经得起干扰"。通过对比稠密模型与 MoE，并追踪训练轨迹，将脆弱性归因于架构性的"输出稀释"。

### 方法框架

#### 整体架构
论文的实证框架包含三个层次：
1. **表征存在性检验**：对 OLMoE-1B-7B 的每一层、每个专家组合训练线性探针，检测道德效价信息是否可被解码。
2. **鲁棒性检验**：对激活注入不同强度的噪声，比较稠密与 MoE 模型的表征存活率。
3. **机制归因**：量化 MoE 聚合前后信号幅度的衰减，并追踪检查点以区分架构性与学习性成因。

![[figure_1_dense_vs_moe_page1.png|600]]

> 图1：稠密模型 vs MoE 模型的表征鲁棒性对比

#### 各模块详细说明

**模块1：线性探针解码（表征存在性）**
- **功能**：验证道德效价信息是否在激活空间中线性可解码
- **处理流程**：对每层/每专家组合训练探针 → 记录峰值准确率
- **结果**：几乎所有专家-层组合都能高精度恢复（平均峰值 >90%）

**模块2：噪声鲁棒性检验（表征脆弱性）**
- **功能**：量化表征在扰动下的存活能力
- **处理流程**：向激活注入高斯噪声 → 测量探针准确率衰减
- **结果**：MoE 在稠密模型可容忍的噪声水平下即崩溃，鲁棒性差 4.2 倍

**模块3：输出稀释归因（机制分析）**
- **功能**：解释脆弱性的架构根源
- **关键发现**：MoE 块对专家输出取平均后再进入残差流，导致前馈信号幅度比稠密 MLP 小近两个数量级
- **验证**：路由在噪声下保持稳定，说明脆弱性并非来自路由，而来自被稀释的聚合结果

![[figure_3_output_dilution_page1.png|600]]

> 图3：输出稀释机制示意

## 实验结果

### 实验设置
- **模型**：OLMoE-1B-7B（稀疏）、匹配尺寸的稠密模型
- **任务**：道德效价的线性探针解码
- **扰动**：激活注入不同强度噪声

### 主要结果

1. **冗余编码**：MoE 中道德效价信息在几乎每个专家-层组合中都可被线性探针恢复，平均峰值层准确率 >90%。
2. **脆弱鲁棒性**：这些表征在稠密同尺寸模型可轻易容忍的噪声水平下崩溃，鲁棒性差距达 4.2 倍。
3. **输出稀释**：MoE 块对活跃专家取平均后才贡献残差流，前馈信号比稠密 MLP 小近两个数量级。
4. **路由稳定**：噪声下路由本身保持稳定，脆弱性完全源于被稀释的聚合结果。
5. **架构性成因**：检查点轨迹显示专家从不专门化，准确率在前几千步即饱和，证明这是架构问题而非学习问题。

![[figure_2_expert_uniformity_page1.png|600]]

> 图2：专家均匀性分析

![[figure_4_training_trajectory_page1.png|600]]

> 图4：训练轨迹分析

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：推翻"冗余即鲁棒"的隐含假设**
  - 创新点：用实验证明稀疏架构中冗余编码不蕴含鲁棒编码，这是对可解释性领域"线性表征假设"在 MoE 上的重要修正
  - 学术价值：为 MoE 表征研究提供了一个新的分析范式
- **贡献2：提出"输出稀释"机制**
  - 创新点：精确量化了 MoE 专家平均化对信号尺度的系统性衰减，将脆弱性归因到可测量的架构量
  - 影响范围：对 MoE 架构设计（如残差连接、专家聚合方式）有直接指导意义

#### 实际应用价值
- **MoE 安全对齐**：揭示了对齐干预（如激活操控）在 MoE 上可能比稠密模型更脆弱，提示需要专门的对齐策略。
- **可解释性工具设计**：提示针对稀疏架构的可解释性方法需要显式考虑信号稀释效应。

### 方法优势详解

#### 优势1：双重视角交叉验证
- **描述**：同时用"存在性探针"和"鲁棒性扰动"检验表征，避免了单一视角的片面结论
- **对比分析**：若只看探针准确率会得出"MoE 表征健康"的错误结论

#### 优势2：机制归因而非现象描述
- **描述**：通过信号幅度量化和训练轨迹追踪，将脆弱性定位到架构机制而非停留在相关性层面
- **实验验证**：路由稳定性实验排除了路由作为脆弱性来源

### 局限性分析

#### 局限1：单一案例概念
- **描述**：仅以"道德效价"作为测试概念
- **影响**：结论是否泛化到其他类型的概念（事实性、语法等）尚待验证
- **可能的解决方案**：扩展到更多概念类别进行系统性验证

#### 局限2：单一模型尺度
- **描述**：主要在 OLMoE-1B-7B 一个规模上验证
- **影响**：输出稀释效应在更大规模 MoE 中的表现未知
- **可能的解决方案**：在更大规模、不同 MoE 变体上复现

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 洞察深刻、机制清晰，纠正了领域内一个重要隐含假设，但案例和尺度较为单一。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "冗余≠鲁棒"的反直觉发现及输出稀释机制新颖 |
| 技术质量 | 8/10 | 实验设计严谨，探针+扰动+轨迹三重验证 |
| 实验充分性 | 6/10 | 单一概念、单一尺度，泛化性有待扩展 |
| 写作质量 | 8/10 | 逻辑清晰，论证链条完整 |
| 实用性 | 8/10 | 对 MoE 对齐与可解释性有直接指导意义 |

### 重点关注

#### 值得关注的技术点
- "输出稀释"的定量刻画方法（信号幅度衰减量）
- 探针存在性与鲁棒性分离的实验范式

#### 需要深入理解的部分
- 输出稀释是否可通过改进聚合策略（如加权求和、残差增强）缓解

## 相关论文

### 直接相关
- [[Mixture_of_Experts]] - MoE 架构基础
- 可解释性领域的线性表征假设相关研究

### 背景相关
- 激活操控（activation steering）相关论文

## 外部资源
- arXiv: https://arxiv.org/abs/2608.25231

> [!tip] 关键启示
> 在稀疏架构中，"信息被编码了"与"信息经得起扰动"是两回事；冗余的表征可能反而脆弱。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 值得一读！对关注 MoE 可解释性与安全对齐的研究者尤其有启发。
