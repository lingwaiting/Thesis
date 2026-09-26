---
date: "2026-09-26"
paper_id: "arXiv:2609.30238"
title: "SemMSA: Latent Semantic-Aided Robust Multimodal Sentiment Analysis with Incomplete Data"
authors: "Wenhao Li, Zhibin Wu, Chong Xiao, Qiangchang Wang"
domain: "多模态"
tags:
  - 论文笔记
  - Multimodal-Sentiment-Analysis
  - Incomplete-Data
  - Spectral-Alignment
  - Latent-Semantic
  - LLM
quality_score: "8.0/10"
created: "2026-09-26"
updated: "2026-09-26"
status: analyzed
---

# SemMSA: Latent Semantic-Aided Robust Multimodal Sentiment Analysis with Incomplete Data

## 核心信息
- **论文ID**：arXiv:2609.30238
- **作者**：Wenhao Li, Zhibin Wu, Chong Xiao, Qiangchang Wang
- **机构**：--
- **发布时间**：2026-09-24
- **会议/期刊**：arXiv 预印本（cs.CL, cs.CV, cs.MM）
- **链接**：[arXiv](http://arxiv.org/abs/2609.30238v1) | [PDF](https://arxiv.org/pdf/2609.30238v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Recent research on Multimodal Sentiment Analysis (MSA) has focused on learning from language, visual, and acoustic modalities with incomplete data to infer human sentiment. Most studies typically compensate for missing information by reconstructing modality features or designing complicated fusion mechanisms. However, these methods still suffer from spurious generation and noisy guidance due to the lack of high-level semantic grounding in partially observed multimodal evidence. To address these issues, we propose SemMSA, a latent semantic-aided framework that constructs rich sentiment-relevant semantics with LLMs, fully integrating with all modalities via anchor-free spectral alignment. It mainly consists of Cross-modal Semantic Refinement (CSR) and Cross-modal Spectral Alignment (CSA). Specifically, CSR first adaptively extracts visual and acoustic representations by corresponding adapters to form a unified multimodal prefix with language in the frozen LLM embedding space. It then iteratively produces continuous discriminative semantic states through a token-efficient latent refinement process without decoding explicit text. Next, CSA simultaneously aligns the refined semantics with all modalities by enhancing the dominant spectral component of their kernel Gram matrix. This captures global nonlinear dependencies among all representations without relying on a predefined anchor modality. In addition, an instance-level spectral separation constraint preserves cross-sample discriminability and mitigates representation collapse. Extensive experiments on SIMS, MOSI, and MOSEI benchmarks demonstrate that SemMSA achieves state-of-the-art performance.

### 中文翻译
近年来多模态情感分析（MSA）的研究聚焦于在数据不完整的情况下，从语言、视觉和声学三种模态中学习以推断人类情感。多数研究通常通过重构缺失模态特征或设计复杂的融合机制来补偿缺失信息。然而，这些方法由于缺乏对部分观测多模态证据的高层语义锚定，仍然存在伪生成（spurious generation）与噪声引导（noisy guidance）的问题。为解决这些问题，本文提出 SemMSA，一个潜在语义辅助的框架，利用 LLM 构建丰富的情感相关语义，并通过无锚点的谱对齐（anchor-free spectral alignment）与所有模态充分融合。它主要由跨模态语义精炼（CSR）和跨模态谱对齐（CSA）构成。具体地，CSR 首先通过相应的适配器自适应地提取视觉和声学表示，在冻结的 LLM 嵌入空间中与语言形成统一的多模态前缀；随后通过一种 token 高效、无需解码显式文本的潜在精炼过程，迭代地产生连续且有判别力的语义状态。接着，CSA 通过增强各表示核 Gram 矩阵的主导谱分量，将精炼语义与所有模态同时对齐，在不依赖预定义锚定模态的情况下捕获所有表示之间的全局非线性依赖。此外，实例级谱分离约束保持了跨样本判别性并缓解表示坍缩。在 SIMS、MOSI 和 MOSEI 基准上的大量实验表明，SemMSA 达到了最先进性能。

### 核心要点提炼
- **研究背景**：多模态情感分析需从语言/视觉/声学三模态推断情感，真实场景常面临模态缺失
- **研究动机**：现有方法重构缺失特征或复杂融合，但缺乏高层语义锚定，产生伪生成与噪声引导
- **核心方法**：CSR（LLM 潜在语义精炼，不显式解码文本）+ CSA（无锚点核 Gram 矩阵谱对齐）
- **主要结果**：在 SIMS、MOSI、MOSEI 三个基准上达到 SOTA
- **研究意义**：用 LLM 潜在语义 + 谱对齐解决不完备多模态情感分析的高层语义缺失问题

## 研究背景与动机

### 领域现状
多模态情感分析（MSA）利用文本、视觉、声学三种模态识别人类情感。真实应用中常出现模态缺失（如无视频、无音频），因此"不完备数据下的 MSA"成为关键问题。主流做法是特征重构（重建缺失模态）或设计复杂融合网络。

### 现有方法的局限性
- **伪生成**：重构缺失模态特征时，模型可能生成与真实情感无关的伪特征
- **噪声引导**：缺乏高层语义锚定，重构特征会引入噪声，误导融合
- **复杂融合机制脆弱**：依赖预定义锚定模态（常以文本为锚），锚模态缺失时性能骤降
- **缺乏高层语义 grounding**：部分观测的多模态证据缺少统一的语义锚点

### 研究动机
能否借助 LLM 的丰富语义知识，为不完备的多模态证据提供高层语义锚定，并摆脱对预定义锚定模态的依赖？

## 研究问题

### 核心研究问题
如何在不完备多模态数据下，构建高层、情感相关的潜在语义，并以无锚点的方式将其与所有可用模态鲁棒地对齐？

## 方法概述

### 核心思想
用冻结 LLM 的嵌入空间构建情感相关的潜在语义状态（不显式解码文本），再通过核 Gram 矩阵的谱对齐，将潜在语义与所有模态在无锚点条件下对齐，从而避免伪生成和锚模态依赖。

### 方法框架

#### 整体架构

![[fig1_page1.png|800]]

> 图1：SemMSA 整体框架，展示 CSR（跨模态语义精炼）与 CSA（跨模态谱对齐）两大组件

#### 各模块详细说明

**模块1：Cross-modal Semantic Refinement（CSR，跨模态语义精炼）**
- **功能**：在冻结 LLM 嵌入空间中构建情感相关的潜在语义状态
- **输入**：语言、视觉、声学三模态的原始表示
- **输出**：连续且有判别力的潜在语义状态
- **处理流程**：
  1. 用对应适配器（adapters）自适应提取视觉和声学表示
  2. 与语言一起在冻结 LLM 嵌入空间中形成统一多模态前缀
  3. 通过 token 高效的潜在精炼过程，迭代产生连续判别语义状态（不显式解码文本）
- **关键技术**：冻结 LLM + 潜在空间精炼（latent refinement），避免显式文本解码

**模块2：Cross-modal Spectral Alignment（CSA，跨模态谱对齐）**
- **功能**：将精炼语义与所有模态在无锚点条件下对齐
- **输入**：精炼后的潜在语义 + 各模态表示
- **输出**：对齐后的多模态融合表示
- **处理流程**：
  1. 计算各表示间的核 Gram 矩阵
  2. 增强主导谱分量，实现无锚点对齐
  3. 捕获所有表示间的全局非线性依赖
- **关键技术**：核 Gram 矩阵谱分析（spectral alignment），不依赖预定义锚定模态

**约束：实例级谱分离（instance-level spectral separation）**
- **功能**：保持跨样本判别性，缓解表示坍缩
- **作用**：在谱空间中对不同样本施加分离约束

### 方法架构图
![[fig1_page1.png|800]]

## 实验结果

### 实验目标
验证 SemMSA 在不完备多模态情感分析上的有效性。

### 数据集与基线
- **数据集**：SIMS、MOSI、MOSEI
- **基线**：现有不完备 MSA 方法（特征重构、复杂融合等）

### 主要结果
- 在 SIMS、MOSI、MOSEI 三个基准上均达到 **state-of-the-art** 性能
- 验证了 CSR（LLM 潜在语义）与 CSA（无锚点谱对齐）的有效性

### 结果分析
- **LLM 潜在语义的价值**：高层语义锚定有效缓解了伪生成与噪声引导
- **无锚点谱对齐的鲁棒性**：摆脱锚定模态依赖，在任意模态缺失下更稳健

## 深度分析

### 研究价值评估

#### 理论贡献
- **潜在语义锚定范式**：将 LLM 的隐式知识引入不完备 MSA，作为高层语义 grounding
- **无锚点谱对齐**：用核 Gram 矩阵谱分量实现不依赖锚定模态的多模态对齐
- **谱分离约束**：缓解表示坍缩，保持跨样本判别性

#### 实际应用价值
- **鲁棒情感识别**：适用于视频/语音/文本可能缺失的真实情感分析场景（客服、舆情、教育等）
- **轻量高效**：冻结 LLM + token 高效精炼，避免显式解码的昂贵开销

### 方法优势详解
- **优势1：消除伪生成**——以 LLM 潜在语义替代显式特征重构，避免伪特征
- **优势2：无锚点鲁棒**——谱对齐不依赖文本锚模态，模态缺失更稳健
- **优势3：判别性保持**——实例级谱分离约束避免表示坍缩

### 局限性分析
- **局限1：LLM 依赖**——性能受冻结 LLM 嵌入空间质量影响
- **局限2：计算开销**——核 Gram 矩阵与谱分解带来额外计算
- **局限3：情感基准有限**——仅在 SIMS/MOSI/MOSEI 验证，跨领域泛化待考

## 技术路线定位

本文属于 **不完备多模态情感分析** 技术路线：
- **承上**：继承 MSA 特征重构与融合机制的研究积累
- **启下**：将 LLM 潜在语义 + 谱对齐引入不完备模态场景，为后续"LLM 辅助鲁棒多模态理解"提供范式
- **关键节点**：从"显式重构"转向"潜在语义锚定"，是解决高层语义缺失的关键一步

## 相关论文
- 直接相关：[[20_Research/Papers/多模态/Light-MER_Do_We_Really_Need_Multimodal_Emotion_Language_Models_Larger_Than_1B_Parameters|Light-MER]] - 多模态情感语言模型
- 背景相关：多模态情感分析（MSA）/ 不完备模态融合系列工作

## 外部资源
- arXiv: http://arxiv.org/abs/2609.30238

> [!tip] 关键启示
> 不完备多模态情感分析的症结是"高层语义缺失 + 锚模态依赖"；用"冻结 LLM 潜在语义 + 无锚点谱对齐"可同时化解二者。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读：将 LLM 潜在语义与谱对齐结合，为解决不完备多模态情感分析提供了新范式，实验覆盖三个主流基准。
