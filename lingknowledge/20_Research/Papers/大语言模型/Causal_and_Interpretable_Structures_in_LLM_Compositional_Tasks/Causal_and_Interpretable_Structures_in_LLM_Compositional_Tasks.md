---
date: "2026-09-30"
paper_id: "arXiv:2609.35970"
title: "Causal and Interpretable Structures in LLM Compositional Tasks"
authors: "Gurbir Arora, Toni J. B. Liu, Jiajun Bao, Raphaël Sarfati, Christopher J. Earls"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - 可解释性
  - Compositionality
  - Representation-Geometry
quality_score: "8.5/10"
related_papers: []
created: "2026-09-30"
updated: "2026-09-30"
status: analyzed
---

# Causal and Interpretable Structures in LLM Compositional Tasks

## 核心信息
- **论文ID**：arXiv:2609.35970
- **作者**：Gurbir Arora, Toni J. B. Liu, Jiajun Bao, Raphaël Sarfati, Christopher J. Earls
- **机构**：--
- **发布时间**：2026-09-28
- **会议/期刊**：cs.CL / cs.AI / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2609.35970) | [PDF](https://arxiv.org/pdf/2609.35970)

## 摘要翻译

### 英文摘要
Large language models are able to solve tasks whose answers depend on not only individual input tokens, but also on relations among them. How is such relational information represented and processed across transformer layers? We study activations from ensembles of prompts that require inferring relationships between three tokens corresponding to a cyclic concept (months, hours, weekdays, and musical notes) to correctly predict the next token. Across model families (Llama, Qwen, Gemma, and Mistral) and cyclic concepts, we find a consistent layerwise progression in how the joint dependence among the tokens is geometrically organized and causally used: intermediate layers use a joint representation based on the inferred relationship between two tokens, while later layers use a joint representation associated with all three tokens to correctly complete the task. We also find other relationships between tokens that are geometrically structured but remain causally inert in the next-token prediction. Crucially, when taken together, these geometric and causal investigations reveal the representation-level mechanism that progressively organizes and composes the relational information to form the answer. More surprisingly, restricting the models to such causally relevant joint representations improves next-token prediction accuracy.

### 中文翻译
大语言模型能够解决答案不仅取决于单个输入 token、还取决于 token 之间关系的任务。这种关系信息究竟是如何在 transformer 的各个层中被表征和处理的？作者研究了大量 prompt 集合上的激活，这些 prompt 需要推断三个 token（对应一个循环概念：月份、小时、星期、音符）之间的关系才能正确预测下一个 token。在多个模型家族（Llama、Qwen、Gemma、Mistral）和循环概念上，作者发现了一个一致的逐层递进规律：中间层使用基于"两个 token 之间推断出的关系"的联合表征，而更靠后的层则使用与"全部三个 token"相关联的联合表征来正确完成任务。作者还发现了其他几何上结构化、但在下一 token 预测中因果上"惰性"（inert）的 token 间关系。关键是，几何与因果两方面研究共同揭示了表征层面的机制——它逐步组织并组合关系信息以形成答案。更令人意外的是，把模型限制在这样"因果相关的联合表征"上，反而能提升下一 token 预测的准确率。

### 核心要点提炼
- **研究背景**：LLM 的组合（compositional）任务要求建模 token 之间的关系，而非仅 token 本身。
- **研究动机**：关系信息在 transformer 层间的表征几何与因果使用规律尚不明确。
- **核心方法**：构造循环概念（月份/小时/星期/音符）的三 token 关系推理任务，结合几何分析与因果分析（ablation）。
- **主要结果**：中间层用"两 token 关系"、后期层用"三 token 联合关系"的逐层递进；限制到因果相关表征可提升准确率。
- **研究意义**：揭示组合推理的表征层机制，并提出"几何结构 ≠ 因果作用"的关键区分。

## 研究背景与动机

### 领域现状
组合性（compositionality）被认为是智能的核心。LLM 在许多需要组合的任务上表现良好，但内部如何把"关系"编码进表征仍不清晰。已有工作多关注单 token 或 token 对的表征几何（如线性表征假说、probe 分析），但对"多 token 联合关系如何逐层组织并最终被因果使用"缺乏系统刻画。

### 现有方法的局限性
- 表征几何研究常止步于"结构是否存在"，未回答"该结构是否被下游因果使用"。
- 缺少在受控、可解释的任务（如循环概念）上跨模型家族的系统对比。
- "几何上结构化"与"因果上起作用"两个维度常被混淆。

### 研究动机
作者希望通过一个最小、可控、跨领域（月/时/周/音符）的关系推理任务，同时从**几何**与**因果**两个角度，回答"关系信息是如何被逐层组织、组合并最终驱动预测的"。

## 研究问题

### 核心研究问题
- 三 token 之间的联合关系，在 transformer 各层中是如何被几何组织的？
- 这些几何结构是否（以及在哪些层）被因果地用于下一 token 预测？
- "几何结构"与"因果作用"之间的关系是什么？

## 方法概述

### 核心思想
构造需要推断循环概念中三个 token 关系的任务，用**几何分析**（表征的联合依赖结构）与**因果分析**（ablation/intervention）双管齐下，定位关系信息在层间的"组织 → 组合 → 使用"递进过程，并揭示哪些结构真正驱动预测。

### 方法框架

#### 整体架构

![[figure_1_llama-3.1-8B_page1.png|700]]

> 图1：方法概览。对循环概念（月份、小时、星期、音符）构造三 token 关系推理任务，逐层分析表征几何（两 token / 三 token 联合依赖）与因果使用。

#### 各模块详细说明

**模块1：循环概念任务构造**
- **功能**：生成需要推断三 token 之间关系的 prompt 集合。
- **输入**：循环概念（月、时、周、音符）及其顺序关系。
- **输出**：大量 prompt，每个要求"已知两个/三个 token，预测下一个 token"。

**模块2：几何分析**
- **功能**：量化激活中"联合依赖"的结构。
- **输入**：各层激活。
- **输出**：两 token 关系 / 三 token 关系的几何组织方式（如联合表征的子空间）。
- **关键技术**：能量分数（energy fractions）、模式分数（mode fractions）等度量，刻画不同关系在表征中的占比。

**模块3：因果分析**
- **功能**：通过 ablation/intervention 判断哪些几何结构真正驱动预测。
- **输入**：各层表征。
- **输出**：因果相关的联合表征 vs 因果惰性的结构。
- **关键技术**：对关系子空间做移除/保留（ablation），观察下一 token 预测准确率变化。

### 方法架构图

![[figure_2_llama-3.1-8B_page1.png|700]]

> 图2：逐层递进的组织方式示意。中间层以"两 token 关系"为主，后期层转向"三 token 联合关系"，后者才是完成任务的关键。

### 关键创新
1. **几何 × 因果的联合分析框架** - 不止问"结构在哪"，更问"结构是否被使用"。
2. **跨模型家族的一致性发现** - 在 Llama/Qwen/Gemma/Mistral 上均观察到一致的逐层递进。
3. **"因果相关表征提升准确率"的反直觉结论** - 限制模型只使用因果相关的联合表征，反而提升预测，说明部分表征是"噪声"。

## 实验结果

### 数据集
- 循环概念任务：月份、小时、星期、音符（各含固定顺序关系的 token 集合）。

### 实验设置
- **基线方法**：原始模型全表征的下一 token 预测。
- **评估指标**：下一 token 预测准确率。
- **实验环境**：Llama、Qwen、Gemma、Mistral 四个模型家族，逐层分析。

### 主要结果

| 层阶段 | 联合表征类型 | 因果作用 |
|--------|-------------|----------|
| 中间层 | 两 token 关系 | 部分因果相关 |
| 后期层 | 三 token 联合关系 | 因果关键，完成任务 |
| 其他 token 关系 | 几何结构化 | 因果惰性（inert） |

> 注：限制模型到"因果相关的联合表征"可提升下一 token 预测准确率。

### 实验结果图

![[figure_3_llama-3.1-8B_page1.png|700]]

> 图3：能量/模式分数随层的变化，展示两 token 关系与三 token 关系的此消彼长，以及因果相关与因果惰性结构的区分。

## 深度分析

### 研究价值
- **理论贡献**：为 LLM 的组合推理提供了"逐层组织关系信息"的清晰机制图景，并把"几何结构"与"因果作用"解耦。
- **实际应用**：对理解模型如何做组合推理、诊断其失败模式有指导意义。
- **领域影响**：是表征几何与机制可解释性交叉方向的重要工作。

### 优势
- 受控、可解释的任务设计，结论干净。
- 跨四模型家族，稳健性强。
- 几何与因果结合，方法论完整。

### 局限性
- 循环概念任务相对简单，能否泛化到任意组合推理待验证。
- "因果惰性"结构的生物学/计算意义尚未充分解释。
- 结论集中在中间表征，未触及训练动态如何形成这种结构。

### 适用场景
- 研究 LLM 组合推理、表征几何、机制可解释性的工作。
- 设计"只使用因果相关子空间"以提升推理效率/准确率的干预。

## 与相关论文对比

### 相关论文 - 线性表征假说（Linear Representation Hypothesis）
- **差异**：线性表征假说关注概念/特征的线性编码，本文关注多 token 联合关系的逐层组织。
- **改进**：从"单概念线性编码"升级到"关系结构的逐层组合"。

### 相关论文 - 机制可解释性（电路分析）
- **差异**：电路分析常定位具体头/MLP，本文从表征几何层面刻画关系组合。
- **改进**：提供与电路分析互补的"表征层"证据。

## 技术路线定位

本文属于**表征几何 + 机制可解释性**技术路线，主要关注 **组合推理的表征组织**子方向。核心特点是：用受控循环概念任务，同时从几何与因果两个维度刻画"关系信息如何被逐层组织并最终驱动预测"。

## 未来工作建议

1. 扩展到更复杂、更自然的组合任务，验证逐层递进规律的一般性。
2. 研究训练过程中这种"几何组织"是如何涌现的。
3. 探索"因果相关子空间"在推理加速、模型压缩中的应用。

## 我的综合评价

### 价值评分
- **总体评分**：8.5/10
- **分项评分**：
  - 创新性：8/10（几何×因果联合框架有亮点，循环概念任务精巧）
  - 技术质量：9/10（方法严谨，跨模型对比充分）
  - 实验充分性：8/10（受控任务充分，自然任务泛化可加强）
  - 写作质量：9/10（清晰）
  - 实用性：7/10（理论价值高，直接应用有限）

### 突出亮点
- "几何结构 ≠ 因果作用"的关键区分。
- 限制到因果相关表征反而提升准确率的反直觉结论。

### 可借鉴点
- 用受控循环概念任务做可解释性研究的设计。
- 几何分析与因果分析并重的方法论。

### 批判性思考
- 循环概念任务是否过于理想化，掩盖了真实组合推理的复杂性？
- "因果惰性"结构是否在其他任务/模型中承担了被忽视的功能？

## 我的笔记

%% 用户可以在这里添加个人阅读笔记 %%

## 相关论文
- [[In-Context_Learning_Amplifies_a_Latent_Symbolic_Circuit|In-Context Learning Amplifies a Latent Symbolic Circuit]] - 同为 LLM 内部机制（电路）的因果可解释性研究

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.35970)
- [PDF](https://arxiv.org/pdf/2609.35970)
