---
date: "2026-07-07"
paper_id: "arXiv:2607.05196"
title: "Unified Audio Intelligence Without Regressing on Text Intelligence"
authors: "Zhifeng Kong, Sang-gil Lee, Jaehyeon Kim, Boxin Wang, Zihan Liu, Sungwon Kim, Yang Chen, Arushi Goel, Rajarshi Roy, Wenliang Dai, Zhuolin Yang, Yangyi Chen, Dongfu Jiang, Sreyan Ghosh, Tuomas Rintamaki, Andrew Tao, Jonathan Raiman, Mohammad Shoeybi, Bryan Catanzaro, Wei Ping"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 多模态
  - Audio-LLM
  - Speech-Understanding
  - TTS
  - MoE
  - Nemotron
  - Multimodal-Generation
  - Audio-Generation
quality_score: "8.5/10"
created: "2026-07-07"
updated: "2026-07-07"
status: analyzed
---

# Unified Audio Intelligence Without Regressing on Text Intelligence

## 核心信息
- **论文ID**：arXiv:2607.05196
- **作者**：Zhifeng Kong*, Sang-gil Lee*, Jaehyeon Kim*, Boxin Wang 等（NVIDIA, *同等贡献，按名字首字母逆序排列）
- **机构**：NVIDIA
- **发布时间**：2026-07-06
- **会议/期刊**：--
- **链接**：[arXiv](https://arxiv.org/abs/2607.05196) | [PDF](https://arxiv.org/pdf/2607.05196v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Audio intelligence involves understanding, reasoning about, and generating both audio and speech. In this work, we introduce Nemotron-Labs-Audex-30B-A3B (Audex), a unified audio-text LLM built on Nemotron-Cascade-2-30B-A3B, a strong text-only MoE LLM. Audex adopts a simple unified design with a single Transformer decoder: audio inputs are encoded and projected into the text embedding space, while text tokens and quantized audio output tokens are treated uniformly during generation. This architecture enables strong audio-text fusion, seamless multimodal generation, and compatibility with standard LLM training and inference infrastructure. For training, we meticulously curate audio-text datasets comprising 157.4B audio tokens and 320.5B text tokens. We apply multi-stage supervised training on these datasets, followed by text-only Cascade RL and multi-domain on-policy distillation. Audex delivers state-of-the-art audio understanding, speech recognition and translation, text-to-speech, audio generation, and speech-to-speech generation, while preserving very compelling reasoning, alignment, knowledge, long-context, and agentic capabilities of its text-only LLM backbone with marginal or no regression. We release the model checkpoints to facilitate open research.

### 中文翻译
音频智能涉及对音频和语音的理解、推理和生成。本文介绍Nemotron-Labs-Audex-30B-A3B（简称Audex），一个构建在Nemotron-Cascade-2-30B-A3B（强大的纯文本MoE LLM）之上的统一音频-文本LLM。Audex采用简单的统一设计，使用单一Transformer解码器：音频输入被编码并投影到文本嵌入空间，而文本token和量化音频输出token在生成过程中统一处理。这种架构实现了强大的音频-文本融合、无缝多模态生成，并与标准LLM训练和推理基础设施兼容。在训练方面，我们精心策划了包含157.4B音频token和320.5B文本token的音频-文本数据集，对这些数据集进行多阶段监督训练，随后进行纯文本Cascade RL和多域On-Policy蒸馏。Audex在音频理解、语音识别和翻译、文本到语音合成、音频生成和语音到语音生成上均达到SOTA，同时基本保持（甚至无回归地保持）了其纯文本LLM基座的推理、对齐、知识、长上下文和Agent能力。我们开源了模型权重以促进开放研究。

### 核心要点提炼
- **研究背景**：音频智能（理解+生成）是多模态AI的重要方向，但现有方法往往在添加音频能力时损害文本能力
- **研究动机**：如何构建一个统一的音频-文本模型，在获得强大音频能力的同时不退化文本智能？
- **核心方法**：在30B-A3B MoE LLM基座上，通过将音频编码投影到文本空间、文本和量化音频token统一生成的方式构建端到端模型
- **主要结果**：在所有音频任务（理解/ASR/翻译/TTS/生成/语音对话）上达SOTA，同时文本推理、对齐、知识等能力几乎零退化
- **研究意义**：证明了统一的多模态LLM可以在不回归文本能力的情况下获得全频谱音频智能，为多模态基础模型提供了新的设计范式

## 研究背景与动机

### 领域现状
音频AI长期以来被分割成多个独立任务：ASR（语音识别）、TTS（文本转语音）、音频理解、音乐生成等。近期虽有GPT-4o、Gemini等尝试统一，但大多数模型在添加多模态能力时都会在文本智能方面出现退化（regression），尤其是在推理、长上下文和Agent能力上。

### 现有方法的局限性
1. **模态冲突**：多模态训练可能稀释文本能力，导致模型在数学推理、代码生成等纯文本任务上退化
2. **架构复杂性**：许多方法需要专门的音频编码器-解码器，与标准LLM基础设施不兼容
3. **数据质量**：音频-文本配对数据的质量和规模远不如纯文本数据

### 研究动机
构建一个"音频智能不回归文本智能"的统一模型——在获得全频谱音频能力的同时，保持基座LLM的所有文本能力。

## 研究问题

如何在单一Transformer解码器架构中统一音频理解、推理和生成，同时保持纯文本LLM基座的全部能力不退化？

## 方法概述

### 核心思想
将音频输入编码并投影到文本嵌入空间，将量化音频输出token与文本token统一处理——从LLM的角度看，音频只是"另一种token"。这与许多使用独立音频模块的方法形成鲜明对比。

### 方法框架

**架构设计**：
- **基座模型**：Nemotron-Cascade-2-30B-A3B，30B参数MoE LLM，A3B激活参数
- **音频编码器**：将音频输入编码并投影到文本嵌入空间
- **统一解码**：单一Transformer解码器同时处理文本token和量化音频输出token
- **训练基础设施**：与标准LLM训练/推理完全兼容

**训练策略**：
1. **数据策展**：157.4B音频token + 320.5B文本token的精心策划数据集
2. **多阶段监督训练**：在音频-文本数据上分阶段进行SFT
3. **Cascade RL**：纯文本RL训练以保持推理和对齐能力
4. **多域On-Policy蒸馏**：将RL阶段的能力蒸馏回音频模型

### 关键设计选择
- **不使用独立音频模块**：避免架构复杂性，确保与标准LLM基础设施兼容
- **MoE架构**：30B参数但仅3B激活，在保持大模型容量的同时控制推理成本
- **Cascade RL + 蒸馏**：先提升文本能力，再蒸馏回多模态模型——这是防止文本退化的关键策略

## 实验结果

### 覆盖的能力谱系

| 能力类型 | 具体任务 |
|----------|----------|
| 音频理解 | 环境声音分类、音频问答、音频推理 |
| 语音识别 | ASR（多语言） |
| 语音翻译 | Speech-to-Text Translation |
| TTS | 文本到语音合成 |
| 音频生成 | 音乐/声音效果生成 |
| 语音到语音 | Speech-to-Speech Generation |

### 主要结果
- 所有音频任务上达到SOTA水平
- **文本能力零退化**：推理、对齐、知识、长上下文、Agent能力几乎无退化
- 这是该领域的一个关键里程碑——此前很少有模型在添加多模态能力时能完全保持文本性能
- 模型权重已开源

## 深度分析

### 研究价值评估

| 维度 | 评分 | 理由 |
|------|------|------|
| 创新性 | 8/10 | "不回归文本智能"的明确目标是重要贡献；Cascade RL + 蒸馏策略很有启发性 |
| 技术质量 | 9/10 | 简洁的架构设计、精心策展的数据、多阶段训练策略——工程实现扎实 |
| 实验充分性 | 8/10 | 覆盖全频谱音频任务，但缺少与GPT-4o、Gemini等商业模型的详细对比 |
| 写作质量 | 8/10 | 目标清晰、论证有力 |
| 实用性 | 9/10 | 模型开源、与标准LLM基础设施兼容，对产业部署友好 |

### 突出亮点
1. **"不回归"是核心贡献**：明确将文本能力保持作为评估维度，而非只顾多模态能力的提升
2. **MoE架构的巧妙运用**：30B参数/3B激活，在控制推理成本的同时保持大模型容量
3. **Cascade RL策略**：先纯文本RL再蒸馏回音频模型——简单但有效的文本能力保持策略

### 优势
- 统一架构降低部署复杂性
- 开源促进社区研究
- MoE架构在成本-性能权衡上表现优秀
- 兼容标准LLM训练/推理基础设施

### 局限性
- 30B参数/A3B激活意味着仍有较高的部署门槛
- 音频生成质量（TTS/音乐）与专门模型的对比较为有限
- 实时音频处理延迟未详细讨论

> [!tip] 关键启示
> 在多模态LLM中"不回归"文本能力的策略：先是纯文本能力的RL提升（Cascade RL），再通过蒸馏将能力回传给多模态模型——这种"先升后传"的策略值得在其他多模态场景中借鉴。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐——对于关注多模态LLM和音频AI的研究者，本文提供了一种简洁有效的统一架构设计，且模型已开源。
