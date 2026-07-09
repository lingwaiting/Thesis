---
date: "2026-07-09"
paper_id: "arXiv:2607.00293"
title: "Rosetta: Composable Native Multimodal Pretraining"
authors: "Xiangyue Liu 等 (6位作者)"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态技术
  - MoE
  - 灾难性遗忘
  - 预训练
  - 梯度冲突
  - 模态扩展
quality_score: "8.0/10"
created: "2026-07-09"
updated: "2026-07-09"
status: analyzed
---

# Rosetta: Composable Native Multimodal Pretraining

## 核心信息
- **论文ID**：arXiv:2607.00293
- **作者**：Xiangyue Liu 等 (6位作者)
- **机构**：--
- **发布时间**：2026-07-01
- **会议/期刊**：arXiv preprint
- **链接**：[arXiv](https://arxiv.org/abs/2607.00293) | [PDF](https://arxiv.org/pdf/2607.00293)
- **分类**：cs.CV, cs.CL, cs.LG
- **项目页面**：https://rosetta-lmm.github.io/

## 摘要翻译

### 英文摘要
Achieving true artificial general intelligence requires foundation models capable of integrating new modalities without forgetting prior knowledge. However, accommodating continuous generative objectives alongside discrete understanding tasks causes severe gradient conflicts. Existing architectures, including standard Mixture-of-Experts (MoE), are highly susceptible to representation overwriting. Even structurally partitioned paradigms like Mixture-of-Transformers (MoT) remain vulnerable to catastrophic forgetting, severely impeding multimodal scalability.

In this work, we introduce Rosetta, a composable native multimodal pretraining framework designed for seamless and non-destructive modality expansion. Rosetta adopts a modular paradigm where core foundational knowledge is preserved within global shared experts, while modality-specific capabilities are distributed across plug-and-play experts. To guarantee non-destructive composition, we propose Momentum-Anchored Orthogonal Projection (MAOP). MAOP leverages the optimizer's momentum state as an implicit semantic anchor, selectively neutralizing conflicting gradient components from new modalities while preserving synergistic updates.

Extensive evaluations demonstrate that, while standard MoE and MoT architectures suffer catastrophic forgetting of previously acquired knowledge, Rosetta robustly preserves established language and visual understanding. Furthermore, it delivers superior image generation and unlocks cross-modal synergy, paving the way for truly composable and unified multimodal foundation models.

### 中文翻译
实现真正的通用人工智能需要能够在不遗忘已有知识的前提下整合新模态的基础模型。然而，同时处理连续的生成目标和离散的理解任务会导致严重的梯度冲突。现有架构，包括标准的混合专家（MoE），极易遭受表征覆盖。即使是结构分区的范式如混合Transformer（MoT），仍然容易受到灾难性遗忘的影响，严重阻碍了多模态可扩展性。

在这项工作中，我们提出了Rosetta，一个面向无缝、无破坏性模态扩展的可组合原生多模态预训练框架。Rosetta采用模块化范式：核心基础知识和能力保存在全局共享专家中，而模态特定能力分布在一系列即插即用的专家中。为保证无破坏性组合，我们提出了动量锚定正交投影（MAOP），利用优化器的动量状态作为隐式语义锚点，选择性地中和来自新模态的冲突梯度分量，同时保留协同更新。

大量实验表明，标准MoE和MoT架构遭受了严重的灾难性遗忘，而Rosetta鲁棒地保留了已有的语言和视觉理解能力，同时实现了更优的图像生成和跨模态协同效应，为真正可组合的统一多模态基础模型铺平了道路。

### 核心要点提炼
- **研究背景**：多模态基础模型在扩展新模态时会灾难性遗忘已有能力
- **研究动机**：现有MoE/MoT架构无法解决新模态引入时的梯度冲突和表征覆盖
- **核心方法**：模块化MoE架构 + 动量锚定正交投影（MAOP），以零额外内存开销实现无破坏性模态扩展
- **主要结果**：在防止遗忘的同时实现更好的图像生成质量和跨模态协同
- **研究意义**：为可组合多模态AGI提供了新的架构范式

## 研究背景与动机

### 领域现状
当前多模态基础模型的发展面临一个核心矛盾：一方面我们希望模型能持续整合新模态（从文本→图像→视频→音频...），另一方面每次新增模态都会通过梯度冲突"覆盖"已学到的知识。现有的应对方案包括：

1. **标准MoE**：通过路由机制选择性激活专家，但不同模态的梯度仍会在共享参数空间中冲突
2. **混合Transformer（MoT）**：为不同模态使用不同的transformer块，结构上做了隔离，但仍然面临灾难性遗忘

### 现有方法的局限性
- **梯度冲突**：理解和生成任务的梯度方向天然存在矛盾，统一优化困难
- **表征覆盖**：新模态训练时会覆盖共享层中已有的表征
- **缺乏遗忘防护机制**：现有架构没有显式的机制来防止旧知识丢失

### 研究动机
需要一个既能保留核心能力、又能灵活扩展新模态的架构框架，类似于"操作系统"的即插即用理念。

## 研究问题

### 核心研究问题
如何设计一个多模态预训练框架，使得添加新模态时：
1. 不遗忘已有的语言和视觉理解能力
2. 新模态能力能够有效学习
3. 不同模态之间产生正向协同效应

## 方法概述

### 核心思想
Rosetta将"核心知识"和"模态能力"解耦：全局共享专家像"操作系统内核"一样保留所有模态共享的基础能力，而即插即用的模态专家像"驱动程序"一样处理特定模态功能。MAOP技术利用优化器已有的动量信息（零额外开销）来判断哪些梯度分量是"冲突的"，并将其投影到正交方向消除干扰。

### 方法框架

#### 整体架构

![[pipeline_page1.png|800]]

> 图1：Rosetta整体架构——全局共享专家保留核心知识，即插即用的模态专家处理特定能力，MAOP保证无破坏性组合

#### 各模块详细说明

**模块1：全局共享专家（Global Shared Experts）**
- **功能**：保留跨所有模态共享的核心知识和基础能力
- **输入**：所有模态的token表示
- **输出**：增强的通用表征
- **关键特性**：在所有训练阶段均被激活，确保核心能力始终得到维护

**模块2：即插即用模态专家（Plug-and-Play Experts）**
- **功能**：处理特定模态的能力（如专门的图像理解专家、生成专家等）
- **输入**：特定模态的token表示
- **输出**：模态特定的增强表征
- **关键特性**：可动态添加/移除，新增模态不干扰已有专家

**模块3：动量锚定正交投影（MAOP）**
- **功能**：在梯度层面防止新模态训练破坏已有知识
- **核心机制**：
  1. 利用优化器动量状态作为"语义锚点"——动量方向代表了已学知识的重要性方向
  2. 检测新模态梯度中与锚点方向冲突的分量
  3. 将冲突分量投影到正交方向，保留协同分量
- **关键优势**：零额外内存开销（动量是优化器已有的），无需额外存储或计算

![[MAOP-new_page1.png|800]]

> 图2：MAOP机制示意——利用动量锚点选择性中和冲突梯度分量

### 方法架构图

![[teaser_page1.png|800]]

> 图3：Rosetta的设计理念——像操作系统的即插即用驱动一样扩展模态能力

## 实验结果

### 实验目标
验证Rosetta在以下方面的表现：(1) 防止灾难性遗忘；(2) 图像生成质量；(3) 跨模态协同效应

### 主要结果

#### 灾难性遗忘防护
- 标准MoE和MoT架构在添加新模态后，已有能力显著退化
- Rosetta鲁棒地保留了已有的语言理解和视觉理解能力
- 验证了MAOP在防止表征覆盖方面的关键作用

#### 图像生成质量
![[t2i_loss_page1.png|800]]

> 图4：文本到图像生成损失曲线对比

#### 跨模态协同
- Rosetta不仅防止了遗忘，还实现了正向的跨模态协同
- 新模态的添加促进了已有模态能力的进一步提升

### 消融实验

![[ab_expert_num_page1.png|800]]

> 图5：专家数量消融实验——验证架构设计的合理性

## 深度分析

### 研究价值评估

#### 理论贡献
- **可组合多模态架构范式**：首次将"核心-插件"架构理念系统性地引入多模态预训练
- **MAOP梯度正交投影**：巧妙利用优化器动量作为语义锚点，零额外开销解决梯度冲突
- **灾难性遗忘的架构级解决方案**：从梯度层面而非数据层面（如重放）解决遗忘问题

#### 实际应用价值
- **灵活的模态扩展**：实际部署中可以按需添加新模态能力，无需重新训练整个模型
- **零额外开销**：MAOP不增加内存或计算开销，易于集成到现有训练框架
- **工业友好的设计**：模块化架构便于维护和迭代

### 方法优势详解
1. **非破坏性扩展**：核心创新，解决了多模态扩展中最根本的问题
2. **零开销梯度管理**：MAOP利用已有动量信息，无需额外存储
3. **即插即用的灵活性**：模态专家可独立开发和部署

### 局限性分析
1. **未见模态数量上限实验**：不清楚扩展到极多模态时架构是否仍稳定
2. **缺乏更大规模验证**：实验规模相对有限，需要更大模型验证
3. **MAOP的超参数敏感性**：正交投影的强度可能需要针对不同模态对进行调整
4. **模态间路由策略未深入探索**：如何最优地将token路由到正确的专家组合

### 适用性与场景分析
- **适用场景**：需要持续扩展新模态的大规模多模态预训练项目
- **不适用场景**：单模态或双模态的简单场景（过度设计）

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 架构设计优雅，MAOP技术巧妙实用，但缺乏大规模验证

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "核心-插件"架构理念在多模态预训练中的创新应用，MAOP巧妙的零开销设计 |
| 技术质量 | 8/10 | 方法设计严谨，理论motivation清晰，工程实现可行 |
| 实验充分性 | 7/10 | 主要实验覆盖关键维度，但大规模验证和模态数量扩展实验不足 |
| 写作质量 | 8/10 | 结构清晰，motivation和contribution表述明确 |
| 实用性 | 9/10 | 即插即用设计极具工业价值，零额外开销降低落地门槛 |

### 重点关注
- **MAOP的梯度管理机制**：值得深入理解其数学原理和实现细节
- **专家路由策略**：全局专家与模态专家的协同激活模式

> [!tip] 关键启示
> 利用优化器已有的动量状态作为知识重要性锚点，以零额外成本实现梯度冲突管理——这种"四两拨千斤"的设计思想值得在其他领域借鉴。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐关注多模态架构和高效训练的读者阅读！

---

## 相关论文
- [[20_Research/Papers/大语言模型/Switch_Transformers|Switch Transformers]] - MoE架构基础
- [[20_Research/Papers/多模态技术/Transferability_Between_Understanding_and_Generation_in_Unified_Multimodal_Models|Transferability UMM]] - 统一多模态模型中的能力迁移（同月论文，互补视角）
