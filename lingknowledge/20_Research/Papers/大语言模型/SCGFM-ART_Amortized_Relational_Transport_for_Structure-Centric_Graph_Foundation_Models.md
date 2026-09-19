---
date: "2026-09-19"
paper_id: "arXiv:2609.20419"
title: "SCGFM-ART: Amortized Relational Transport for Structure-Centric Graph Foundation Models"
authors: "Xiaodong He, Xincheng Wang, Zhao Kang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 图基础模型
  - Graph-Foundation-Model
  - Gromov-Wasserstein
  - 迁移学习
  - 表示学习
  - 图神经网络
quality_score: "8.8/10"
created: "2026-09-19"
updated: "2026-09-19"
status: analyzed
---

# SCGFM-ART: Amortized Relational Transport for Structure-Centric Graph Foundation Models

## 核心信息
- **论文ID**：arXiv:2609.20419
- **作者**：Xiaodong He, Xincheng Wang, Zhao Kang
- **机构**：电子科技大学（University of Electronic Science and Technology of China）
- **发布时间**：2026-09-17
- **会议/期刊**：投稿中（IEEE）
- **链接**：[arXiv](https://arxiv.org/abs/2609.20419) | [PDF](https://arxiv.org/pdf/2609.20419)
- **领域**：图基础模型 · 图表示学习 · 迁移学习

## 摘要翻译

### 英文摘要
Graph foundation models (GFMs) aim to learn transferable representations across severely heterogeneous graph domains. However, severe domain shifts in topology, graph scale, and feature semantics impede the construction of a unified, domain-agnostic representation space. To address this, we propose SCGFM-ART, a structure-centric GFM framework that aligns arbitrary graphs onto a shared relational atlas via Amortized Relational Transport (ART). The relational atlas serves as a universal coordinate system defined by a finite set of relational landmarks (bases), while ART directly predicts reusable, end-to-end graph-to-base transport plans, bypassing costly runtime Gromov-Wasserstein optimizations. Under this formulation, SCGFM-ART decomposes a graph into a unified representation: globally via its relational response coordinates relative to the atlas, and locally via its node-to-role structural correspondences. These correspondences project disparate node attributes into a canonical role space, resolving structural and semantic heterogeneity within a singular alignment interface. Rigorously modeling graphs and atlas bases as finite measured relational spaces, we establish coordinate fidelity bounds, prove stability under predicted transport plans, and derive an amortized coverage bound that guarantees our learning objective tightly surrogates ideal relational coverage. Benchmarked across 14 cross-domain graph- and node-level classification tasks, SCGFM-ART achieves state-of-the-art transferability, securing superior average ranks of 2.29 and 1.14, respectively. Topological perturbation analyses demonstrate that node-role transport retains fine-grained structural nuances beyond global coordinates. On real-world benchmarks, the amortized formulation yields 44.2 to 85.1 times faster frozen target-domain inference by avoiding iterative alignment at test time.

### 中文翻译
图基础模型（GFM）旨在跨高度异构的图域学习可迁移表示。然而，拓扑、图规模与特征语义上的严重域偏移阻碍了统一、域无关表示空间的构建。为此，本文提出 SCGFM-ART——一个以结构为中心的 GFM 框架，通过摊销式关系传输（Amortized Relational Transport, ART）将任意图对齐到一个共享的关系图谱（relational atlas）上。该关系图谱作为通用坐标系，由一组有限的关系地标（bases）定义；ART 直接预测可复用的端到端图-基传输方案，从而绕开运行时昂贵的 Gromov-Wasserstein 优化。在这一形式化下，SCGFM-ART 将图分解为统一表示：全局上通过其相对于图谱的关系响应坐标，局部上通过其节点-角色结构对应。这些对应将异构的节点属性投影到规范的角色空间，在单一对齐接口内化解结构与语义异质性。作者将图与图谱基严格建模为有限测度关系空间，建立了坐标保真度界，证明了在预测传输方案下的稳定性，并推导出摊销覆盖界，保证学习目标紧密代理理想的关系覆盖。在 14 个跨域图级与节点级分类任务上，SCGFM-ART 取得最先进的迁移性，分别获得 2.29 与 1.14 的优异平均排名。拓扑扰动分析表明，节点-角色传输保留了超越全局坐标的细粒度结构差异。在真实世界基准上，由于测试时避免迭代对齐，摊销式形式化使冻结的目标域推理加速 44.2–85.1 倍。

### 核心要点提炼
- **研究背景**：图基础模型要跨异构图域学习可迁移表示，但拓扑/规模/语义域偏移严重。
- **研究动机**：现有对齐方法（如 Gromov-Wasserstein）运行时迭代优化昂贵，且难以统一异构特征语义。
- **核心方法**：ART 摊销式预测图-基传输方案 + 共享关系图谱（relational atlas）作为通用坐标系。
- **主要结果**：14 个跨域任务图级/节点级平均排名 2.29/1.14，推理加速 44.2–85.1 倍。
- **研究意义**：为图基础模型提供"结构中心 + 摊销对齐"的统一表示范式。

## 研究背景与动机

### 领域现状
图基础模型试图模仿语言/视觉基础模型，在图数据上学到可跨任务、跨域迁移的统一表示。但图数据的异质性远高于文本与图像：不同域的图在拓扑结构、节点规模、特征语义上千差万别，导致难以构建统一的表示空间。

### 现有方法的局限性
1. **域无关表示难**：拓扑、规模、语义三重域偏移叠加。
2. **对齐成本高**：基于 Gromov-Wasserstein 的对齐需在运行时做迭代优化，测试时开销大。
3. **特征语义异构**：不同图的节点属性不可直接对齐。

### 研究动机
作者希望用"关系图谱（atlas）作为通用坐标系 + 摊销式传输（ART）一次性预测对齐方案"来同时解决统一表示与推理效率两大难题。

## 研究问题

### 核心研究问题
1. 如何定义一个能容纳任意图的通用坐标系（relational atlas）？
2. 如何用摊销式方法直接预测图-基传输方案，绕开运行时 GW 优化？
3. 如何在单一对齐接口内化解结构与语义双重异质性？

## 方法概述

### 核心思想
把"图对齐"重新表述为"把任意图投影到一个共享关系图谱上"：关系图谱是有限个关系基构成的通用坐标系；ART 模块用前馈网络直接预测图到各基的耦合（transport plan），用 Sinkhorn 投影满足边际约束，从而一次预测、免迭代。

### 方法框架

#### 整体架构
![[framework.png|800]]

> 图1：SCGFM-ART 框架。结构中心图编码器 + ART 模块（预测图-基耦合）→ 关系图谱（atlas）→ 关系坐标 + 传输条件特征，构成统一表示。

#### 各模块详细说明

**模块1：Structure-only Graph Encoder（结构图编码器）**
- **功能**：只依赖结构信息，提取可跨域共享的节点嵌入。
- **输入**：归一化节点度 $X_s(i)=\deg(i)/\max_j\deg(j)$。
- **输出**：节点嵌入 $U=\mathrm{GIN}(A,X_s)\in\mathbb R^{N\times D_h}$。
- **关键技术**：两层跨域共享的 GIN，仅用度数作节点特征，规避特征语义异构。

**模块2：Relational Atlas（关系图谱）**
- **功能**：定义通用坐标系的有限关系基。
- **输入**：$K$ 个可学习参数矩阵 $Z_k$。
- **输出**：对称、有界、去对角的关系矩阵 $B_k=\mathrm{Hollow}[\sigma((Z_k+Z_k^\top)/2)]$。
- **关键技术**：对称化 + sigmoid + 去对角，保证基是合法的关系矩阵；用共享 MLP 得到基角色嵌入 $R_k$。

**模块3：Amortized Relational Transport（ART）**
- **功能**：前馈预测图到各基的传输耦合，替代运行时 GW 优化。
- **输入**：图节点嵌入 $U$ 与基角色嵌入 $R_k$。
- **输出**：图-基耦合 $\widehat T_k=\mathrm{Sinkhorn}(L_k;\mu_G,\nu)$。
- **处理流程**：
  1. 缩放点积计算兼容性 logits：$L_k=(UW_G)(R_kW_B)^\top/\sqrt{D_h}$。
  2. log 域 Sinkhorn 投影到传输多面体 $\Pi(\mu_G,\nu)$。
  3. 由关系能量转换得到图谱坐标。
- **关键技术**：单阶段交叉传输 + Sinkhorn，保留图与基的边际约束，无需逐样本 GW 外循环。

### 方法架构图
![[framework.png|800]]

> 图1（同）：ART 摊销式关系传输的完整流程。

## 实验结果

### 实验目标
验证跨域少样本迁移能力、核心组件贡献（消融）、拓扑扰动鲁棒性与可扩展性。

### 数据集与基线
- **数据集**：14 个跨域图级与节点级分类任务。
- **基线**：自监督图预训练、图基础模型、GW 对齐方法等。
- **评估指标**：平均排名（average rank）、准确率、推理加速比。

### 主要结果

| 任务类型 | 平均排名 | 说明 |
|----------|----------|------|
| 图级分类 | **2.29** | 跨 14 个跨域任务 |
| 节点级分类 | **1.14** | 最优迁移性 |

> 注：平均排名越低越好（1 为最优），SCGFM-ART 均处于前列。

#### 结果分析
- 跨域迁移：在 14 个任务上取得 SOTA 迁移性，说明关系图谱坐标有效统一了异构域。
- 推理效率：摊销式形式化使冻结目标域推理加速 44.2–85.1 倍（测试时免迭代对齐）。
- 拓扑扰动：节点-角色传输保留细粒度结构差异，超越全局坐标。

### 消融实验
- 核心组件分析（rq2）：关系图谱、ART 摊销、节点-角色对应各自贡献明确。
- 拓扑扰动（Figure4）：验证节点-角色传输的细粒度结构保留能力。
- 可扩展性（rq5）：验证大规模下的摊销推理效率。

### 实验结果图
![[Figure4_topology_perturbation.png|800]]

> 图2：拓扑扰动分析，展示节点-角色传输对细粒度结构差异的保留能力。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：关系图谱坐标系**——把 GFM 统一表示为"相对有限关系基的坐标"，提供图域的通用坐标系形式化。
- **贡献2：摊销式关系传输**——用前馈网络一次性预测传输方案，免去运行时 GW 优化，有理论界（保真度、稳定性、覆盖）支撑。
- **贡献3：结构与语义统一**——节点-角色对应在单一接口内化解双重异质性。

#### 实际应用价值
- **应用场景1：跨域图迁移**——在新图域上冻结推理、快速适配。
- **应用场景2：图预训练表示**——为下游任务提供域无关的统一嵌入。

#### 领域影响
- **短期**：为 GFM 提供 SOTA 的跨域迁移与推理加速。
- **中期**：推动图域"基础模型 + 摊销对齐"范式成熟。
- **长期**：关系图谱坐标可能成为图表示学习的通用接口。

### 方法优势详解
1. **理论完备**：坐标保真度、稳定性、摊销覆盖三大界。
2. **推理高效**：44.2–85.1 倍加速，冻结目标域即可用。
3. **结构保留**：节点-角色传输保留细粒度结构差异。

### 局限性分析
1. **结构中心**：只依赖度数等结构特征，对属性丰富的图可能丢失信息。
2. **任务范围**：主要验证分类任务，生成/回归类任务未覆盖。
3. **图谱可解释性**：关系基的语义含义尚不直观。

### 适用性与场景分析
- **适用**：跨域图迁移、图基础模型预训练、需快速适配的图下游任务。
- **不适用**：强属性依赖、需密集图生成的任务。

## 我的综合评价

### 价值评分

#### 总体评分
**8.8/10** — 理论严谨（三大界）、方法优雅（摊销传输免迭代）、实验全面（14 任务 + 消融 + 扰动 + 可扩展性），是图基础模型方向的高质量工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 关系图谱坐标系 + 摊销传输的组合有范式意义 |
| 技术质量 | 9/10 | 三大理论界，数学严谨 |
| 实验充分性 | 9/10 | 14 任务 + 消融 + 拓扑扰动 + 可扩展性 |
| 写作质量 | 8/10 | 理论部分较密集 |
| 实用性 | 8/10 | 跨域迁移与推理加速价值明确 |

### 重点关注
- **值得关注的技术点**：ART 如何用单阶段 Sinkhorn 替代 GW 外循环；关系图谱基的可学习参数化。
- **需要深入理解的部分**：摊销覆盖界如何保证学习目标紧密代理理想关系覆盖。

## 相关论文

### 直接相关
- 自监督图预训练（GraphCL、JOAO 等）
- 图基础模型（AnyGraph、UniGraph 等）
- Gromov-Wasserstein 学习与摊销传输

### 后续工作
- 关系图谱在属性丰富图域的扩展

> [!tip] 关键启示
> 用"有限关系基构成的通用坐标系 + 摊销传输"替代逐样本对齐，是图基础模型实现统一表示与高效推理的关键路径。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐——图基础模型方向的理论与工程结合之作，跨域迁移价值突出。
