---
date: "2026-10-23"
paper_id: "arXiv:2610.31586"
title: "Would an LLM Match into a Residency? Uncertainty, Reasoning and Image Use of Open Language Models on the Spanish MIR Examination"
authors: "Miguel Fernandez-de-Retana, Unai Zulaika, Ruben Sanchez-Corcuera, Aritz Bilbao-Jayo, Aitor Almeida"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM
  - Medical
  - Uncertainty
  - Reasoning
  - Multimodal
quality_score: "7.0/10"
related_papers: []
created: "2026-10-23"
updated: "2026-10-23"
status: analyzed
---

# Would an LLM Match into a Residency? Uncertainty, Reasoning and Image Use of Open Language Models on the Spanish MIR Examination

## 核心信息
- **论文ID**：arXiv:2610.31586
- **作者**：Miguel Fernandez-de-Retana, Unai Zulaika, Ruben Sanchez-Corcuera, Aritz Bilbao-Jayo, Aitor Almeida
- **机构**：--
- **发布时间**：2026-10-22
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.CY / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.31586) | [PDF](https://arxiv.org/pdf/2610.31586)
- **引用**：--

## 摘要翻译

### 英文摘要
Accuracy on medical examinations is common evidence offered for the clinical use of language models, yet a health system that intends to run an open model on its own hardware needs to know considerably more: whether the model recognizes when it is likely to be wrong, whether medical fine-tuning improves this ability, whether its correct answers rest on sound clinical reasoning, or whether it actually uses the images it is given. We examine these questions for four open models of 8 to 27 billion parameters on the Spanish MIR residency examination, scoring 1,552 questions from eight editions under the exam's own rules. Answering every question, the three larger models would rank among roughly the first 600 to 3,000 candidates, with 41 to 43 specialties still open to them. Abstention, however, never pays, because even their least confident answers are right more often than the 25% break-even. Medical fine-tuning reduced overconfidence at the cost of accuracy, without improving how well confidence separates right from wrong answers. Moreover, 13-38% of correct answers rejected a distractor for a clinically unsound reason that confidence did not reflect, and removing or swapping the provided contextual images left accuracy unchanged.

### 中文翻译
医学考试的准确率常被当作语言模型可用于临床的证据，但一个打算在自有硬件上运行开源模型的医疗系统需要知道更多：模型能否识别自己何时可能出错、医学微调能否提升这种能力、其正确答案是否建立在可靠的临床推理之上、以及它是否真的用到了所给的图像。本文对 4 个 80 亿到 270 亿参数的开源模型在西班牙 MIR 住院医师考试上考察这些问题，按考试自身规则对 8 届共 1,552 道题评分。若答全部题，3 个较大模型大致排在前 600 到 3000 名候选，仍有 41 到 43 个专科可选。然而弃权从不划算——即使是最不自信的答案，其正确率也高于 25% 的盈亏平衡。医学微调以牺牲准确率为代价降低了过度自信，但并未改善置信度区分对错的能力。此外，13-38% 的正确回答是以临床上不可靠的理由排除了干扰项（置信度并未反映这一点），而移除或交换所给的情境图像，准确率不变。

### 核心要点提炼
- **研究背景**：医学考试准确率是 LLM 临床能力的常见证据，但部署方关心的远不止准确率。
- **研究动机**：开源模型自部署需要「何时错」「推理是否可靠」「是否用图」等可检验证据。
- **核心方法**：在西班牙 MIR 考试上对 4 个开源模型做 4 维评估——不确定性、医学微调效应、临床推理质量、图像使用。
- **主要结果**：3 个较大模型可进前 600-3000 名；弃权不划算；微调降低过度自信但降准确率且不提升判别；13-38% 正确答案推理「临床不可靠」；图像对准确率无影响。
- **研究意义**：为「考试高分 ≠ 临床可用」提供了系统的开源模型侧证据。

## 研究背景与动机

### 领域现状
医疗 LLM 评估大量依赖医学考试（如 USMLE、MIR）的准确率作为「临床就绪」的证据，但准确率掩盖了模型「何时错、为何对」的深层问题。

### 现有方法的局限性
- 只报准确率，不评估模型对自身不确定性的认知。
- 不区分「正确答案」是否来自可靠临床推理。
- 多模态医学模型常被假设「在用图」，但缺乏验证。

### 研究动机
面向「想在自有硬件上跑开源医学模型」的医疗系统，回答四个比准确率更关键的部署问题。

## 研究问题

核心问题：开源 LLM 在西班牙 MIR 考试上，(1) 能否识别自己可能出错（不确定性校准）？(2) 医学微调是否提升这种能力？(3) 正确答案是否建立在可靠临床推理上？(4) 模型是否真的使用了所提供的图像？

## 方法概述

### 核心思想
把「考试准确率」拆解为四个可独立检验的维度，用 4 个开源模型（8B–27B）在 8 届 MIR 共 1,552 题上按考试规则打分，逐一给出证据。

### 方法框架
1. **模型**：4 个开源模型（80 亿–270 亿参数）。
2. **数据**：8 届西班牙 MIR 住院医师考试，共 1,552 题，按考试自身评分规则。
3. **四维评估**：
   - 不确定性：置信度 vs 正确率的分离度（弃权盈亏平衡 25%）。
   - 医学微调：对比微调前后过度自信与判别能力。
   - 临床推理：检查正确回答是否以「临床可靠理由」排除干扰项。
   - 图像使用：移除/交换情境图像，看准确率是否变化。

![[2610.31586_fig1.png|600]]

> 图1：MIR 考试多模型四维评估概览。

### 关键创新
1. **四维解耦评估**——把「考试高分」拆成可独立验证的部署问题。
2. **推理可靠性审计**——量化「正确但理由临床不可靠」的占比（13-38%）。
3. **图像使用反事实检验**——通过移除/交换图像证明模型实际未利用图像。

## 实验结果

### 数据集
- 西班牙 MIR 住院医师考试，8 届、1,552 题。

### 实验设置
- **模型**：4 个开源模型（8B–27B）。
- **评估指标**：准确率、置信度-正确率分离度、弃权盈亏平衡、临床推理可靠性、图像敏感性。

### 主要结果
| 维度 | 结果 |
|------|------|
| 全部作答排名 | 3 个较大模型 ≈ 前 600–3000 名；41–43 专科可选 |
| 弃权收益 | 从不划算（最低置信度仍 > 25% 盈亏平衡） |
| 医学微调 | 降低过度自信，但降低准确率，未提升判别 |
| 推理可靠性 | 13–38% 正确回答的理由「临床不可靠」 |
| 图像使用 | 移除/交换图像，准确率不变 |

### 结果分析
- 模型「高准确率」背后存在大量「碰巧对」——推理不可靠。
- 置信度无法区分这些「不可靠的正确」，是部署的最大隐患。
- 所谓多模态医学模型，实际未用图像，多模态能力名不副实。

## 深度分析

### 研究价值
- **理论贡献**：为医学 LLM 部署提供了「准确率之外的四个问题」系统评估范式。
- **实际应用**：直接服务于开源医学模型自部署决策。
- **领域影响**：质疑「考试高分即临床可用」的流行叙事。

### 优势
- 评估维度全面、贴近真实部署关切。
- 图像使用反事实检验设计巧妙，结论有说服力。
- 开源模型侧证据，可复现。

### 局限性
- 仅覆盖西班牙 MIR，语言/题型单一。
- 仅 4 个开源模型，未对比闭源模型。
- 临床推理「可靠性」判定标准需进一步细化。

### 适用场景
- 医疗系统开源模型选型前的风险审计。
- 多模态医学模型的图像使用验证。

## 与相关论文对比

- 与 [[Med-PaLM|医疗 LLM 基准]] 路线对比：本文不做榜单刷分，转而审计「分数背后的可靠性」。
- 与多模态医学 VQA 研究对比：用反事实检验揭示模型未真正用图，纠正「多模态即用了图」的假设。

## 技术路线定位

本文属于 **医疗 LLM 评估** 技术路线，主要关注「部署就绪度审计」子方向：准确率之外的可靠性、推理与多模态真实性。

## 未来工作建议
1. 扩展到多语言、多题型医学考试。
2. 引入闭源模型对比与真实临床病例。
3. 将「推理可靠性」判定标准化，形成可复现审计协议。

## 我的综合评价

### 价值评分
- **总体评分**：**7.0/10**
- **分项评分**：
  - 创新性：7/10（四维解耦 + 图像反事实，视角新颖）
  - 技术质量：7/10（设计合理，但推理可靠性判定偏主观）
  - 实验充分性：6/10（模型与语言覆盖有限）
  - 写作质量：8/10（问题驱动，清晰）
  - 实用性：8/10（对开源医学模型部署有直接价值）

### 突出亮点
- 「13-38% 正确回答理由临床不可靠」这一量化发现。
- 图像反事实检验证明多模态能力名不副实。
- 弃权不划算的反直觉结论。

### 重点关注
- 置信度无法识别「不可靠的正确」这一部署隐患。
- 多模态医学模型「未用图」的广泛含义。

### 可借鉴点
- 反事实检验（移除/交换输入）可推广到其他多模态任务，验证是否真用了某模态。

### 批判性思考
- 「临床不可靠理由」的标注标准未完全公开，可能存在评判者偏差。
- 弃权盈亏平衡 25% 依赖 MIR 计分规则，迁移需重算。

## 我的笔记

%%

## 相关论文
- [[Med-PaLM]] - 医疗 LLM 基准路线

## 外部资源
- [arXiv](https://arxiv.org/abs/2610.31586)
- [PDF](https://arxiv.org/pdf/2610.31586)

> [!success] 推荐指数
> ⭐⭐⭐⭐ 一篇「别只看考试分数」的医疗 LLM 部署审计，对做医疗/多模态模型的人很有启发。
