---
date: "2026-11-12"
paper_id: "2611.13997"
title: "Specify Privacy Yourself: Assessing Inference-Time Personalized Privacy Preservation Ability of Large Vision-Language Models"
authors: "Xingqi Wang, Xiaoyuan Yi, Xing Xie, Jia Jia"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Vision-Language
  - Privacy
  - Alignment
  - Benchmark
quality_score: "8.3/10"
related_papers: []
created: "2026-11-12"
updated: "2026-11-12"
status: analyzed
---

# Specify Privacy Yourself: Assessing Inference-Time Personalized Privacy Preservation Ability of Large Vision-Language Models

## 核心信息
- **论文ID**：2611.13997
- **作者**：Xingqi Wang, Xiaoyuan Yi, Xing Xie, Jia Jia
- **机构**：清华大学（Jia Jia 组）、微软亚洲研究院（Xing Xie）
- **发布时间**：2026-11-10
- **会议/期刊**：arXiv 预印本（cs.CV / cs.AI / cs.CL / cs.CY / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.13997) | [PDF](https://arxiv.org/pdf/2611.13997)
- **引用**：--（代码与数据：https://github.com/achernarwang/specify-privacy-yourself）

## 摘要翻译

### 英文摘要
Large Vision-Language Models (LVLMs) have demonstrated remarkable capabilities but raise significant privacy concerns due to their abilities to infer sensitive personal information from images with high precision. While current LVLMs are relatively well aligned to protect universal privacy, e.g., credit card data, we argue that privacy is inherently personalized and context-dependent. This work pivots towards a novel task: can LVLMs achieve Inference-Time Personalized Privacy Protection (ITP³), allowing users to dynamically specify privacy boundaries through language specifications? To this end, we present SPY-Bench, the first systematic assessment of ITP³ ability, which comprises (1) 32,700 unique samples with image-question pairs and personalized privacy instructions across 67 categories and 24 real-world scenarios, and (2) novel metrics grounded in user specifications and context awareness. Benchmarking the ITP³ ability of 21 SOTA LVLMs, we reveal that: (i) most models, even the top-performing o4-mini, perform poorly, with only ~24% compliance accuracy; (ii) they show quite limited contextual privacy understanding capability. Therefore, we implemented initial ITP³ alignment methods, including a novel Noise Contrastive Alignment variant which achieves 96.88% accuracy while maintaining reasonable general performance. These results mark an initial step towards the ethical deployment of more controllable LVLMs.

### 中文翻译
大视觉语言模型（LVLM）展现出强大能力，但也因其能从图像中高精度推断敏感个人信息而引发严重的隐私担忧。当前 LVLM 在保护"通用隐私"（如信用卡数据）上已相对对齐良好，但作者认为隐私本质上是**个性化且情境依赖**的。本文转向一个新任务：LVLM 能否实现"推理时个性化隐私保护"（ITP³），让用户通过语言描述动态指定隐私边界？为此作者提出 SPY-Bench，这是对 ITP³ 能力的首个系统性评测，包含（1）32,700 个独特样本（图像-问题对 + 跨 67 类别、24 个真实场景的个性化隐私指令），以及（2）基于用户规范与情境感知的新型指标。对 21 个 SOTA LVLM 的评测揭示：(i) 大多数模型、甚至表现最好的 o4-mini 都表现不佳，合规准确率仅约 24%；(ii) 它们的情境化隐私理解能力相当有限。因此作者实现了初步的 ITP³ 对齐方法，包括一个新颖的"噪声对比对齐"变体，在保持合理通用性能的同时达到 96.88% 的准确率。这些结果标志着迈向"更可控 LVLM 伦理部署"的第一步。

### 核心要点提炼
- **研究背景**：LVLM 能从图像推断隐私，现有对齐只覆盖"通用隐私"，忽略"个性化/情境化"。
- **研究动机**：隐私是因人而异、因情境而异的，用户应能自主、动态地划定边界。
- **核心方法**：提出 ITP³ 任务 + SPY-Bench 评测集 + 噪声对比对齐（NCA）变体。
- **主要结果**：SOTA LVLM 合规准确率仅 ~24%；NCA 变体达 96.88%。
- **研究意义**：首次系统定义并评测"推理时个性化隐私保护"，为可控 LVLM 部署奠基。

## 研究背景与动机

### 领域现状
LVLM（如 GPT-4V、Qwen-VL、InternVL 等）在图像理解上已能高精度地推断出人脸、地点、身份、消费习惯等敏感信息。主流缓解方案是对齐（alignment）——训练模型拒绝回答涉及"通用隐私"（信用卡号、身份证号等）的问题。这类对齐在"一刀切"的隐私类型上已相当成熟。

### 现有方法的局限性
1. **通用对齐 ≠ 个性化**：用户对"什么算隐私"的边界各不相同（有人不愿暴露位置，有人不在意年龄）。
2. **静态边界 ≠ 动态情境**：隐私诉求随场景变化，现有模型无法在推理时按用户当下的语言指令调整。
3. **缺乏评测**：此前没有系统性 benchmark 来量化 LVLM 的"个性化隐私保护"能力。

### 研究动机
作者的核心论点是：隐私保护应该**把控制权交还给用户**——用户用自然语言说一句"不要透露我的位置信息"，模型就应在推理时即时遵守。这一"推理时个性化隐私保护"（ITP³）能力，需要被定义、被评测、被对齐。

## 研究问题

### 核心研究问题
1. 如何形式化"推理时个性化隐私保护"（ITP³）任务？
2. 现有 SOTA LVLM 的 ITP³ 能力到底有多强（SPY-Bench 量化）？
3. 能否设计对齐方法显著提升 ITP³ 合规率，同时不损害通用能力？

## 方法概述

### 核心思想
把隐私从"模型预设的静态类别"转变为"用户动态指定的语言边界"。核心产物有两层：一是 **SPY-Bench** 评测集（大规模、多类别、多场景的个性化隐私指令 + 图像-问题对），二是 **噪声对比对齐（Noise Contrastive Alignment, NCA）** 训练方法，让模型学会"按指令守住隐私边界"。

### 方法框架

#### 整体架构

![[intro_page1.png|800]]

> 图3：ITP³ 任务示意。用户通过语言规范动态指定隐私边界，LVLM 需在推理时理解该边界，拒绝透露被指定为隐私的信息，同时正常回答其余问题。

#### 各模块详细说明

**模块1：SPY-Bench 构建**
- **功能**：系统化构建个性化隐私评测样本。
- **组成**：
  - 32,700 个独特样本（图像-问题对 + 个性化隐私指令）。
  - 67 个隐私类别、24 个真实世界场景。
- **关键技术**：隐私类别覆盖 + 场景化设计 + 用户规范驱动的指令模板。

**模块2：ITP³ 指标设计**
- **功能**：量化模型是否"按用户规范守边界"。
- **维度**：
  - 合规准确率（是否拒绝透露被指定的隐私）。
  - 情境感知能力（是否理解隐私的上下文相关性）。
- **关键技术**：基于用户规范与情境的评测指标。

**模块3：噪声对比对齐（NCA）**
- **功能**：训练模型提升 ITP³ 合规率。
- **核心思想**：通过噪声对比信号，让模型学会区分"应保护"与"可透露"的信息。
- **结果**：96.88% 合规准确率，同时保持合理通用性能。

### 关键创新
1. **提出 ITP³ 新任务** - 把隐私保护从"通用对齐"推进到"推理时个性化、用户可控"。
2. **SPY-Bench 首个系统评测集** - 32,700 样本、67 类别、24 场景，规模与覆盖面开创先河。
3. **噪声对比对齐（NCA）** - 在保持通用性能的同时把合规率从 ~24% 拉到 96.88%。

## 实验结果

### 数据集
- SPY-Bench：32,700 个样本，67 隐私类别，24 真实场景。

### 实验设置
- **评测模型**：21 个 SOTA LVLM（含 o4-mini 等）。
- **评估指标**：合规准确率（compliance accuracy）、情境隐私理解能力、通用性能保持度。
- **基线方法**：现有对齐方法；本文 NCA 及其变体。

### 主要结果
| 方法 | ITP³ 合规准确率 | 通用性能 |
|------|----------------|----------|
| SOTA LVLM（含 o4-mini） | ~24%（最佳 o4-mini） | 高 |
| 现有对齐方法 | 较低 | 保持 |
| **本文 NCA 变体** | **96.88%** | 合理保持 |

### 结果分析
评测揭示了一个残酷现实：即便是最强的 o4-mini，在"个性化隐私"这一任务上也仅约 24% 合规率——说明现有模型完全没学会"按用户当下指令动态守边界"。而 NCA 变体把合规率提升到 96.88%，证明这一能力**是可训练、可对齐的**，且不牺牲通用性能。

## 深度分析

### 研究价值
- **理论贡献**：提出 ITP³ 任务与 SPY-Bench，为"个性化、情境化隐私保护"建立了可量化的研究范式。
- **实际应用**：医疗影像、个人照片助手、安防监控等需要"按人、按情境"保护隐私的场景。
- **领域影响**：推动 LVLM 对齐从"通用安全"迈向"用户可控、动态个性化"的新阶段。

### 优势
- 问题定义清晰且切中要害——隐私本就因人而异。
- SPY-Bench 规模大、覆盖广，评测严谨。
- NCA 方法效果显著（24% → 96.88%），且保持通用能力。

### 局限性
- 隐私指令目前以文本语言为主，更自然的交互形式（如隐式表达）未覆盖。
- 96.88% 的合规率在对抗性用户/越狱场景下是否稳健，尚需验证。
- 情境理解的评测维度仍有主观性。

### 适用场景
- 需要"按人定制隐私边界"的图像理解与多模态助手。
- 医疗、金融、社交等强隐私约束领域的 LVLM 部署。

## 与相关论文对比

### 通用 LVLM 隐私对齐工作 - 对比关系
- **差异**：本文从"通用静态隐私"转向"个性化动态隐私"，并首次提供系统评测。
- **改进**：引入用户规范驱动的边界 + NCA 对齐，量化能力上限并给出解法。

## 技术路线定位

本文属于"LVLM 安全对齐"技术路线，聚焦"隐私保护"子方向。发展脉络：通用安全对齐 → 通用隐私对齐 → **个性化/情境化隐私保护（本文）**。关键节点在于**把控制权交还用户，并用 benchmark + 对齐方法使这一能力落地**。

## 未来工作建议

1. **更自然的隐私表达** - 支持隐式、多模态的隐私边界表达。
2. **对抗鲁棒性** - 验证 NCA 在越狱/对抗提示下的稳健性。
3. **跨任务泛化** - 将 ITP³ 对齐推广到文本、语音等多模态隐私场景。

## 我的综合评价

### 价值评分

**8.3/10** - 问题定义新颖、评测系统、解法有效，是 LVLM 隐私对齐方向的重要一步。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | ITP³ 新任务 + NCA 方法有明确新意 |
| 技术质量 | 8/10 | SPY-Bench 规模与指标设计严谨 |
| 实验充分性 | 9/10 | 21 模型横评 + 对齐方法验证充分 |
| 写作质量 | 8/10 | 清晰，数据翔实 |
| 实用性 | 8/10 | 医疗、金融等场景有直接价值 |

> [!tip] 关键启示
> 隐私保护的本质不是"模型决定什么该隐藏"，而是"用户决定什么该隐藏"——本文把控制权交还用户，并用 24% → 96.88% 的跃迁证明这条路走得通。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐：LVLM 隐私对齐方向的开创性工作，问题定义与解法都值得深读。
