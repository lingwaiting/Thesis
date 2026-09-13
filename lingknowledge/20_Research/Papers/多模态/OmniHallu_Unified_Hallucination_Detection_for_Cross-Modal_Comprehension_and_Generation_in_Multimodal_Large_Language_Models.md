---
date: "2026-09-13"
paper_id: "arXiv:2609.11244"
title: "OmniHallu: Unified Hallucination Detection for Cross-Modal Comprehension and Generation in Multimodal Large Language Models"
authors: "Jianjiang Yang, Peihang Li, Shanqing Xu, Mengchen Qian, Lu Zhang, Meng Luo"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - MLLM
  - 幻觉检测
  - 基准数据集
  - 多智能体
  - 跨模态
quality_score: "8.0/10"
created: "2026-09-13"
updated: "2026-09-13"
status: analyzed
---

# OmniHallu: Unified Hallucination Detection for Cross-Modal Comprehension and Generation in Multimodal Large Language Models

## 核心信息
- **论文ID**：arXiv:2609.11244
- **作者**：Jianjiang Yang, Peihang Li, Shanqing Xu, Mengchen Qian, Lu Zhang, Meng Luo
- **机构**：曼彻斯特大学、香港大学、华中科技大学、上海教育科学研究院、新加坡国立大学
- **发布时间**：2026-09-10
- **类别**：cs.CL, cs.CV
- **链接**：[arXiv](https://arxiv.org/abs/2609.11244) | [PDF](https://arxiv.org/pdf/2609.11244)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
While Multimodal Large Language Models (MLLMs) have achieved remarkable progress across diverse tasks, they suffer from hallucinations where generated outputs contradict or misrepresent input semantics. Existing research typically addresses hallucination detection within a single modality or task type, limiting generalizability. We introduce OmniHallu, a unified hallucination detection framework spanning both comprehension and generation tasks across image, video, and audio modalities. We contribute OmniHallu-Bench, a 10,000-sample benchmark with claim-level human annotations covering six cross-modal tasks: image-to-text (I2T), video-to-text (V2T), audio-to-text (A2T), text-to-image (T2I), text-to-video (T2V), and text-to-audio (T2A). Our multi-agent architecture decomposes model outputs into atomic claims, verifies them through modality-specific experts, and aggregates evidence via structured reasoning. We further propose a preference-optimized trainable verifier that approximates the multi-agent decision boundary, reducing expert calls by 66% with minimal performance loss.

### 中文翻译
尽管多模态大语言模型（MLLMs）在各类任务上取得了显著进展，但它们仍存在幻觉问题——生成的输出与输入语义相矛盾或曲解。现有研究通常只在单一模态或单一任务类型内处理幻觉检测，限制了泛化性。本文提出 OmniHallu，一个统一的幻觉检测框架，覆盖图像、视频和音频模态下的理解与生成任务。我们贡献了 OmniHallu-Bench，一个包含 10000 个样本、带 claim 级人工标注的基准，覆盖六个跨模态任务：图像到文本（I2T）、视频到文本（V2T）、音频到文本（A2T）、文本到图像（T2I）、文本到视频（T2V）和文本到音频（T2A）。我们的多智能体架构将模型输出分解为原子声明（atomic claims），通过模态特定专家进行验证，并通过结构化推理聚合证据。我们进一步提出一个偏好优化的可训练验证器，逼近多智能体的决策边界，将专家调用减少 66%，而性能损失最小。

### 核心要点提炼
- **研究背景**：MLLM 在视觉、音频、语言任务上取得进展，但幻觉问题成为安全关键应用部署的根本障碍
- **研究动机**：现有幻觉检测方法局限于单一模态或单一任务方向（多为理解），缺乏跨模态统一的检测协议
- **核心方法**：将 decompose–verify–aggregate 范式扩展到六个双向任务，多智能体架构分解原子 claim + 模态特定专家验证 + 结构化推理聚合
- **主要结果**：偏好优化验证器（GRPO 对齐）减少 66% 专家调用且性能损失最小，并揭示模态依赖的性能梯度
- **研究意义**：首次实现跨四模态、六任务、双方向的统一幻觉检测，为跨模态幻觉模式提供细粒度洞察

## 研究背景与动机

### 领域现状
MLLMs 已在视觉、音频与语言任务上取得长足进步，但幻觉（hallucination）——生成与输入语义矛盾或曲解的输出——始终是阻碍其在安全关键场景落地的核心问题。幻觉在各类任务中普遍存在：视觉语言模型可能描述图像中不存在的物体；视频语言模型存在内在/外在幻觉；音频-视频模型可能忽略声学内容；生成任务（T2I/T2V）则常出现组合性 prompt 对齐失败与时间一致性缺失。

### 现有方法的局限性
1. **模态割裂**：图像、视频、音频各自的幻觉基准（POPE、AMBER、FactVC、AHLALM）相互孤立，无法横向比较
2. **任务方向单一**：绝大多数工作只覆盖"理解"（模态→文本），忽略"生成"（文本→模态），而两者共享因感知与推理不足导致的幻觉模式
3. **粒度不一致**：既有基准在响应级、片段级、claim 级的标注粒度参差，缺乏统一的检测协议
4. **缺乏系统化对比**：无法回答"哪些组件可跨模态迁移、哪些必须模态特定"这一关键问题

### 研究动机
作者提出一个统一的 claim 级检测协议，使跨模态、跨任务方向的并列比较成为可能，同时揭示组件迁移性与模态特定验证的必要性边界。

## 研究问题

**核心研究问题**：如何构建一个统一的幻觉检测框架，在跨图像/视频/音频、跨理解/生成的设定下，实现可并列比较的 claim 级幻觉检测，并刻画跨模态幻觉的规律？

形式化定义：设 $\mathcal{T}, \mathcal{I}, \mathcal{V}, \mathcal{A}$ 分别表示文本、图像、视频、音频数据。MLLM 将输入 $x$ 映射为输出 $\hat{y}$。理解任务为 $x\in\{\mathcal{I},\mathcal{V},\mathcal{A}\}\to\hat{y}\in\mathcal{T}$，生成任务为 $x\in\mathcal{T}\to\hat{y}\in\{\mathcal{I},\mathcal{V},\mathcal{A}\}$。若输出 $\hat{y}$ 包含任何未被输入 $x$ 支持或与 $x$ 矛盾的语义声明，则判定为幻觉：$\mathrm{Hallucinate}(\hat{y}|x)=\mathbb{1}[\exists\phi(\hat{y})\notin\mathcal{G}]$，其中 $\mathcal{G}$ 为可从 $x$ 推导的真实语义元素集合。

## 方法概述

### 方法架构
OmniHallu 采用 **decompose–verify–aggregate** 三段式多智能体架构：

1. **原子声明分解（Decompose）**：将 MLLM 输出拆解为不可再分的原子声明（atomic claims）
2. **模态特定专家验证（Verify）**：不同模态调用不同验证工具与专家——视频验证强调时间与因果证据，音频验证依赖声学线索（工具生态尚不成熟）
3. **结构化推理聚合（Aggregate）**：基于推理将证据聚合为最终判定

在此基础上，作者进一步提出 **偏好优化可训练验证器**：用 GRPO 对齐一个紧凑的可训练验证器，逼近多智能体决策边界，从而大幅减少昂贵的专家调用。

### 关键创新
1. **跨模态系统化**：首次将 decompose–verify–aggregate 范式适配到六个双向任务，覆盖四模态
2. **统一幻觉分类法**：定义四类跨模态通用幻觉——Object（不存在实体，35%）、Attribute（属性误述，25%）、Event（事件细节错误，25%）、Relation（时空/因果关系错误，15%）
3. **偏好优化验证器**：以 GRPO 训练的紧凑验证器替代昂贵多智能体，专家调用减少 66%

### 基准设计（OmniHallu-Bench）
- **规模**：10000 样本，理解任务 60%、生成任务 40%；图像/视频/音频按 5:3:2 分层
- **数据来源**：I2T 用 COCO/Nocaps/Flickr30k（InternVL2.5-78B、Qwen2.5-VL-72B、GPT-4.1、Gemini-2.5-Pro 生成）；V2T 用 MSVD/MSRVTT/VATEX；A2T 用 AudioCaps/ClothoV2/AudioSet-Caps；T2I/T2V/T2A 用 T2I-CompBench++/HRS-Bench、T2V-CompBench/FETV、WavText5K/FSD50K/SoundDescs
- **对比**：相对 QAGS、HaluEval、POPE、AMBER、MHaluBench 等，OmniHallu-Bench 是唯一同时覆盖 6 任务、4 模态、并提供 Rationale 的 claim 级检测基准

## 实验结果

- **统一协议有效性**：在统一 claim 级协议下，跨模态幻觉呈现一致的**模态依赖性能梯度**，为幻觉模式提供细粒度刻画
- **验证器蒸馏**：偏好优化验证器减少 66% 专家调用，性能损失最小，证明多智能体决策边界可被紧凑模型逼近
- **组件贡献分析**：通过受控实验刻画各组件（分解/验证/聚合）的贡献、性能波动与失败模式

## 深度分析

### 研究价值
1. **填补跨模态检测空白**：将幻觉检测从单模态"烟囱式"研究统一到可并列比较的框架，是领域的重要系统化贡献
2. **实用的降本路径**：偏好优化验证器把昂贵的多智能体推理蒸馏为单模型，兼顾精度与成本，对落地部署有直接价值
3. **可迁移性洞察**：通过受控分析回答"哪些验证组件可跨模态迁移"，为后续研究提供方法论指导

### 局限性
1. **工具生态不对称**：音频验证工具链尚不成熟，可能影响 A2T/T2A 任务的检测上限
2. **验证器泛化边界**：偏好优化验证器逼近的是当前多智能体的决策边界，其在新模型/新任务上的泛化性仍需验证
3. **生成任务的客观判定**：T2I/T2V/T2A 的"语义 discrepancy"判定本质上更主观，claim 级标注的一致性可能弱于理解任务

### 未来工作
- 扩展音频验证工具生态，弥补跨模态工具不对称
- 探索验证器在更强 MLLM 与更多任务上的泛化
- 将框架迁移到更多模态（如 3D、触觉）与组合式生成任务

## 相关论文对比

| 方法/基准 | 功能 | 粒度 | 模态数 | 是否提供 Rationale |
|-----------|------|------|--------|-------------------|
| MHaluBench | 检测 | 响应/片段/声明 | 2 | ✔ |
| POPE | 检测 | 响应 | 2 | ✗ |
| FactVC | 检测 | 响应 | 2 | ✗ |
| AHLALM | 检测 | 响应 | 2 | ✗ |
| **OmniHallu-Bench** | 检测 | 响应/片段/声明 | 4 | ✔ |

OmniHallu 相较 UNIHD（仅图像-文本）、CrossCheckGPT（系统排序而非 claim 级检测）等，首次在单一框架内统一了四模态、六任务、双方向的 claim 级检测。
