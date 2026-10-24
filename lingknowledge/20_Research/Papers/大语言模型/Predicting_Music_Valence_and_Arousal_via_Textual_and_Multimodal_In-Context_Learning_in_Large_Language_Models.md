---
date: "2026-10-24"
paper_id: "arXiv:2610.31202"
title: "Predicting Music Valence and Arousal via Textual and Multimodal In-Context Learning in Large Language Models"
authors: "Chi-En Dai, Chih-Pin Tan, Yi-Hsuan Yang"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM
  - In-Context-Learning
  - Multimodal
  - Music-Emotion
quality_score: "7.0/10"
related_papers: []
created: "2026-10-24"
updated: "2026-10-24"
status: analyzed
---

# Predicting Music Valence and Arousal via Textual and Multimodal In-Context Learning in Large Language Models

## 核心信息
- **论文ID**：arXiv:2610.31202
- **作者**：Chi-En Dai, Chih-Pin Tan, Yi-Hsuan Yang
- **机构**：--
- **发布时间**：2026-10-22
- **会议/期刊**：arXiv 预印本（cs.SD / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2610.31202) | [PDF](https://arxiv.org/pdf/2610.31202)
- **引用**：--

## 摘要翻译

### 英文摘要
While arousal can be reliably predicted from musical audio, emotional valence remains difficult to model due to its subjective and context-dependent nature. In this work, we investigate the potential of in-context learning (ICL) with large language models to advance continuous valence and arousal prediction. Rather than predicting emotion scores directly, our framework leverages contextual demonstrations from an auxiliary dataset to refine the initial predictions produced by a small, task-specific base model. We systematically evaluate a text-based ICL variant using lyrics and generated audio captions, alongside a multimodal variant that incorporates raw audio signals. Using the entire MER60 dataset strictly as a held-out test set, our experiments demonstrate substantial performance gains, particularly for valence. Text-based ICL improves the valence R² from 0.110 to 0.427 with 80 demonstration shots, while the multimodal variant achieves 0.346 with only 10 shots. These findings suggest that training-free ICL offers an effective inference-time strategy for current language models to predict continuous music emotion.

### 中文翻译
虽然唤醒度（arousal）能从音乐音频中被可靠预测，情绪效价（valence）由于其主观性与情境依赖而难以建模。本文研究利用大语言模型的上下文学习（ICL）来推进连续的效价与唤醒度预测。不同于直接预测情绪分数，该框架利用来自辅助数据集的上下文示例，来精炼一个小型、任务专用的基础模型所产出的初始预测。本文系统评估了使用歌词与生成的音频描述的文本式 ICL 变体，以及融入原始音频信号的多模态变体。以整个 MER60 数据集严格作为留出测试集，实验展现出显著性能提升，尤其对效价。文本式 ICL 在 80 个演示样本下把效价 R² 从 0.110 提升到 0.427，而多模态变体仅用 10 个样本就达到 0.346。这些发现表明，免训练的 ICL 是当前语言模型预测连续音乐情绪的一种有效推理期策略。

### 核心要点提炼
- **研究背景**：音乐情绪预测中，arousal 易预测、valence 难（主观、情境依赖）。
- **研究动机**：探索 LLM 的免训练 ICL 能否突破 valence 建模瓶颈，且不依赖昂贵微调。
- **核心方法**：用辅助数据集的上下文示例，精炼小型任务专用基础模型的初始预测；分文本式（歌词+音频描述）与多模态（原始音频）两种变体。
- **主要结果**：文本式 ICL 把 valence R² 从 0.110 提至 0.427（80 shots）；多模态 10 shots 达 0.346。
- **研究意义**：证明免训练 ICL 是预测连续音乐情绪的实用推理期策略。

## 研究背景与动机

### 领域现状
音乐情绪识别长期使用「效价-唤醒度」二维连续模型。其中唤醒度（arousal，兴奋/平静）与音频能量高度相关，易被模型预测；效价（valence，积极/消极）高度主观、依赖文化与情境，是长期难点。

### 现有方法的局限性
- 监督微调需要大量标注数据，且跨数据集泛化差。
- 传统音频模型对 valence 的 R² 长期偏低（常 <0.2）。
- 纯音频信号对「情感语义」的表达有限，需要文本/语义补充。

### 研究动机
利用 LLM 强大的语义理解与上下文学习能力，通过「示例精炼」而非「直接预测」的方式，绕过 valence 的标注瓶颈。

## 研究问题

核心问题：**LLM 的上下文学习能否通过少量示例，显著提升连续音乐情绪（尤其是 valence）的预测性能？** 文本式（歌词/描述）与多模态（原始音频）哪种更有效、更省样本？

## 方法概述

### 核心思想
「预测 + 精炼」两段式：先由一个任务专用的小基础模型给出初始情绪预测，再用 LLM 的 ICL 根据辅助数据集的上下文示例对初始预测进行精炼，而非让 LLM 从零预测连续分数。

### 方法框架
1. **基础模型**：小规模、任务专用的情绪预测模型，产出初始 valence/arousal 分数。
2. **文本式 ICL 变体**：以歌词 + 生成的音频描述（audio captions）作为上下文示例，引导 LLM 精炼预测。
3. **多模态 ICL 变体**：在文本之外引入原始音频信号作为额外模态输入。
4. **评估**：严格以整个 MER60 数据集为留出测试集，避免数据泄漏。

![[2610.31202_fig1.png|600]]

> 图1：基于 ICL 的音乐效价/唤醒度预测框架（基础模型初始预测 + LLM 示例精炼）。

### 关键创新
1. **把连续情绪预测重构为「精炼」任务**——让 LLM 修基础模型的分数，而非直接回归连续值。
2. **文本与多模态两种 ICL 变体系统对比**——揭示语义（歌词/描述）与原始音频在情绪建模中的互补性。
3. **免训练推理期策略**——无需微调，直接以少量示例提升性能，尤其利好数据稀缺场景。

## 实验结果

### 数据集
- MER60：整个数据集严格作为留出测试集（held-out）。

### 实验设置
- **基础模型**：小型任务专用情绪预测模型。
- **ICL 变体**：文本式（歌词 + 音频描述）、多模态（原始音频）。
- **评估指标**：valence / arousal 的 R²。

### 主要结果
- **文本式 ICL**：valence R² 从 0.110 → 0.427（80 shots）。
- **多模态 ICL**：valence R² 达 0.346（仅 10 shots）。
- 两类变体对 valence 的提升尤为显著（arousal 本身易预测，提升空间较小）。

### 结果分析
- 文本语义（歌词/描述）对主观性强的 valence 帮助最大。
- 多模态变体仅需 10 个示例即接近文本式 80 示例的效果，说明原始音频信息密度更高、样本效率更好。

## 深度分析

### 研究价值
- **理论贡献**：把 LLM 的 ICL 用于「连续情绪回归的精炼」，扩展了 ICL 的适用边界。
- **实际应用**：为音乐推荐、情绪标注、心理健康等低标注场景提供免训练方案。
- **领域影响**：连接「音乐信息检索」与「LLM 多模态理解」两个社区。

### 优势
- 免训练、推理期即可部署，成本低。
- 严格留出测试，避免数据泄漏，结论可信。
- 揭示文本与音频两种模态的互补性。

### 局限性
- 仅在音乐情绪（MER60）验证，向其他连续回归任务泛化未证明。
- 依赖基础模型的初始预测质量，存在误差累积风险。
- 绝对 R²（0.427）仍偏低，离实用标注还有距离。

### 适用场景
- 音乐情绪识别、连续情感标注、弱标注/低资源情绪预测。

## 技术路线定位

本文属于「LLM 多模态 + 音乐信息检索（MIR）」交叉路线，聚焦「用 ICL 做连续情绪精炼」这一子方向。

## 未来工作建议

1. 将「预测 + 精炼」框架推广到其他连续值回归任务。
2. 研究更多示例选择策略（retrieval-based few-shot）以进一步提升样本效率。
3. 探索更强的多模态 LLM（直接音频理解）替代「文本描述 + 音频」的组合。

## 我的综合评价

### 价值评分
- **总体评分**：7.0/10
- **分项评分**：
  - 创新性：7/10
  - 技术质量：7/10
  - 实验充分性：6/10
  - 写作质量：7/10
  - 实用性：7/10

### 突出亮点
- 「预测 + 精炼」的范式把难问题（连续回归）转成 LLM 擅长的任务。
- 多模态 10 shots 接近文本 80 shots，样本效率结论有实用价值。
- 严格留出测试，方法学严谨。

### 重点关注
- valence R² 0.427 的绝对水平与提升的实际意义。
- 基础模型误差累积对最终性能的制约。

### 可借鉴点
- 「小模型先预测、大模型再精炼」可复用到其他主观/难标注任务。
- 多模态样本效率优势为「音频原生 LLM」提供论据。

### 批判性思考
- 单数据集（MER60）结论偏窄；valence 提升是否来自「语义先验」还是「示例平均」未充分解耦。
- 未与「直接用音频 LLM 端到端预测」的强基线对比。

## 相关论文
- [[BLIP]] - 多模态文本-图像对齐，与本文文本/音频对齐思路相关
- [[CLIP]] - 对比学习跨模态表示，可借鉴其音频-文本对齐

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.31202)
- [PDF](https://arxiv.org/pdf/2610.31202)
