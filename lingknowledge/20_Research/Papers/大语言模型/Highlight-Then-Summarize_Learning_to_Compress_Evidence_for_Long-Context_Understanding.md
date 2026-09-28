---
date: "2026-09-28"
paper_id: "2609.31382"
title: "Highlight-Then-Summarize: Learning to Compress Evidence for Long-Context Understanding"
authors: "Zhaoyuan Xia, Qinghongbing Xie, Yung Xiang Hue, Jianguang Jiang, Gaofeng Lu, Zhenyu Jiao, Xing Yuan, Dai Dai, Tong Mo, Long Zeng"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - 长上下文
  - RAG
  - 证据压缩
quality_score: "8.5/10"
related_papers: []
created: "2026-09-28"
updated: "2026-09-28"
status: analyzed
---

# Highlight-Then-Summarize: Learning to Compress Evidence for Long-Context Understanding

## 核心信息
- **论文ID**：2609.31382
- **作者**：Zhaoyuan Xia, Qinghongbing Xie, Yung Xiang Hue, Jianguang Jiang, Gaofeng Lu, Zhenyu Jiao, Xing Yuan, Dai Dai, Tong Mo, Long Zeng
- **机构**：北京大学（Peking University）、清华大学（Tsinghua University）、百度（Baidu Inc）
- **发布时间**：2026-09-25
- **会议/期刊**：arXiv 预印本（cs.CL, cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2609.31382) | [PDF](https://arxiv.org/pdf/2609.31382)
- **引用**：--

## 摘要翻译

### 英文摘要
Long-context understanding requires large language models (LLMs) to reason over lengthy documents, conversations, and code, yet task-relevant evidence is often sparse and scattered amid substantial irrelevant and redundant content. We propose Highlight-Then-Summarize (H2S), a compress-then-reason paradigm that first identifies source-grounded, question-relevant evidence and then integrates it into a compact, question-conditioned summary before producing the final answer. To train this behavior, we construct H2S-Dataset, comprising 6,647 examples from 11 benchmark families with an average context length of 43.9K tokens, and introduce H2S-RL, which provides process-level rewards for evidence selection and summary construction in addition to final-answer correctness. We evaluate on H2S-Bench, a seven-task long-context suite. Under a shared 128K input and 4K output budget, H2S-14B achieves an average score of 32.60, outperforming Qwen3.8-27B by 10.17 points and obtaining the strongest overall result among the evaluated open-source models. H2S-14B also achieves the highest Evidence-Summary Quality score and retains 97.1% of its 16K-budget performance with only a 4K output budget. These results show that explicitly selecting and integrating evidence improves long-context reasoning while enabling more compact generation.

### 中文翻译
长上下文理解要求大语言模型（LLM）在冗长的文档、对话和代码上进行推理，然而与任务相关的证据往往稀疏且分散，淹没在大量无关与冗余内容之中。作者提出 **Highlight-Then-Summarize（H2S）**，一种"先压缩后推理"（compress-then-reason）范式：首先识别**源可溯、问题相关**的证据，随后将其整合为紧凑的、以问题为条件的摘要，最后才给出最终答案。为训练这一行为，作者构建了 **H2S-Dataset**，包含来自 11 个基准家族的 6,647 个样本，平均上下文长度为 43.9K token，并提出 **H2S-RL**，在最终答案正确性之外，为证据选择与摘要构建提供**过程级奖励**。作者在七任务的 **H2S-Bench** 长上下文套件上评估。在统一的 128K 输入 / 4K 输出预算下，H2S-14B 取得 32.60 的平均分，比 Qwen3.8-27B 高出 10.17 分，在被评估的开源模型中取得最强整体结果。H2S-14B 还取得了最高的证据-摘要质量（Evidence-Summary Quality）分数，且在仅 4K 输出预算下保留了其 16K 预算性能的 97.1%。这些结果表明，显式地选择并整合证据能改善长上下文推理，同时支持更紧凑的生成。

### 核心要点提炼
- **研究背景**：长上下文任务中，相关证据稀疏、分散，LLM 直接"硬读"整段长文既贵又易被噪声干扰。
- **研究动机**：能否在生成最终答案前，先把证据"挑出来并压缩"，让模型在小预算下高质量推理。
- **核心方法**：H2S = 高亮（选出源可溯的相关证据）→ 摘要（整合成问题条件化的紧凑摘要）→ 推理（最终答案），并用 H2S-RL 提供过程级奖励。
- **主要结果**：H2S-14B 平均 32.60，超 Qwen3.8-27B 达 10.17 分；4K 输出预算保留 16K 预算 97.1% 性能。
- **研究意义**：证明"显式证据选择 + 整合"能同时提升长上下文推理质量与生成紧凑性。

## 研究问题

### 核心研究问题
如何让 LLM 在长上下文中**先显式筛选并压缩证据、再推理**，从而在小输出预算下依然保持高质量理解？

现有方法痛点：
1. **直接全文推理**：长上下文注意力开销大，且无关/冗余内容干扰判断；
2. **检索增强（RAG）**：粗粒度检索可能漏掉分散证据，且检索与生成解耦；
3. **单纯"压缩上下文"**：压缩可能丢失关键证据或破坏可溯源性。

## 方法概述

### 核心思想
把"长上下文理解"拆成显式的两步：**Highlight（高亮/选择证据）→ Summarize（摘要整合）**，再进入推理。证据必须"源可溯、问题相关"，摘要必须"以问题为条件"，从而在小预算内保留关键信息。

![[H2S_pipeline_page1.png|800]]

> 图：H2S 两阶段范式——先高亮源可溯的相关证据，再整合为问题条件化的紧凑摘要，最后推理出答案。

### 方法框架

**1. Highlight（证据选择）**
- 从长上下文中识别与问题相关、且可溯源到原文的证据片段。

**2. Summarize（证据整合）**
- 将选出的证据整合为一段紧凑的、以问题为条件的摘要。

**3. Reason（最终推理）**
- 基于紧凑摘要而非原始长文生成最终答案。

**4. H2S-RL 训练**
- 在最终答案正确性之外，为证据选择与摘要构建提供**过程级奖励**，引导模型"正确地压缩"。

### 关键创新
1. **compress-then-reason 范式** - 把证据压缩显式建模为可训练、可奖励的中间步骤。
2. **H2S-RL 过程级奖励** - 不仅奖励答案，还奖励"证据选得对不对、摘要压得好不好"。
3. **极强预算效率** - 4K 输出预算即可保留 16K 预算 97.1% 的性能。

## 实验结果

### 数据集
- **H2S-Dataset**：6,647 例，来自 11 个基准家族，平均上下文 43.9K token；
- **H2S-Bench**：七任务长上下文评测套件。

### 主要结果
- **平均分 32.60**（128K 输入 / 4K 输出），超 Qwen3.8-27B 达 **10.17 分**，开源最强。
- **证据-摘要质量最高**：显式证据整合显著优于直接推理。
- **预算鲁棒性**：4K 输出预算保留 16K 预算 **97.1%** 性能。

### 实验结果图

![[H2S_datapipeline_page1.png|800]]

> 图：H2S-Dataset 的数据构造流程。

![[dataset_composition_by_input_tokens_page1.png|800]]

> 图：数据集按输入 token 的构成分布。

## 深度分析

### 研究价值
- **理论贡献**：将"证据压缩"从工程技巧提升为可监督、可奖励的学习目标，给出 compress-then-reason 的完整训练范式。
- **实际应用**：长文档、对话、代码理解场景的推理成本可大幅下降（小输出预算即可保持精度）。
- **领域影响**：为长上下文 LLM 与 RAG 的融合提供了新范式，过程级奖励的"压缩监督"思路值得推广。

### 优势
- 推理质量与生成紧凑性兼得（32.60 分 / 4K 预算）。
- 证据"源可溯"，可解释性优于黑盒压缩。
- 训练数据与奖励设计系统化，可复现性好。

### 局限性
- 评测以 14B 模型为主，更大规模模型的收益曲线未充分展示。
- "高亮"步骤对检索质量的依赖可能成为瓶颈。
- 摘要引入的潜在信息丢失（即使 97.1% 保留）在极端任务上仍需警惕。

### 适用场景
- 长文档 QA、会议纪要/合同/代码库的推理；
- 需要小输出预算、强可溯源的推理系统。

## 技术路线定位
本文属于**长上下文理解 + 检索增强（RAG）**技术路线，具体子方向为**证据压缩驱动的 compress-then-reason**，是"先检索/压缩、后推理"范式在过程级奖励下的系统化实现。

## 未来工作建议
1. 在更大规模模型（如 70B+）上验证收益曲线。
2. 探索 H2S 与检索器（retriever）的端到端联合优化。
3. 将过程级压缩奖励泛化到更多模态与任务类型。

## 我的综合评价

### 价值评分
- **总体评分**：**8.5/10** - 范式清晰、实验扎实、预算效率惊艳，是长上下文推理的代表性工作。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | compress-then-reason + 过程级压缩奖励有新意 |
| 技术质量 | 9/10 | 数据、奖励、评测三件套设计完整 |
| 实验充分性 | 8/10 | 七任务评测充分，但规模上限未探明 |
| 写作质量 | 9/10 | 逻辑清晰、指标交代到位 |
| 实用性 | 9/10 | 预算效率高，直接服务长上下文落地 |

### 突出亮点
- 4K 输出预算保留 97.1% 性能，成本效率是核心价值。
- 过程级奖励让"证据压缩"可学习、可优化，而非工程补丁。

### 可借鉴点
- "高亮 + 摘要"的两阶段压缩监督，可作为其他长上下文/RAG 系统的通用增强。

### 批判性思考
- 32.60 的绝对分数对任务本身而言是否仍偏低？"相对开源最强"与"实用可用"之间还有距离。

## 相关论文
- [[20_Research/Papers/大语言模型|大语言模型]] - 相关领域

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.31382)
- [PDF](https://arxiv.org/pdf/2609.31382)

> [!tip] 关键启示
> 让模型先"挑出并压缩证据"再推理，比让它硬读整段长文更准也更省。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读——长上下文理解的"先压缩后推理"范式，预算效率与方法论俱佳。
