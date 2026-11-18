---
date: "2026-11-18"
paper_id: "arXiv:2611.21608"
title: "Latent Lattice Language Models"
authors: "Shivam Duggal, Zongze Wu, William T. Freeman, Antonio Torralba, Richard Zhang, Eli Shechtman"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 语言模型架构
  - 残差流
  - 循环状态
  - 计算容量
quality_score: "9.3/10"
related_papers: []
created: "2026-11-18"
updated: "2026-11-18"
status: analyzed
---

# Latent Lattice Language Models

## 核心信息
- **论文ID**：arXiv:2611.21608
- **作者**：Shivam Duggal, Zongze Wu, William T. Freeman, Antonio Torralba, Richard Zhang, Eli Shechtman
- **机构**：MIT CSAIL（麻省理工学院）；Adobe
- **发布时间**：2026-11-16
- **会议/期刊**：ICLR（iclr 模板）
- **链接**：[arXiv](https://arxiv.org/abs/2611.21608) | [PDF](https://arxiv.org/pdf/2611.21608)
- **引用**：--

## 摘要翻译

### 英文摘要
Language models typically increase per-token computation through deeper or looped layers and latent reasoning steps, while richer residual connections improve information flow. The authors instead increase **computational capacity**: the number of active computational states a model maintains. They introduce the "Lattice of Latents" — a set of per-layer horizontal latent streams that run recurrently along time, alongside the vertical residual stream through depth. These latents directly condition the vertical stream's attention, KV-cache writes, MLP computation, and decoding. A 406M-parameter Lattice model lowers validation perplexity from 15.41 to 14.40 over a parameter-matched Transformer on 20B FineWeb-Edu tokens, composing with prior residual-stream enhancements to reach 13.61.

### 中文翻译
语言模型通常通过更深的层、循环层或潜在推理步骤来增加逐 token 计算量，而更丰富的残差连接则改善跨层信息流。作者反其道而行，转而提升**计算容量**——即模型维持的活跃计算状态的数量。作者提出「潜在晶格」（Lattice of Latents）：一组逐层的**水平**潜在流，随时间循环运行，与沿深度流动的**垂直**残差流并存。这些潜在状态直接条件化垂直流的注意力、KV 缓存写入、MLP 计算与解码。在 20B FineWeb-Edu token 上训练，406M 参数的 Lattice 模型将验证困惑度从 15.41 降至 14.40（相对等参数 Transformer），且能与既有残差流增强（hyper-connections、输入残差）组合，进一步降至 13.61。

### 核心要点提炼
- **研究背景**：提升 LM 计算能力的传统路径是「加深/循环」或「强化残差连接」。
- **研究动机**：现有方法仍受限于「单一潜在状态承担所有角色」的瓶颈。
- **核心方法**：增加逐层的水平潜在流，提供随 token 持久化的「伴生计算状态」，直接条件化垂直流。
- **主要结果**：等参数下验证困惑度 15.41→14.40；组合残差增强后 13.61；在 Gated DeltaNet、Mamba-2 等混合架构上普遍有效。
- **研究意义**：确立「持久逐层状态」作为 LM 计算扩展的**互补新轴**。

## 研究背景与动机

### 领域现状
语言模型通过两种主流方式提升逐 token 计算量：沿**深度**（更深/循环层）与沿**时间**（潜在推理步骤、状态持久化）。同时，超连接（hyper-connections）、mHC、注意力/值残差等残差流增强工作改善了跨层信息流。

### 现有方法的局限性
- 无论加深、循环还是残差增强，模型仍把全部活跃计算压在一个「单一潜在状态」上，该状态要同时负责查询记忆、写 KV 缓存、产生残差更新、解码下一个 token，形成瓶颈。
- 混合 Transformer-SSM 架构的循环状态主要作为「序列混合的内部记忆」，而非直接条件化垂直流的计算状态。

### 研究动机
作者提出一个新维度：**计算容量**（活跃计算状态的数量）。通过给每个 token 增加一组随时间的持久化水平潜在状态，让垂直流的各个组件（注意力、KV、MLP、解码）都得到额外的计算条件化，从而打破单一潜在状态的瓶颈。

## 研究问题

### 核心研究问题
能否通过增加「活跃计算状态的数量」（而非深度或循环），为语言模型引入一条**互补的计算扩展轴**，从而在等参数、等数据下稳定降低困惑度？

## 方法概述

### 核心思想
在标准 Transformer 的垂直残差流之外，引入「潜在晶格」（Lattice of Latents）：每层一组**水平潜在流**，随时间循环运行并跨 token 持久化。与 Transformer-SSM 混合架构不同，这些潜在状态不单是记忆，而是**直接条件化**垂直流的注意力、KV 缓存写入、MLP 计算与解码。

### 方法框架

#### 整体架构

![[teaser_flow_2_page1.png|800]]

> 图1：Latent Lattice 示意图——垂直残差流（沿深度）与水平潜在流（沿时间）并存，水平流为垂直流的各组件提供额外计算条件化。

![[residual_stream_comparison_page1.png|800]]

> 图2：残差流结构对比——Latent Lattice 与 hyper-connections、mHC、注意力/值残差等既有增强方式的关系。

#### 各模块详细说明

**模块1：垂直残差流（Vertical Residual Stream）**
- **功能**：标准 Transformer 的信息流，沿深度传播。
- **作用**：承载注意力、MLP、KV 缓存写入与解码。

**模块2：水平潜在流（Horizontal Latent Streams / Lattice of Latents）**
- **功能**：每层一组潜在状态，随时间循环运行、跨 token 持久化。
- **关键技术**：这些潜在状态**直接条件化**垂直流的注意力、KV 写入、MLP 计算与解码。

**模块3：组合机制（Composition）**
- **功能**：与既有残差流增强（hyper-connections、输入残差）正交组合。
- **效果**：可叠加，进一步降低困惑度。

### 关键创新
1. **新扩展轴**：将「活跃计算状态数量」确立为独立于深度/时间的 LM 扩展维度。
2. **主动条件化**：潜在状态不仅是记忆，更直接参与垂直流的注意力、KV、MLP、解码计算。
3. **架构无关性**：在 Transformer、Gated DeltaNet、Mamba-2、Triadic GDN、GDN-Mamba-2 等上普遍有效，且能媲美 4× 更大原生状态的纯基线。

## 实验结果

### 数据集
- **FineWeb-Edu（20B tokens）**：预训练
- 下游任务：0-shot / 5-shot 评测

### 实验设置
- **主模型**：406M 参数 Lattice Transformer，对比等参数 Transformer。
- **基线**：Transformer、Transformer + hyper-connections + 输入残差。
- **混合架构**：Gated DeltaNet、Mamba-2、Triadic GDN、GDN-Mamba-2。
- **评估指标**：验证困惑度（PPL）、下游任务准确率。

### 主要结果

![[recurrent_backbone_fineweb_resutls_page1.png|800]]

> 图3：各循环骨干上的困惑度对比——Lattice 变体在每个原生状态尺寸下均降低困惑度。

| 模型 | 验证 PPL |
|------|----------|
| Transformer（基线） | 15.41 |
| Transformer + Lattice | **14.40** |
| Transformer + 残差增强 | 14.22 |
| Transformer + 残差增强 + Lattice | **13.61** |

- **组合性**：Lattice 与 hyper-connections、输入残差正交组合，13.61 刷新最优。
- **混合架构**：Lattice-GDN、Lattice-Triadic-GDN 在每个原生状态尺寸下均优于纯基线，且媲美 4× 更大原生状态的纯模型。

![[paired_backbones_task_accuracy_best_variant_clean_0shot_page1.png|800]]

> 图4：下游任务 0-shot 准确率对比——Lattice 骨干在任务上保持或提升准确率。

### 结果分析
- 「持久逐层状态」是独立于深度/时间的**互补**扩展轴，与残差流增强叠加仍能带来增益。
- Lattice 通过为垂直流各组件提供额外条件化，而非简单的记忆扩展，实现了稳定的困惑度下降。

## 深度分析

### 研究价值评估
- **理论贡献**：提出「计算容量 = 活跃计算状态数量」的新视角，系统化区分了记忆型状态与计算型状态。
- **实际应用**：为设计下一代高效 LM 架构提供了新的扩展维度与组合范式。
- **领域影响**：可能推动「残差流增强」与「循环状态」两条研究线的融合。

### 优势
1. 视角新颖（从「加深/循环」转向「增加活跃状态数」）。
2. 结论普适（跨 Transformer、SSM、混合架构验证）。
3. 与现有残差增强正交，实用可叠加。

### 局限性
1. 主实验在 406M 规模 / 20B tokens 上，尚未验证更大规模（>1B）的扩展性。
2. 下游任务评测主要为 0/5-shot，未见 RLHF/指令微调后的表现。
3. 水平潜在流带来的额外参数量与计算开销的性价比未做详尽消融。

## 与相关论文对比

### 对比论文选择依据
围绕「残差流增强」与「循环/混合架构」两条技术线选取。

### Hyper-connections / mHC（残差流增强）
- **核心方法**：沿深度重路由或增强残差连接。
- **关系类型**：正交组合。
- **本文改进**：Lattice 从「时间维度」引入持久状态，与深度方向的残差增强互补，叠加后 13.61。

### Transformer-SSM 混合架构（Gated DeltaNet / Mamba-2）
- **核心方法**：引入循环状态作为序列混合的内部记忆。
- **关系类型**：本文的验证载体之一。
- **本文改进**：指出其循环状态主要是「记忆」，而 Lattice 的状态直接条件化计算，定位更主动。

## 技术路线定位

### 所属技术路线
- **语言模型架构设计**
- **残差流与计算容量扩展**

### 未来方向
- 在更大规模（1B–7B+）验证扩展性与效率。
- 探索 Lattice 状态与 KV 缓存压缩、稀疏注意力的结合。
- 将「计算容量」框架推广到多模态与扩散模型架构。
