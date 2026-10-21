---
date: "2026-10-21"
paper_id: "arXiv:2610.27142"
title: "ICLHerd: Herding-Based Data Selection for LLMs with In-Context Learning as a Proxy"
authors: "Yuqiao Wen, Lili Mou"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 数据选择
  - Data-Selection
  - Herding
  - In-Context-Learning
quality_score: "7.8/10"
related_papers: []
created: "2026-10-21"
updated: "2026-10-21"
status: analyzed
---

# ICLHerd: Herding-Based Data Selection for LLMs with In-Context Learning as a Proxy

## 核心信息
- **论文ID**：arXiv:2610.27142
- **作者**：Yuqiao Wen, Lili Mou
- **机构**：阿尔伯塔大学（University of Alberta / Amii）
- **发布时间**：2026-10-19
- **会议/期刊**：arXiv（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.27142) | [PDF](https://arxiv.org/pdf/2610.27142)
- **引用**：--

## 摘要翻译

### 英文摘要
Data selection plays a central role in modern large language model training, as the quality of training data strongly influences downstream performance. A common approach is to curate a development set that encodes the desired skills (e.g., reasoning or multilinguality) and select data that are similar to them. Existing methods compute similarity using cosine distance over either the model's hidden states or gradients. However, we observe that this max-similarity strategy induces a mode-seeking bias: the selected data clusters tightly around the development samples. To address this, we propose ICLHerd, a data selection framework that leverages the von Mises-Fisher kernel to balance mode-seeking with mode-averaging. We further introduce in-context learning as a validation proxy, enabling efficient hyperparameter search over the balance trade-off. Results show that ICLHerd outperforms existing LLM data selection algorithms, highlighting the importance of balancing mode-seeking and mode-averaging.

### 中文翻译
数据选择在现代大语言模型训练中处于核心地位，因为训练数据的质量会强烈影响下游性能。一种常见做法是构造一个"开发集"，用来编码期望的技能（如推理或多语言能力），再选择与之相似的数据。现有方法用模型隐状态或梯度上的余弦距离来计算相似度。然而本文观察到，这种"最大相似度"策略会诱发一种"寻模偏置"（mode-seeking bias）：选中的数据会紧密聚集在开发样本周围。为此，作者提出 ICLHerd 数据选择框架，利用 von Mises-Fisher 核来平衡"寻模"（mode-seeking）与"求平均模"（mode-averaging）。作者进一步引入上下文学习（in-context learning）作为验证代理，从而能够高效地搜索该平衡折衷的超参数。结果表明，ICLHerd 优于现有的 LLM 数据选择算法，凸显了平衡寻模与求平均模的重要性。

### 核心要点提炼
- **研究背景**：数据选择决定 LLM 训练质量，主流方法基于"开发集相似度"挑选数据。
- **研究动机**：现有余弦相似度的"最大相似度"策略会导致选出的数据过度聚集在开发样本附近（寻模偏置），覆盖不足。
- **核心方法**：ICLHerd 用 von Mises-Fisher 核把"寻模"与"求平均模"统一到一个可调的折衷框架中，并以 ICL 作为验证代理高效搜索超参数。
- **主要结果**：ICLHerd 优于现有 LLM 数据选择算法，证明"寻模/求平均模"的平衡对数据选择质量至关重要。
- **研究意义**：为 LLM 数据选择提供了一个有理论依据（基于 herding/核方法）的新视角，把"选最像的"改为"选最有代表性的"。

## 研究背景与动机

### 领域现状
大语言模型的性能高度依赖训练数据的构成。数据选择（data selection）旨在从海量候选语料中挑出最能提升下游任务性能的子集。主流范式是"以开发集为中心"：先构造一个小而精的开发集，编码目标技能（推理、多语言、指令遵循等），然后依据"与开发集的相似度"打分并挑选数据。相似度的计算方式主要有两类：基于模型隐状态（embedding 空间）或基于梯度（影响函数、gradient matching 等）。

### 现有方法的局限性
无论用隐状态还是梯度，这些方法都采用"最大化相似度"的策略——即优先选与开发样本最接近的数据。作者指出这一策略存在系统性偏差：它本质上是"寻模"（mode-seeking），会把选出的数据簇缩在开发样本周围，导致：
- 覆盖不足：远离开发样本但同样有价值的区域被忽略，多样性受损；
- 对开发集的代表性过度依赖：开发集稍有偏差，选出的数据就随之偏斜。

与此相对，"求平均模"（mode-averaging / mode-covering）策略会均匀覆盖整个分布，但可能引入大量与目标技能无关的噪声。

### 研究动机
作者的核心观察是：寻模与求平均模并非二选一，而是一个连续谱上的折衷。问题在于如何在一个有理论支撑的框架内控制这个折衷点，并且如何高效地确定折衷点（超参数）。这需要两个工具：(1) 一个能同时表达寻模与求平均模的相似度核；(2) 一个能快速评估"给定数据子集对下游性能影响"的代理指标，避免为每个超参数都跑一次昂贵的大规模训练。

## 研究问题

### 核心研究问题
如何在 LLM 数据选择中，用一个统一、可控的框架平衡"寻模"（选最像开发集的）与"求平均模"（选最能覆盖分布的），并高效地为该平衡点选择超参数？

具体分解为两个子问题：
1. 什么样的核/相似度度量能连续地插值于寻模与求平均模之间？
2. 如何在不做昂贵端到端训练的前提下，评估不同平衡点对应数据子集的质量？

## 方法概述

### 核心思想
ICLHerd 借用"herding"（牧群/羊群）这一核方法思想：herding 的目标是从分布中选出若干点，使其加权均值尽可能逼近分布的期望。在数据选择语境下，把开发集视为目标分布的"代表点集合"，用 von Mises-Fisher（vMF）核来度量数据点与开发集之间的相似度。vMF 核有一个"浓度参数"（concentration parameter），调节它即可在"寻模"（高浓度，只选最像开发集的点）与"求平均模"（低浓度，均匀覆盖）之间连续插值。

### 方法框架

#### 整体架构
ICLHerd 由三个组件构成：

1. **相似度计算**：用 vMF 核在隐状态空间度量候选数据与开发集的相似度；
2. **平衡折衷控制**：通过 vMF 的浓度参数控制寻模/求平均模的折衷；
3. **ICL 验证代理**：把"所选数据子集拼成 few-shot 示例"喂给模型做上下文学习，用其在下游任务上的表现作为该子集质量的代理分数，从而对浓度参数做高效搜索。

![[2610.27142_page2.png|600]]

> 图1：ICLHerd 数据选择流程示意——以 vMF 核在开发集周围打分，通过 ICL 代理验证选出的子集质量并调浓度参数。

#### 各模块详细说明

**模块1：vMF 核相似度**
- **功能**：在模型隐状态空间中计算候选数据点与开发集之间的核相似度
- **输入**：候选数据、开发集的隐状态表示
- **输出**：每个候选点的相似度得分
- **关键技术**：von Mises-Fisher 核（单位超球面上的分布），其浓度参数 κ 是核心旋钮

**模块2：寻模/求平均模折衷**
- **功能**：通过 κ 控制选点策略在"寻模"与"求平均模"之间的位置
- **输入**：浓度参数 κ
- **输出**：确定的数据选择策略
- **关键技术**：高 κ 趋向寻模（紧贴开发集），低 κ 趋向求平均模（均匀覆盖）

**模块3：ICL 验证代理**
- **功能**：用所选子集构造上下文示例，快速评估子集质量
- **输入**：候选数据子集、下游任务
- **输出**：下游性能的代理分数
- **处理流程**：
  1. 用当前 κ 选出数据子集
  2. 将子集拼成 few-shot 上下文
  3. 在验证任务上测 ICL 性能
  4. 据此更新 κ（超参数搜索）
- **关键技术**：in-context learning 作为廉价代理，避免每次全量训练

## 实验结果

### 实验目标
验证 ICLHerd 是否优于现有 LLM 数据选择算法，以及"寻模/求平均模"平衡的重要性。

### 实验设置
- **基线**：现有基于隐状态/梯度余弦相似度的数据选择算法
- **代理验证**：ICL 下游任务表现
- **评估**：选出的数据子集训练/ICL 后在下游任务上的性能

### 主要结果
- ICLHerd 在选数据质量上**优于现有 LLM 数据选择算法**；
- 实验证明单纯寻模（max-similarity）会因覆盖不足而次优，验证了"寻模/求平均模平衡"的核心主张。

## 深度分析

### 研究价值评估

#### 理论贡献
- 把 herding / 核方法引入 LLM 数据选择，用 vMF 核的浓度参数把"寻模 vs 求平均模"从经验选择提升为可理论刻画的连续谱；
- 揭示现有"最大相似度"策略的寻模偏置，并给出可操作的纠偏手段。

#### 实际应用价值
- 对数据配比、指令数据筛选、领域适配数据选择等场景有直接借鉴意义；
- ICL 验证代理让超参数搜索成本大幅降低，具备工程可行性。

### 方法优势详解
- **理论可控**：浓度参数使折衷点连续、可解释，而非黑箱调参；
- **低成本验证**：ICL 代理避免了"每超参跑一次训练"的昂贵开销；
- **通用性**：相似度计算只需隐状态，不依赖梯度，兼容黑盒/API 模型。

### 局限性分析
- 摘要未给出具体基准/数据集的绝对数值，实验规模与泛化性需读正文确认；
- vMF 核假设隐状态落在单位超球面上，需归一化处理，对表示质量敏感；
- ICL 代理与真实训练效果之间可能存在 gap，代理分数的可靠性是隐性假设。

### 适用性与场景分析
- **适用**：目标技能明确、可构造开发集的数据选择任务（推理、多语言、指令遵循等）；
- **不适用**：难以定义"开发集"或下游任务无明确 ICL 形式的场景。

## 技术路线定位

本文属于 **LLM 数据选择（data selection / data curation）** 技术路线，其核心特点是"以开发集为锚点、以相似度为打分"。本文的改进在于用核方法把"选最像的"升级为"选最有代表性的"，并引入 ICL 代理做超参搜索。

```
相似度打分（cosine/hidden/gradient） → 寻模/求平均模平衡（ICLHerd, vMF 核） → 未来方向
```

### 具体子方向
数据选择中的"代表性/多样性"子方向：如何既贴合目标分布、又保持覆盖，避免过度聚集。

## 未来工作建议

1. **扩展到多模态/多目标任务**：将 vMF 核与 ICL 代理推广到更复杂的目标技能编码。
2. **代理可靠性的理论与实证验证**：研究 ICL 代理分数与真实下游性能之间的相关性边界。
3. **与更大规模数据筛选流水线结合**：作为候选打分器嵌入工业级数据 pipeline。

## 我的综合评价

### 总体评分
**7.8/10** —— 问题定位清晰、方法有理论根基（herding + vMF 核），但实验规模与绝对指标需进一步确认。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 把 herding/核方法引入 LLM 数据选择，视角新颖 |
| 技术质量 | 7/10 | vMF 核 + ICL 代理思路清晰，但细节待正文 |
| 实验充分性 | 7/10 | 摘要声明优于基线，但缺具体数值/数据集 |
| 写作质量 | 8/10 | 动机链条清晰 |
| 实用性 | 8/10 | ICL 代理低成本、工程可行 |

## 相关论文

### 直接相关
- 基于梯度/影响函数的数据选择（如 TracIn、gradient matching 系列）

### 背景相关
- Herding 核方法经典工作（Welling 的 kernel herding）
- 基于嵌入相似度的数据去重/筛选工作

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.27142)

> [!tip] 关键启示
> 数据选择的本质不是"选最像开发集的"，而是"选最能代表目标分布的"——寻模与求平均模之间的平衡，才是决定数据质量的关键。

> [!warning] 注意事项
> - vMF 核需在单位球面归一化，对表示质量敏感
> - ICL 代理与真实训练效果间存在 gap
> - 摘要未给绝对数值，读正文需关注实验规模
