---
date: "2026-08-18"
paper_id: "arXiv:2608.15869"
title: "Beyond Visual CoT: Internalized Visual Thinking for Proactive Video Reasoning"
authors: "Xiaoyu Zhu, Xinke Deng, Suresh Taddewadikar, Arnab Kumar Mondal, Zhongyu Jiang, Ian Fasel, Joerg Liebelt"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态
  - Vision-Language
  - Video-Reasoning
  - Visual-CoT
  - World-Model
quality_score: "8.5/10"
related_papers: []
created: "2026-08-18"
updated: "2026-08-18"
status: analyzed
---

# Beyond Visual CoT: Internalized Visual Thinking for Proactive Video Reasoning

## 核心信息
- **论文ID**：arXiv:2608.15869
- **作者**：Xiaoyu Zhu, Xinke Deng, Suresh Taddewadikar, Arnab Kumar Mondal, Zhongyu Jiang, Ian Fasel, Joerg Liebelt
- **机构**：--
- **发布时间**：2026-08-16
- **会议/期刊**：arXiv 预印本（cs.CV / cs.AI / cs.CL / cs.LG / cs.MM）
- **链接**：[arXiv](https://arxiv.org/abs/2608.15869) | [PDF](https://arxiv.org/pdf/2608.15869)
- **引用**：--

## 摘要翻译

### 英文摘要
Multimodal large language models increasingly use visual chain-of-thought (Visual CoT) to reason about spatial, temporal, and embodied environments. By generating intermediate reasoning images, Visual CoT provides an intuitive mechanism for visual foresight but introduces substantial inference overhead, which is particularly problematic for proactive video reasoning. We ask whether models can learn to think visually during training while reasoning directly at inference. We introduce Internalized Visual Thinking (IVT), a post-training framework that jointly optimizes textual prediction and next-embedding prediction over unlabeled videos. Given a partially observed video, IVT predicts latent representations of future frames together with the target textual answer, encouraging the model to capture motion, object transitions, interactions, and latent intent. At inference, IVT generates the answer directly without synthesizing or re-encoding future frames.

### 中文翻译
多模态大语言模型越来越多地采用视觉思维链（Visual CoT）来推理空间、时间与具身环境。通过生成中间的推理图像，Visual CoT 为视觉预判提供了一种直观机制，但也带来了显著的推理开销，这对主动式视频推理尤其成问题。本文追问：模型能否在训练阶段学会"用视觉思考"，而在推理时直接推理？作者提出 Internalized Visual Thinking（IVT），一个联合优化文本预测与下一帧嵌入预测的后训练框架。给定一段部分观测的视频，IVT 同时预测未来帧的隐式表征与目标文本答案，促使模型捕捉运动、物体切换、交互与潜在意图。在推理阶段，IVT 无需合成或重新编码未来帧，直接生成答案。

### 核心要点提炼
- **研究背景**：多模态大模型用 Visual CoT 做视频/具身推理，但逐帧生成推理图像带来巨大延迟开销。
- **研究动机**：主动式视频推理（预测未来并做决策）需要高效推理，显式像素级生成是瓶颈。
- **核心方法**：IVT 在训练时让模型同时预测"下一帧的隐式表征"和"目标文本答案"，把预测式世界建模"内化"进模型。
- **主要结果**：六个评测设置上全面超越直接答案微调；相比显式 Visual CoT，性能相当或更优，端到端延迟降低 5 倍以上。
- **研究意义**：证明推理时的显式像素生成并非主动视频推理所必需，为高效多模态推理器提供新范式。

## 研究背景与动机

### 领域现状
视觉思维链（Visual CoT）已成为多模态大模型处理空间、时间与具身推理的主流手段。其核心做法是在生成最终答案前，先显式生成中间"推理图像"（如预测未来帧），让模型对视觉场景形成可解释的预判。

### 现有方法的局限性
- **推理开销大**：逐帧合成/重新编码中间图像，端到端延迟高，难以用于需要快速反应的主动式视频推理。
- **显式像素生成为必需假设**：现有范式默认"可视化预判"必须落到像素空间，但这个假设未被严格检验。

### 研究动机
本文的核心假设是：预测式世界建模可以被"内化"到模型参数中，推理时无需显式生成像素。若能验证，即可在保持（甚至提升）精度的同时，大幅降低延迟。

## 研究问题

### 核心研究问题
模型能否在**训练时**学会视觉思考、在**推理时**直接推理，从而在不牺牲准确率的前提下消除 Visual CoT 的像素生成开销？

## 方法概述

### 核心思想
IVT 通过一个额外的"下一帧嵌入预测"目标，把对未来帧的预测式建模压缩进模型的隐空间，而非像素空间。给定部分观测视频，模型同时学习输出未来帧的**隐式表征**与**目标文本答案**，从而在推理时直接生成答案。

### 方法框架

![[fig1_paradigms_page1.png|600]]

> 图1：IVT 范式示意——训练阶段联合优化文本预测与下一帧嵌入预测，推理阶段直接生成答案，无需显式视觉生成。

#### 各模块详细说明

**训练目标（双任务联合优化）**
- **文本预测**：给定部分观测视频，生成目标文本答案。
- **下一帧嵌入预测**：预测未来帧的隐式表征，促使模型捕获运动、物体切换、交互与潜在意图。
- **关键设计**：两条任务共享视觉编码器的隐空间，使"预判未来"的能力被内化。

**推理阶段**
- 模型直接生成答案，不再合成或重新编码未来帧，推理通路与普通直接答案微调一致。

### 关键创新
1. **内化式视觉思维（IVT）**：把预测式世界建模从"像素空间"迁移到"隐空间"，推理时零额外生成开销。
2. **下一帧嵌入预测目标**：用无标注视频即可训练，数据高效。
3. **系统性受控研究**：覆盖目标表征、解码器设计、预测时域、数据配比、训练课程与预测目标，验证结论的稳健性。

## 实验结果

### 数据集与评测设置
- 六个评测设置（涉及视频推理、主动预判等），涵盖不同目标表征与解码器设计。

### 主要结果
- **全面超越直接答案微调**：IVT 在全部六个评测设置上均优于 direct-answer fine-tuning。
- **对比显式 Visual CoT**：性能相当或更优，同时端到端平均延迟降低 **5 倍以上**。
- **结论**：推理时的显式像素空间生成并非有效主动视频推理所必需。

## 深度分析

### 研究价值
- **理论贡献**：挑战了"视觉预判必须显式生成像素"的既有范式，提出"隐式世界建模"的新思路。
- **实际应用**：为需要低延迟的视频推理（自动驾驶、具身智能、视频问答）提供直接可用的效率提升路径。
- **领域影响**：为多模态推理器的效率优化提供了一个不牺牲精度的新方向。

### 优势
- 推理通路不变，易于部署与迁移。
- 用无标注视频训练，数据获取成本低。
- 受控研究设计严谨，结论可信度高。

### 局限性
- 论文为预印本，尚未经过同行评审。
- "下一帧嵌入"的具体表征形式（如 VAE 隐码、视觉 token）的选择对效果的影响仍需更多跨任务验证。
- 主动视频推理的评测基准尚未完全标准化，泛化性有待后续验证。

## 我的综合评价

### 价值评分
- **总体评分**：**8.5/10**
- **分项评分**：
  - 创新性：8/10（范式级改进：内化式视觉思维）
  - 技术质量：8/10（方法简洁，受控实验严谨）
  - 实验充分性：8/10（六种设置 + 5x 延迟对比）
  - 写作质量：8/10
  - 实用性：8/10（低延迟视频推理需求明确）

### 突出亮点
- "内化而非显式生成"的核心洞察。
- 5x 延迟降低且精度不降，工程价值高。
- 无标注视频训练，扩展性强。

### 可借鉴点
- 把昂贵的显式生成目标替换为隐式表征预测，是通用的效率优化思路。
- 联合优化"预测未来 + 回答当前"的双任务范式，可迁移到其他多模态预判任务。

## 相关论文
- 与 Visual CoT / Video CoT 系列工作构成对比关系（显式生成 vs 内化预测）。
- 与预测式世界模型（world model）路线相关。

## 外部资源
- [arXiv](https://arxiv.org/abs/2608.15869)
- [PDF](https://arxiv.org/pdf/2608.15869)

> [!tip] 关键启示
> 推理时显式生成像素并非视觉推理的必需品——把"预判未来"内化进隐空间，可在不损失精度的前提下换回 5 倍以上的延迟收益。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 值得精读：多模态推理效率优化的重要新方向。
