---
date: "2026-10-04"
paper_id: "arXiv:2610.02186"
title: "Higher-Order Molecular Grammars for Generative and Foundation Models in Chemistry"
authors: "Yiming Huang, Yujie Zeng, Vijay Prakash Dwivedi, Simone Foti, Jianmin Wang, Jure Leskovec, Tolga Birdal"
domain: "大模型"
tags:
  - 论文笔记
  - 大模型
  - Foundation-Model
  - 分子生成
  - 表征学习
  - 组合复形
  - 高阶文法
quality_score: "8.5/10"
related_papers: []
created: "2026-10-04"
updated: "2026-10-04"
status: analyzed
---

# Higher-Order Molecular Grammars for Generative and Foundation Models in Chemistry

## 核心信息
- **论文ID**：arXiv:2610.02186
- **作者**：Yiming Huang, Yujie Zeng, Vijay Prakash Dwivedi, Simone Foti, Jianmin Wang, Jure Leskovec, Tolga Birdal
- **机构**：--
- **发布时间**：2026-10-01
- **会议/期刊**：分类 cs.LG, cs.AI
- **链接**：[arXiv](https://arxiv.org/abs/2610.02186) | [PDF](https://arxiv.org/pdf/2610.02186)

## 摘要翻译

### 英文摘要
Molecular learning models are strongly shaped by their underlying representations. Yet standard sequential and graph formalisms struggle to explicitly encode higher-order topology, such as ring systems and recurring motifs. Existing higher-order representations can capture these structures directly, but they are often computationally demanding and difficult to decode into valid molecules. Here, we introduce Higher-order Grammar Representation (HGR), a principled, topology-aware framework that lifts molecules to combinatorial complexes and parses each complex into a compact sequence of production rules under a context-free higher-order grammar.

### 中文翻译
分子学习模型深受其底层表征形式的影响。然而，标准的序列化与图形式化难以显式编码高阶拓扑结构（如环体系与重复出现的 motifs）。现有的高阶表征虽能直接捕捉这些结构，但往往计算开销大且难以解码为有效分子。本文提出高阶文法表征（Higher-order Grammar Representation, HGR），一个原理清晰、拓扑感知的框架：将分子提升为组合复形（combinatorial complex），并在上下文无关的高阶文法下，将每个复形解析为一条紧凑的产生式规则序列。

### 核心要点提炼
- **研究背景**：分子学习的表征形式决定模型能力，但标准序列/图表征难以显式编码环体系与 motifs 等高阶拓扑。
- **研究动机**：现有高阶表征计算开销大、难以解码为有效分子。
- **核心方法**：HGR —— 将分子提升为组合复形，用高阶文法序列化为产生式规则序列，使高阶拓扑直接兼容标准序列模型。
- **主要结果**：分子生成上 100% 合法率 + 领先的分布对齐（FCD 五个基准全部第一）；表征学习上 MoleculeNet 七个基准平均 AUC 最高。
- **研究意义**：为分子生成与可迁移表征学习提供高效、拓扑表达的高阶表征框架。

## 研究背景与动机

### 领域现状
- 分子学习模型（生成模型与 foundation model）的能力受底层分子表征形式制约。
- 主流表征为序列化（如 SMILES）或图（原子-键图），均难以显式表达环体系、重复 motifs 等**高阶拓扑结构**。

### 现有方法的局限性
- 已有的高阶表征（如 hypergraph、cell complex）能直接捕捉高阶拓扑，但**计算开销大**，且**难以解码回有效分子**。
- 现有基准偏向简单环体系，掩盖了高阶表征的差异化优势。

### 研究动机
能否设计一种既保留高阶拓扑表达力、又可直接用标准序列模型处理、且解码天然合法的表征？

## 研究问题

### 核心研究问题
如何将分子的高阶拓扑结构（环体系、motifs）以高效、可解码的方式编码，使其与标准序列模型兼容，从而提升分子生成与表征学习的质量？

## 方法概述

### 核心思想
HGR 的关键在于"文法序列化"：把分子**提升为组合复形**以显式表达高阶拓扑，再在上下文无关的高阶文法下将复形**解析为紧凑的产生式规则序列**。这样，高阶拓扑被"序列化"，可直接送入标准序列模型，既避免了显式高阶编码的计算开销，又保留了拓扑表达力。

### 方法框架

#### 整体架构
![[2610.02186_page1.png|600]]

> 图1：HGR 整体示意 —— 分子 → 组合复形 → 高阶文法 → 产生式规则序列。

![[HGR-frame1d_page1.png|600]]

> 图2：HGR 的 1D 框架，展示从分子到文法规则序列的解析过程。

- **组合复形提升**：将分子提升为组合复形，显式编码环体系与 motifs 等高阶拓扑。
- **高阶文法解析**：用上下文无关的高阶文法，把复形解析为紧凑的产生式规则序列。
- **序列模型兼容**：规则序列可直接由标准序列模型（生成/表征）处理，解码 100% 合法。
- **RingDiv 基准**：构建含 118 万分子的环富集基准（含 RingDiv300k 子集）与环多样性指数 RDI，缓解基准对简单环体系的偏置。

### 关键创新
1. **高阶文法表征 HGR** —— 用"组合复形 + 高阶文法序列化"统一高阶拓扑表达与序列模型兼容性。
2. **RingDiv 基准与 RDI 指标** —— 提供环富集的分子基准，量化环体系覆盖度。
3. **100% 合法 + 领先分布对齐** —— 生成上天然合法，同时保持分布对齐能力。

## 实验结果

### 数据集
- **分子生成**：五个生成基准（FCD 评估）
- **表征学习**：MoleculeNet 七个基准（probe 与 full fine-tuning 两种迁移协议）

### 主要结果
- 分子生成：HGR 模型在五个生成基准上 FCD 全部第一，且 100% 合法。
- 表征学习：HGR-FM 在 MoleculeNet 七个基准平均 AUC 最高，相对最强基线在 probing 下提升 8.3 分、full fine-tuning 下提升 3.3 分。

## 深度分析

### 研究价值
- **理论贡献**：将组合复形与上下文无关高阶文法结合，给出"拓扑感知 + 序列兼容 + 天然合法"的统一表征范式。
- **实际应用**：HGR 可直接用于分子生成、分子 foundation model 的预训练表征。
- **领域影响**：为 chemistry 领域的 foundation model 提供了一种有原则的输入表征选择。

### 优势
- 生成合法率 100% 由构造保证，无需事后校正。
- 避免显式高阶编码的高计算开销。
- 附带基准（RingDiv）与指标（RDI）可推动领域公平评测。

### 局限性
- 文法设计对分子空间覆盖的完备性仍需更系统验证。
- 与大规模语言模型（化学 LLM）的融合路径尚未充分探索。

### 未来工作
- 将 HGR 与化学 foundation model / 多模态模型结合，探索更大规模预训练。
- 扩展文法以覆盖更复杂的化学空间（如立体化学、反应）。
