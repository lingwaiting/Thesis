---
date: "2026-10-15"
paper_id: "arXiv:2610.18829"
title: "OmniCompass: A Holistic Benchmark for Omni-Modal Video Grounding and Understanding"
authors: "Xuyang Liu, Shihao Wang, Xinyu Wei, Minghan Li, Zeqing Wang, Zuhao Yang, Zichen Wen, Lei Zhang"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - 视频理解
  - 视频基准
  - 音视频理解
  - 时空定位
  - MLLM
quality_score: "8.5/10"
related_papers: []
created: "2026-10-15"
updated: "2026-10-15"
status: analyzed
---

# OmniCompass: A Holistic Benchmark for Omni-Modal Video Grounding and Understanding

## 核心信息
- **论文ID**：arXiv:2610.18829
- **作者**：Xuyang Liu、Shihao Wang、Xinyu Wei、Minghan Li、Zeqing Wang、Zuhao Yang、Zichen Wen、Lei Zhang
- **机构**：香港理工大学（PolyU）、OPPO 研究院、科罗拉多矿业学院、南洋理工大学、上海交通大学
- **发布时间**：2026-10-13
- **会议/期刊**：预印本（cs.CV / cs.CL / cs.MM）
- **链接**：[arXiv](https://arxiv.org/abs/2610.18829) | [PDF](https://arxiv.org/pdf/2610.18829)
- **引用**：--

## 摘要翻译

### 英文摘要
With the rapid development of multimodal large language models (MLLMs), evaluating their video understanding capabilities has become increasingly important. Existing video understanding benchmarks cover only part of the evaluation dimensions, limiting systematic diagnosis of omni-modal video understanding. We introduce OmniCompass, a holistic benchmark for omni-modal video grounding and understanding. OmniCompass defines two evaluation tracks: OmniCompass-VL assesses vision-language models on tasks requiring visual evidence, while OmniCompass-AV assesses omni-modal models on tasks requiring joint audio-visual evidence. Each track includes three complementary tasks: temporal grounding (TG), spatial grounding (SG), and question answering (QA). ... Evaluations of 50 MLLMs show that open-source models still lag behind proprietary counterparts, particularly in audio-visual understanding. Precise temporal and spatial grounding remains challenging even for leading models.

### 中文翻译
随着多模态大语言模型（MLLM）的快速发展，评估其视频理解能力变得愈发重要。现有视频理解基准只覆盖了评估维度的一部分，限制了对全模态（omni-modal）视频理解的系统性诊断。作者提出 **OmniCompass**，一个面向全模态视频定位与理解的全面基准。它定义两条评估轨道：**OmniCompass-VL** 评估依赖视觉证据的视觉-语言模型，**OmniCompass-AV** 评估依赖联合音视频证据的全模态模型。每条轨道包含三个互补任务：**时序定位（TG）**、**空间定位（SG）** 与**问答（QA）**，分别回答「目标何时出现」「目标在哪」「发生了什么」。对 50 个 MLLM 的评估表明，开源模型仍落后于闭源模型，尤其在音视频理解方面；即便是顶尖模型，精确的时序与空间定位仍具挑战。

### 核心要点提炼
- **研究背景**：MLLM 视频理解能力飞速发展，但缺乏覆盖全维度的系统性基准。
- **研究动机**：现有基准只测部分维度（视觉/音频、时序/空间、短/长视频），无法系统诊断。
- **核心方法**：双轨道（VL/AV）× 三任务（TG/SG/QA）× 双目标设定（text-only / text+image）的全面基准。
- **主要结果**：50 个模型评估显示开源落后闭源，精确时空定位仍是瓶颈。

## 研究背景与动机

### 领域现状
视频理解基准发展多年（Charades-STA、TVQA、VidSTG、NExT-QA 等），但普遍存在三类局限：只测视觉而忽略音频、只覆盖单一时长、任务类型单一（或只有 QA、或只有定位）。随着音视频统一 MLLM 的兴起，需要一个「全模态」的评估框架。

### 现有方法的局限性
- **证据维度不全**：多数基准只测视觉证据，忽略「需要音频才能回答」的样本。
- **目标指定方式单一**：只用纯文本描述目标，缺乏「文本+参考图像」的配对设定。
- **时长覆盖不均**：短/中/长视频覆盖不均衡。

### 研究动机
用一个统一的、覆盖全维度的基准来系统诊断 MLLM 的视频理解能力，揭示模型在「精确时序定位」「空间定位」「音视频联合推理」上的真实差距。

## 研究问题
- 如何构建覆盖「视觉 + 音视频」双证据、「时序 + 空间 + 问答」三任务、「短 + 中 + 长」三时长的视频理解基准？
- 现有 50 个 MLLM 在全模态视频理解上的真实水平如何？开源与闭源差距多大？

## 方法概述

### 核心思想
用「双轨道 × 三任务 × 双目标设定」的笛卡尔积结构，构建一个可系统诊断的全模态视频理解基准，并保证所有实例经过多轮人工质量校验。

### 方法框架

#### 整体架构
1. **双轨道**：OmniCompass-VL（纯视觉证据）、OmniCompass-AV（联合音视频证据）。
2. **三任务**：时序定位（TG）、空间定位（SG）、问答（QA）。
3. **双目标设定**：每个实例都用「text-only」和「text+image」两种方式指定同一目标，用于检验模型对目标指定的鲁棒性。

#### 数据规模
- OmniCompass-VL：820 视频 / 2,460 实例，覆盖多领域与短/中/长三档时长。
- OmniCompass-AV：240 视频 / 720 实例（叙事与音频丰富的子集）。
- 全部实例人工标注 + 多轮质检。

![[benchmark_overview_page1.png|600]]

> 图：OmniCompass 基准的整体结构（双轨道、三任务、双目标设定）

## 实验结果

### 主要结果
- **整体水平**：Gemini-3.8-Flash（42.8%）与 Kimi-K3（39.6%）分别在闭源、开源模型中得分最高，但绝对分数仍低，说明全模态视频理解远未饱和。
- **开源 vs 闭源**：开源模型全面落后，音视频理解（AV）差距尤其明显。
- **定位瓶颈**：即便领先模型，精确的时序与空间定位仍困难，且模型在同一目标的两种指定方式（text-only vs text+image）下表现不一致。

![[omnicompass_vl_index_page1.png|600]]

> 图：OmniCompass-VL 上各模型的整体表现排名

## 深度分析

### 亮点
1. **全维度覆盖**：首次把「视觉/音视频 × 时序/空间/问答 × 短中长时长 × 双目标设定」系统整合进单一基准。
2. **诊断性强**：配对的目标设定（text-only vs text+image）能专门暴露模型对目标描述的敏感性。
3. **规模与质量**：50 个 MLLM 的规模化评测 + 人工多轮质检。

### 局限
- 绝对分数偏低（最高 42.8%），可能存在较大难度空间，但也可能包含主观标注噪声。
- 基准本身不提供方法改进，偏「评测」而非「解决方案」。

### 对研究方向的启示
音视频联合理解与精确时空定位是 MLLM 下一阶段的明确短板，可作为一个稳定的评测锚点。
