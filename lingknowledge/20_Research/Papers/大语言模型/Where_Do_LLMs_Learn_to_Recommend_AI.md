---
date: "2026-10-22"
paper_id: "arXiv:2610.29785"
title: "Where Do LLMs Learn to Recommend AI?"
authors: "Benaya Trabelsi, Jonathan Shaki, Sarit Kraus"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 推荐系统
  - 偏见
  - Pro-AI-Bias
  - 后训练
  - 提示词
quality_score: "8.2/10"
related_papers: []
created: "2026-10-22"
updated: "2026-10-22"
status: analyzed
---

# Where Do LLMs Learn to Recommend AI?

## 核心信息
- **论文ID**：arXiv:2610.29785
- **作者**：Benaya Trabelsi, Jonathan Shaki, Sarit Kraus
- **机构**：Bar-Ilan University（以色列巴伊兰大学）
- **发布时间**：2026-10-21
- **会议/期刊**：arXiv（cs.CL / cs.AI / cs.CY / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.29785) | [PDF](https://arxiv.org/pdf/2610.29785)
- **引用**：--

## 摘要翻译

### 英文摘要
Asked what to study, which career to pursue, or what product to build, large language models (LLMs) often recommend AI-related options. We ask where this tendency comes from by comparing models before and after fine-tuning and by varying how questions are asked. First, pretrained models already include explicit AI/ML terms in the recommendations of 27% of their responses, and their released instruction-tuned versions do so in 59%. Second, fine-tuning models ourselves shows that this rise is not inevitable: general instruction tuning leaves the rate unchanged, but removing the 7% of instruction examples that concern AI cuts it by 14 percentage points, below the level of the pretrained models it starts from. Data rewarding helpfulness raise it by 9 points, while data ranked by the preferences of diverse simulated users lower it by 9. Finally, with models held fixed, asking for the "best" option or for what to do "right now" raises the rate by 8 and 18 points, whereas telling the model it is an AI advisor adds 4. Overall, the tendency starts in pretraining, and what models learn afterward, together with how a question is phrased, decides how strongly it appears.

### 中文翻译
当被问及"该学什么、该从事什么职业、该打造什么产品"时，大语言模型往往会推荐与 AI 相关的选项。本文探究这一倾向从何而来，方法是对比微调前后的模型，并变化提问方式。首先，预训练模型已有 27% 的回复包含显式 AI/ML 术语，而其发布的指令微调版本这一比例高达 59%。其次，作者自行微调模型发现，这一上升并非必然：通用指令微调几乎不改变该比例；但若移除指令数据中 7% 与 AI 相关的样本，该比例会下降 14 个百分点，甚至低于起始预训练模型的水平。奖励"有用性"的数据会使其上升 9 个百分点，而按多样化模拟用户偏好排序的数据会使其下降 9 个百分点。最后，在模型固定不变时，询问"最好"的选项或"现在"该做什么会分别使比例上升 8 和 18 个百分点，而告诉模型它是"AI 顾问"只增加 4 个百分点。总体而言，这一倾向始于预训练，而模型之后学到的东西以及提问方式共同决定其强弱。

### 核心要点提炼
- **研究问题**：LLM 频繁推荐 AI 相关选项（"亲 AI 倾向"）——这一系统性倾向来自哪里？
- **四个假设**：H1 预训练先验；H2 后训练；H3 AI 身份/上下文；H4 提示词措辞。
- **核心方法**：20 个开放式建议问题 × 4 领域 × 10 种改写，用"推荐中是否含显式 AI/ML 术语"作为统一度量，配合受控训练树与双重 LLM 过滤器。
- **主要结果**：预训练已有（26.8%）→ 发布版指令模型更高（59.28%）；通用 SFT 几乎不变（+0.02pp），但移除 AI 样本的 Clean no-AI SFT 骤降（−13.89pp）；Helpfulness DPO 升（+9.48pp）、Persona DPO 降（−8.95pp）；措辞"right now"（+17.88pp）与"best"（+8.10pp）的影响远大于"自称为 AI"（+1.24pp，不显著）。

## 研究背景与动机

### 领域现状
LLM 的推荐会塑造用户考虑的选项乃至最终决策，因此其中的系统性倾向值得深入理解，即使单条建议看起来合理。此前工作已记录"亲 AI 显著性"（pro-AI prominence）现象——即 LLM 在开放式建议中频繁纳入 AI 相关内容，以及在配对任务中对 AI 案例给出更有利的估计（Trabelsi et al. 2026）。但这一倾向的**来源**尚不清晰：当指令模型推荐 AI 时，无法从单条建议判断这是预训练行为、后续训练、系统上下文还是提问措辞造成的。

### 现有方法的局限性
- 现有研究要么只"记录现象"（Trabelsi et al. 2026），要么比较"发布版模型"或"整体替换指令数据集"（Itzhak et al. 2024/2025），无法隔离具体训练配方的作用。
- 提示词敏感性研究（Mizrahi et al. 2024；Sclar et al. 2024）未针对"AI 推荐"这一特定结果。

### 研究动机
作者提出一个系统框架，把"亲 AI 倾向"分解为四个可独立检验的假设（H1–H4），通过**受控训练树**（controlled training tree）逐配方隔离后训练的影响，并通过**受控措辞实验**隔离提示词与 AI 身份标签的影响。

## 研究问题

### 核心研究问题
LLM 在开放式建议中频繁推荐 AI 相关选项，这一"亲 AI 倾向"分别有多少来自：预训练先验、后训练、AI 身份/上下文、以及提示词措辞？

## 方法概述

### 核心思想
用统一的**显式 AI/ML 术语检测**度量（固定词典 + 两个 LLM 过滤器剔除推荐外的提及），配合四类受控对比，把"亲 AI 倾向"的来源拆解清楚。

![[2610.29785_page2.png|600]]

> 图1：研究设计——四个可能来源及其检验对比；受控分支与各自的直接父节点比较，共享"推荐中含显式 AI/ML 术语"这一统一结果变量。

### 方法框架

#### 任务与度量
- **任务**：4 领域（Study/Career/Startup/Scientific Research）× 5 概念问题 × 10 种改写 = 20 个概念问题（统计单元）。
- **度量**：推荐中是否含显式 AI/ML 术语（artificial intelligence、machine learning、deep learning、LLMs、generative AI 等）。先用固定词典检测，再用 Qwen3.6-27B 与 DeepSeek-V4-Flash 两个过滤器剔除推荐外的提及，两过滤器等权平均。
- **统计**：20 个问题为单元，20,000 次 bootstrap 置信区间 + 全 2²⁰ 符号翻转精确检验 + Holm 校正（H1/H2/H3/H4 家族内分别校正）。

#### 四类对比
- **H1（预训练）**：10 对发布版 Base–Instruct 模型（5 个家族），同一批问题/提示。
- **H2（后训练）**：11 条受控训练树模型（6 家族）。General SFT 与 Clean no-AI SFT 从 Base 出发；Helpfulness/Safety DPO、Role SFT、Persona DPO 从 General SFT 出发，各与直接父节点对比。
- **H3（AI 身份）**：系统消息中"advisor vs AI advisor""grammar vs AI grammar"两对匹配消息。
- **H4（措辞）**：superlative（best/strongest/most promising）与 temporal（right now/currently/at present）两个线索的 2×2 因子设计。

## 实验结果

### 主要结果（单位：百分点 pp，均 Holm 校正后显著，除非注明）

**H1 预训练先验**
- 发布版 Base → Instruct：**+32.48 pp**（26.80% → 59.28%）。

**H2 后训练（受控）**
- General SFT：+0.02 pp（不显著，几乎不变）
- Clean no-AI SFT（移除 7.2% AI 样本）：**−13.89 pp**（24.64% → 10.74%）
- Helpfulness DPO：**+9.48 pp**
- Persona DPO：**−8.95 pp**
- Safety DPO：−2.34 pp
- Role SFT：+2.44 pp（不显著）

**H3 AI 身份/上下文**
- AI advisor − advisor：+3.60 pp
- AI grammar − grammar：+2.36 pp
- "自称为 AI 的额外效应"：+1.24 pp（**不显著**）

**H4 提示词措辞**
- Temporal 线索：**+17.88 pp**（47.05% → 63.80%）
- Superlative 线索：+8.10 pp
- 交互：+2.25 pp（不显著，可加性成立）

### 关键结论
1. 亲 AI 倾向始于预训练，发布版指令模型大幅放大（+32pp）。
2. 后训练方向由**训练数据/配方**而非"后训练阶段"决定：同一 DPO 方法、同一 checkpoint，Helpfulness 数据升、Persona 数据降，相差约 18pp。
3. 推理时，普通措辞（"right now""best"）的影响远大于"自称为 AI"的身份标签。

## 深度分析

### 研究价值评估

#### 理论贡献
- 首次系统地把"亲 AI 推荐倾向"分解为预训练/后训练/身份/措辞四个来源，并给出受控实验证据，厘清了"阶段 vs 配方 vs 措辞"的贡献边界。

#### 实际应用价值
- 对 LLM 建议的评估与治理有直接意义：开发者可通过选择训练数据与偏好目标塑造亲 AI 显著性；对 LLM 建议的评估应测试同一请求的多种措辞。

### 方法优势详解
- **受控训练树**：逐配方与直接父节点对比，隔离了单一训练阶段的影响，远比"比较发布版模型"更严格。
- **严格统计**：以概念问题为统计单元、bootstrap + 符号翻转精确检验 + Holm 校正，避免了把 10 种改写当作独立样本的伪重复。
- **双重过滤器 + 人类验证**：两个 LLM 过滤器估计几乎一致（r=0.990），与人类专家一致性 85.2%（精确率 96.9%、召回率 86.6%）。

### 局限性分析
- **度量只测"显式术语"**：不评估建议质量、不捕捉隐式 AI 推荐，绝对比例偏保守（召回 86.6%）。
- **无法定位预训练内部来源**：只证明倾向"已在预训练中存在"，不能确定预训练语料中的具体机制。
- **受控分支单次训练**：每条配方每个模型仅一次训练运行，且统计推断把模型视为固定、重采样问题，跨模型/语言/领域的泛化未知。

### 适用性与场景分析
- **适用**：LLM 建议系统的偏见审计、训练数据配比决策、提示词设计。
- **不适用**：需要判断"AI 推荐是否超出替代方案实际适配度"的规范性结论（作者明确留待后续）。

## 技术路线定位

本文属于 **LLM 偏见溯源（bias attribution）** 技术路线，核心特点是"用受控训练树 + 受控措辞实验，把系统性行为倾向归因到具体来源"。

```
现象记录（Trabelsi et al.）→ 来源分解（本文：H1–H4）→ 未来：AI 推荐适配度的规范性判断
```

### 具体子方向
LLM 推荐系统偏见中的"亲 AI / 自我偏好（self-preference）"子方向：AI 作为被推荐对象的偏好，与"自我偏好""供应商偏见"并列。

## 未来工作建议

1. **隐式 AI 推荐检测**：超越显式术语，捕捉语义层面的 AI 推荐。
2. **预训练机制定位**：探究预训练中哪类 AI 相关语料驱动了这一倾向。
3. **规范性评估**：在匹配用户需求与已知适配度差异下，比较 AI 与非 AI 推荐是否真的"过度"。

## 我的综合评价

### 总体评分
**8.2/10** —— 问题重要、实验设计严谨（受控训练树 + 精确检验）、结论清晰且可操作，但度量只覆盖显式术语、训练规模有限。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次把"亲 AI 倾向"系统归因到四个来源 |
| 技术质量 | 9/10 | 受控训练树 + bootstrap/精确检验 + 双重过滤器，方法论扎实 |
| 实验充分性 | 8/10 | 10/11 模型、6 配方、4 领域，规模可观但单次训练运行 |
| 写作质量 | 9/10 | 假设清晰、结论与证据一一对应 |
| 实用性 | 8/10 | 对训练数据/提示词设计有直接指导意义 |

## 相关论文

### 直接相关
- Trabelsi et al. 2026（Pro-AI bias in LLMs，arXiv:2601.13749，本文的姊妹工作）

### 背景相关
- LLM 自我偏好（Panickssery et al. 2024）；供应商偏见（Zhang et al. 2025）
- 训练阶段偏见（Itzhak et al. 2024/2025）；RLHF 放大迎合（Shapira et al. 2026）
- 提示词敏感性（Mizrahi et al. 2024；Sclar et al. 2024）

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.29785)

> [!tip] 关键启示
> 亲 AI 倾向是"可塑造"的：它始于预训练，但后训练数据配方与提问措辞（"right now""best"）能大幅改变其强弱——而"自称为 AI"的影响反而很小。评估 LLM 建议必须多措辞、多配方地测。

> [!warning] 注意事项
> - 只测显式 AI/ML 术语，隐式 AI 推荐被低估
> - 无法定位预训练语料中的具体机制
> - 统计推断视模型为固定，跨模型/语言泛化未知
