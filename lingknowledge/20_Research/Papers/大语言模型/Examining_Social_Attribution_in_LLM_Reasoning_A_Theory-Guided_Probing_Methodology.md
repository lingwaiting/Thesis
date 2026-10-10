---
date: "2026-10-10"
paper_id: "arXiv:2610.12022"
title: "Examining Social Attribution in LLM Reasoning: A Theory-Guided Probing Methodology"
authors: "Zhaoxin Yu, Qingchao Kong, Dajun Zeng, Wenji Mao"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Social-Attribution
  - Probing
  - Interpretability
  - Reasoning
quality_score: "8.5/10"
created: "2026-10-10"
updated: "2026-10-10"
status: analyzed
---

# Examining Social Attribution in LLM Reasoning: A Theory-Guided Probing Methodology

## 核心信息
- **论文ID**：arXiv:2610.12022
- **作者**：Zhaoxin Yu, Qingchao Kong, Dajun Zeng, Wenji Mao
- **机构**：中国科学院自动化研究所（基于作者背景推断）
- **发布时间**：2026-10-08
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.12022) | [PDF](https://arxiv.org/pdf/2610.12022)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models (LLMs) are increasingly deployed in sociotechnical systems where social attribution, the reasoning process attributing external events to the causes and reasons of agents' social behaviors, plays a critical role. These processes involve judgments of social cause, responsibility, and blame/credit to agents. Although attributional models are well-studied in social psychology and cognition through Attribution Theory, social attribution remains underexplored in AI, particularly LLM social reasoning. This paper provides the first systematic exploration of LLM social attribution. Our work focuses on responsibility and blame attributions, examining current LLMs' judgments and their underlying internal mechanisms. Guided by attribution theory, we construct a social attribution benchmark consisting of a Vignette subset based on classic scenarios from attribution theory research and a Reality subset based on real-world social narratives, yielding 7,639 responsibility/blame judgment questions. On this basis, we evaluate 32 representative LLMs and 5 basic non-LLM baselines. To further explore the internal mechanisms underlying the LLM judgment process, we develop a probing-based methodology to investigate the latent-space representations of 5 key attribution dimensions and the consistency of their influences on LLM judgments compared to those in human social attribution. Our research findings reveal that current LLMs exhibit measurable but incomplete agreement with human responsibility and blame judgments, and meanwhile, this agreement is positively correlated with model size. Some attribution dimensions are systematically decodable from specific positions in LLM hidden states, and their influences on the final judgment are consistent with those indicated by human Attribution Theory. The dataset and associated code are available at https://github.com/Yuzhaoxin946/SAB-Bench.

### 中文翻译
大语言模型（LLM）正越来越多地被部署在社会技术系统中，其中"社会归因"——即将外部事件归因于智能体社会行为的原因和理由的推理过程——扮演着关键角色。这类过程涉及对社会因果、责任以及对智能体的责备/赞誉的判断。尽管归因模型在社会心理学和认知科学中通过"归因理论"得到了充分研究，但社会归因在 AI 领域、尤其是 LLM 社会推理方面仍未得到充分探索。本文首次系统地探索了 LLM 的社会归因能力。工作聚焦于责任归因与责备归因，考察当前 LLM 的判断及其背后的内部机制。在归因理论的指导下，我们构建了一个社会归因基准，包含基于归因理论经典场景的 Vignette 子集和基于真实社会叙事的 Reality 子集，共产生 7,639 个责任/责备判断题。在此基础上，我们评估了 32 个代表性 LLM 和 5 个基础非 LLM 基线。为进一步探索 LLM 判断过程的内部机制，我们开发了一套基于 probing 的方法，研究 5 个关键归因维度在潜在空间中的表征，以及它们对 LLM 判断的影响与人类社会归因之间的一致性。研究发现，当前 LLM 与人类的责任/责备判断存在可测量但不完全的一致，且这种一致性程度与模型规模正相关。某些归因维度可以从 LLM 隐状态的特定位置被系统性解码，且它们对最终判断的影响与人类归因理论所指出的方向一致。数据集与代码已开源：https://github.com/Yuzhaoxin946/SAB-Bench。

### 核心要点提炼
- **研究背景**：LLM 越来越多地被用于需要社会判断的系统中，但它们在"社会归因"（判断责任/责备）这一基础认知能力上的表现鲜有人系统研究。
- **研究动机**：社会心理学中的归因理论成熟，但 AI 界缺少对应的基准和内部机制分析。
- **核心方法**：理论指导构建 7,639 题的社会归因基准（SAB-Bench），对 32 个 LLM + 5 个基线做行为评估，再用 probing 方法剖析隐状态中的 5 个归因维度。
- **主要结果**：LLM 与人类判断有可测量但不完全的一致，且与模型规模正相关；归因维度可从隐状态特定位置解码，影响方向与人类归因理论一致。
- **研究意义**：为"LLM 是否真正理解社会因果"提供了第一个可量化、可解释的评测框架。

## 研究背景与动机

### 领域现状
LLM 的推理能力评测长期聚焦于数学、代码、常识问答等"客观"任务，而对"社会推理"（social reasoning）——尤其是涉及责任、责备、因果归因这类带价值判断的主观推理——关注不足。已有的社会推理基准（如 Social IQa、ToM 类测试）多关注社会常识或心智理论，缺乏对"归因"这一社会心理学核心概念的系统刻画。

### 现有方法的局限性
- 缺少基于成熟心理学理论（归因理论）的 LLM 归因评测基准；
- 行为层面的"对/错"评测无法解释 LLM 为何作出某种归因判断；
- 归因过程涉及多个维度（因果性、可控性、意图性等），现有工作未系统分离这些维度的影响。

### 研究动机
把社会心理学中成熟的归因理论引入 LLM 评测，既能量化 LLM 与人类社会判断的一致程度，又能借助 probing 打开"黑箱"，揭示 LLM 判断背后的内部表征是否遵循与人类相同的归因逻辑。

## 研究问题

### 核心研究问题
1. 当前 LLM 在责任归因与责备归因上的判断与人类有多大程度的一致？
2. LLM 的判断是否随模型规模/能力增强而更接近人类？
3. 归因的关键维度（如意图性、可控性、因果性）是否在 LLM 隐状态中具有可解码的表征？其影响方向是否与人类归因理论一致？

## 方法概述

### 核心思想
用"理论指导"的方式构建基准，把归因拆解为可度量的维度，既做行为对齐评估，又做表征层 probing 分析，从而同时回答"LLM 做得好不好"和"LLM 为什么这么做"两个问题。

### 方法框架

#### 整体架构
整体流程分两阶段：

1. **行为评估阶段**：基于归因理论构建 SAB-Bench（Vignette + Reality 两个子集，7,639 题），在 32 个 LLM 与 5 个非 LLM 基线上评测责任/责备判断与人类标注的一致性。
2. **机制剖析阶段**：对 5 个关键归因维度训练 probing 分类器，在 LLM 各层隐状态上测试其可解码性，并分析这些维度对最终判断的影响方向。

![[2610.12022_fig29.png|600]]

> 图1：论文的核心框架/结果示意图（SAB-Bench 的构建与 probing 分析方法论概览）。

#### 各模块详细说明

**模块1：SAB-Bench 基准构建**
- **功能**：生成 7,639 个责任/责备判断题。
- **构成**：Vignette 子集（归因理论经典场景）+ Reality 子集（真实社会叙事）。
- **关键技术**：以归因理论为骨架设计场景与标签，保证心理学上的规范性。

**模块2：行为对齐评估**
- **功能**：衡量 LLM 判断与人类判断的一致性。
- **范围**：32 个代表性 LLM + 5 个基础非 LLM 基线。
- **关键技术**：一致性度量 + 与模型规模的关联分析。

**模块3：归因维度 probing**
- **功能**：研究 5 个关键归因维度（如意图性、可控性、因果性等）在潜在空间的表征。
- **输入**：LLM 各层的隐状态表征。
- **输出**：各维度在特定位置的可解码性，以及其对最终判断影响方向与人类归因理论的一致性。

## 实验结果

### 实验目标
验证 LLM 社会归因的行为一致性与内部机制可解释性。

### 数据集
- SAB-Bench：7,639 个责任/责备判断题（Vignette + Reality 两子集）。

### 主要结果

- **行为一致性**：当前 LLM 与人类责任/责备判断存在**可测量但不完全**的一致；该一致性程度与**模型规模正相关**。
- **机制可解释性**：部分归因维度可从 LLM 隐状态的**特定位置**被系统性解码；这些维度对最终判断的影响方向与人类归因理论一致。
- **规模效应**：更大模型在归因判断上更接近人类，暗示能力提升带来社会推理的渐进对齐。

### 结果分析
论文的核心发现是"LLM 的社会归因既不是随机的，也尚未完全对齐人类"：一方面归因维度确实编码在模型内部（可解码），且作用方向正确，说明模型学到了部分归因逻辑；另一方面行为层一致性仍不完整，说明存在系统性偏差。这与"能力越大越对齐"的普遍观察一致，但首次在归因这一具体认知能力上给出实证。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：首次将社会心理学"归因理论"系统引入 LLM 评测，提出理论指导的 benchmark 设计范式。
  - 创新点：从"客观问答"拓展到"价值判断 + 因果归因"的主观推理评测。
- **贡献2**：行为层 + 表征层双重视角，同时回答"对不对"与"为什么"。
  - 创新点：probing 方法将归因维度从隐状态中解码，为可解释性研究提供新工具。

#### 实际应用价值
- **应用场景1**：负责任 AI 与 AI 安全——评估模型在涉及责任认定的场景（如事故归因、内容审核）中的可靠性。
- **应用场景2**：社会模拟/社会计算——用 LLM 模拟社会行为时，可据此校准其归因倾向。

### 局限性分析
- **局限1**：责任/责备判断本身带文化与主观性，基准标签依赖特定人类标注，跨文化泛化待验证。
- **局限2**：仅覆盖责任与责备两类归因，社会因果、赞誉等维度未完全纳入。
- **局限3**：probing 的可解码性不等于因果性——维度"可解码"不代表模型"使用"了该维度做判断。

### 适用性与场景分析
- **适用场景**：需要量化 LLM 社会推理对齐程度、或研究 LLM 归因内部机制的学术与安全评测场景。
- **不适用场景**：追求纯客观事实推理的任务；对文化中立性要求极高的评测。

## 与相关论文对比
本文处于"LLM 社会推理 / 可解释性"交叉领域。相较于 Social IQa、ToM 基准（关注社会常识/心智理论的行为正确率），本文独特性在于：以归因理论为纲、区分责任/责备、并深入隐状态做 probing。相比通用 probing 工作，本文的 probing 目标维度有明确的心理学理论支撑。

## 技术路线定位
- **所属技术路线**：LLM 社会推理评测 + 机制可解释性。
- **本文位置**：把"心理学理论 → 基准设计 → 表征分析"打通，是"理论驱动的可解释评测"这一路线的代表性工作。
- **启下**：为后续"归因维度因果干预""跨文化社会对齐"等工作提供基准与工具。

## 未来工作建议
1. 从"可解码"走向"因果"：用干预实验（activation patching）验证归因维度是否被模型实际用于判断。
2. 扩展归因维度与场景，覆盖更多文化背景，检验结论的跨文化稳健性。

## 我的综合评价

### 价值评分
**8.5/10** —— 选题新颖（LLM 社会归因首次系统探索）、方法论扎实（理论指导 + 行为/表征双层），可解释性视角独特；局限在于主观判断标签的文化依赖与 probing 的因果性局限。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次系统研究 LLM 社会归因，理论指导的基准设计有新意 |
| 技术质量 | 8/10 | 基准规模可观，probing 方法规范 |
| 实验充分性 | 8/10 | 32 个 LLM + 基线，规模效应分析充分 |
| 写作质量 | 8/10 | 逻辑清晰 |
| 实用性 | 8/10 | 对 AI 安全与社会对齐有直接价值 |

### 重点关注
- 归因维度在隐状态中的可解码位置，及其"可解码 vs 被使用"的区分。
- 与模型规模正相关的对齐趋势对 scaling 研究的启示。

## 相关论文
- 社会推理 / ToM 类 LLM 评测工作（Social IQa 等）
- LLM 可解释性 probing 相关工作

## 外部资源
- 数据集与代码：https://github.com/Yuzhaoxin946/SAB-Bench

> [!tip] 关键启示
> LLM 的社会归因既不是随机猜测，也尚未与人类完全对齐——归因维度确实编码在模型内部且作用方向正确，为"模型学到了社会推理的一部分"提供了直接证据。

> [!warning] 注意事项
> - probing 的"可解码"不等于因果使用，需干预实验进一步确认。
> - 责任/责备标签有文化主观性，跨文化泛化需谨慎。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读！这是"LLM 社会推理"可解释性方向的开创性工作，为负责任 AI 评测提供了新范式。
