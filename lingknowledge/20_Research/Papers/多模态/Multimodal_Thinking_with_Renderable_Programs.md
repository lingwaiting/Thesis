---
date: "2026-09-27"
paper_id: "2609.30130"
title: "Multimodal Thinking with Renderable Programs"
authors: "Sunli Chen, Ding Zhong, Ziqiao Ma, Jiaxin Liu, Zeyuan Yang, Hao Zhang, Lie Lu, Joyce Chai, Chuang Gan"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态
  - Vision-Language
  - SVG
  - 推理
quality_score: "8.5/10"
related_papers: []
created: "2026-09-27"
updated: "2026-09-27"
status: analyzed
---

# Multimodal Thinking with Renderable Programs

## 核心信息
- **论文ID**：2609.30130
- **作者**：Sunli Chen, Ding Zhong, Ziqiao Ma, Jiaxin Liu, Zeyuan Yang, Hao Zhang, Lie Lu, Joyce Chai, Chuang Gan
- **机构**：--（第一作者与 Chuang Gan（MIT-IBM Watson AI Lab）、Joyce Chai（UMich）等合作，未在 arXiv 元数据中给出）
- **发布时间**：2026-09-24
- **会议/期刊**：arXiv 预印本（cs.CV, cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2609.30130) | [PDF](https://arxiv.org/pdf/2609.30130)
- **引用**：--

## 摘要翻译

### 英文摘要
Current vision-language models (VLMs) excel at visual content understanding and text-based reasoning, yet their structure limits the advancement of incorporating images into the reasoning chain. We introduce SVGLM, a framework that uses scalable vector graphics (SVG) primitives to connect text and image in reasoning tasks. We exploit the duality of SVG as both image description and text instructions, yielding a more compact, interpretable solution to equip general VLMs with the capability of generating images within the reasoning process.

### 中文翻译
当前的视觉语言模型（VLM）擅长视觉内容理解和基于文本的推理，但其结构限制了将图像纳入推理链的进一步发展。本文提出 SVGLM，一个利用可缩放矢量图形（SVG）原语在推理任务中连接文本与图像的框架。作者利用 SVG 既是图像描述又是文本指令的二元性，得到一种更紧凑、更可解释的方案，使通用 VLM 具备在推理过程中生成图像的能力。

### 核心要点提炼
- **研究背景**：VLM 擅长看图+文本推理，但难以在"推理链中生成图像"；Omnimodal 模型统一了文本和图像生成，但依赖栅格化/隐式表示，缺乏可追溯性。
- **研究动机**：需要一个紧凑、可解释的中间表示，让通用 VLM 在思考过程中"画图"。
- **核心方法**：用 SVG 原语作为文本与图像的桥梁，SVG 本身即文本指令、又可渲染为图像。
- **主要结果**：在数学推理基准上展现出强大的 SVG 生成能力和"边想边画"（think-with-image）智能。
- **研究意义**：SVG 是构建更鲁棒的数字域智能体的合适媒介，弥合了文本思维与像素图像的鸿沟。

## 研究问题

### 核心研究问题
如何让通用 VLM 在推理链中**生成并利用图像**，而不受栅格化/隐式表示缺乏可追溯性的制约？

现有 VLM 将图像视为理解对象，推理始终以文本为媒介；Omnimodal 模型虽能生成图像，但：
1. 面向开放域视觉任务，缺乏结构化推理能力；
2. 图像以栅格化像素或隐式向量表示，难以在推理链中被解析、编辑、复用。

作者提出用 **SVG（可缩放矢量图形）** 作为中间表示，因为它同时具备两种性质——既是人类可读的文本指令，又能渲染为精确图像。

## 方法概述

### 核心思想
把"图像生成"降维为"SVG 程序生成"。SVG 是一段文本代码，VLM 可以直接在 token 空间生成它；同时 SVG 又能渲染为几何精确的图像，供下游任务（视觉推理、几何证明）使用。这样"思考过程中的图像"就变成 VLM 天然擅长的文本生成任务。

### 方法框架

#### 整体架构
SVGLM 由三部分组成：数据侧（大规模 SVG 图像编辑数据集）、训练侧（在开源 VLM 上的微调范式）、推理侧（在数学/几何推理链中按需渲染 SVG）。

![[teaser_v1_page1.png|700]]

> 图1（teaser）：SVGLM 的核心思想示意——SVG 既是文本指令又是图像描述，使 VLM 在推理过程中"边想边画"。

#### 各模块详细说明

**模块1：SVG 作为统一表示**
- **功能**：将图像内容编码为 SVG 文本原语，使图像生成与理解共用同一 token 空间。
- **关键技术**：利用 SVG 的"二元性"（image description ↔ text instructions）。
- **优势**：紧凑、可解释、可编辑，相比栅格图/隐式向量更利于推理链中的追溯与修正。

**模块2：SVG 图像编辑数据集**
- **功能**：提供大规模、精心构造的 SVG-based 图像编辑训练数据。
- **作用**：让开源 VLM 学会生成、修改、渲染 SVG 的能力。

**模块3：开源 VLM 微调范式**
- **功能**：给出将上述能力注入通用 VLM 的训练流程。
- **目标**：在不损失通用能力的前提下，赋予模型"在推理中生成图像"的智能。

### 关键创新
1. **SVG 作为推理中间表示**——首次系统性论证 SVG 的文本/图像二元性可桥接"文本思维"与"像素图像"。
2. **think-with-image 智能**——让 VLM 在推理链内部生成视觉证据，而非仅在输入端理解图像。
3. **可追溯的视觉生成**——相比隐式/栅格生成，SVG 可被解析、校验、迭代编辑，更适合数字域智能体。

## 实验结果

### 数据集
- **SVG-based 图像编辑数据集**：作者自建的大规模训练集。
- **数学推理基准**：用于验证 SVG 生成能力与"边想边画"智能。

### 实验设置
- **评估目标**：SVG 生成质量 + think-with-image 的推理收益。
- **对比方向**：直接文本推理的 VLM、栅格化/隐式图像生成的 Omnimodal 模型。

### 主要结果
- SVGLM 在数学推理基准上展现出**强大的 SVG 生成能力**。
- 论证了 **think-with-image 智能**带来的推理提升。
- 结论：SVG 是构建更鲁棒数字域智能体的合适媒介。

![[comparison_page1.png|700]]

> 图2（comparison）：与基线方法的对比，体现 SVG 生成质量与推理收益。

![[data_gen_pipeline_page1.png|700]]

> 图3（data_gen_pipeline）：SVG 图像编辑数据集的数据生成管线。

## 深度分析

### 研究价值
- **理论贡献**：提出"用可渲染程序（SVG）作为多模态推理中间表示"的新范式，为"视觉生成如何服务推理"提供了新视角。
- **实际应用**：几何/数学解题、数字域智能体（UI 自动化、图表生成）、需要可追溯视觉输出的场景。
- **领域影响**：为 Omnimodal 模型与推理模型的融合指出了"结构化中间表示"这一方向。

### 优势
1. SVG 紧凑、可解释、可编辑，天然适合推理链中的迭代与校验。
2. 将视觉生成转化为 VLM 擅长的文本生成，工程上可复用现有文本微调基础设施。
3. 输出可被程序化解析与渲染，适合构建自动化智能体。

### 局限性
1. 摘要仅报告数学推理基准，**其他视觉任务（自然图像编辑、开放域生成）的泛化性待验证**。
2. SVG 表达能力有限，难以覆盖复杂纹理、光影等像素级细节。
3. 微调对开源 VLM 通用能力的潜在影响未在摘要中量化。

### 适用场景
- **几何/数学推理**：需要精确图形辅助的解题任务。
- **数字域智能体**：UI 理解、图表/示意图生成、可视化推理。
- **不适用场景**：需要照片级真实感图像的开放域生成任务（应选扩散模型等）。

## 技术路线定位
本文属于 **Omnimodal / 多模态推理** 技术路线，具体子方向是"以结构化程序（SVG）为中间表示的视觉生成 + 推理"。它在"栅格图生成"与"纯文本推理"之间架起了一座可追溯的桥梁。

## 未来工作建议
1. 将 SVG 中间表示扩展到自然图像、视频等更丰富的视觉模态。
2. 与工具调用/外部渲染器结合，构建闭环的"生成—渲染—校验"智能体。
3. 在更多推理基准（不仅数学）上系统评估 think-with-image 的收益边界。

## 我的综合评价

### 价值评分
- **总体评分**：**8.5/10**
- **分项评分**：
  - 创新性：8/10（SVG 作为推理中间表示的角度新颖，但非全新范式）
  - 技术质量：8/10（方法清晰，数据+微调范式完整）
  - 实验充分性：7/10（摘要仅覆盖数学推理基准，广度有限）
  - 写作质量：8/10（motivation 明确）
  - 实用性：8/10（对数字域智能体方向有直接价值）

### 突出亮点
- SVG 的"文本/图像二元性"是优雅的洞察。
- "边想边画"（think-with-image）概念直击 VLM 推理的痛点。
- 可追溯、可编辑的视觉生成，对智能体落地友好。

### 可借鉴点
- 把"图像生成"问题重铸为"程序生成"问题，降低了对生成模型能力的要求。
- 结构化中间表示比隐式向量更适合推理与校验，值得在其他模态中推广。

### 批判性思考
- 摘要的证据链较薄：仅数学推理基准，缺少与主流扩散模型、Omnimodal 模型的严格量化对比。
- SVG 的表示能力上限是潜在瓶颈，能否支撑"真实世界视觉推理"存疑。

## 我的笔记

%% 用户阅读后手动补充 %%

## 相关论文
- （暂无已收录的直接相关笔记，后续可关联 Omnimodal 生成与多模态推理类论文）

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.30130)
- [PDF](https://arxiv.org/pdf/2609.30130)
