---
date: "2026-11-06"
paper_id: "arXiv:2611.07216"
title: "Compositional Unalignment: Auditing Safety in Large Vision-Language Models"
authors: "Hong kyu Lee, Kai Shu, Li Xiong"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 多模态
  - LVLM-Safety
  - Alignment
  - Un-alignment
  - Activation-Steering
quality_score: "8.5/10"
created: "2026-11-06"
updated: "2026-11-06"
status: analyzed
---

# Compositional Unalignment: Auditing Safety in Large Vision-Language Models

## 核心信息
- **论文ID**：arXiv:2611.07216
- **作者**：Hong kyu Lee, Kai Shu, Li Xiong（Emory University）
- **机构**：Emory University
- **发布时间**：2026-11-04
- **会议/期刊**：arXiv preprint（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2611.07216) | [PDF](https://arxiv.org/pdf/2611.07216v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Large vision-language models (LVLMs) are vulnerable to compositional harm: an image and a prompt that are benign in isolation become harmful through the model's cross-modal reasoning. Recent alignment methods report strong scores on compositional safety benchmarks, but an LLM judge only measures observed behavior and cannot reveal how robustly safety is integrated into the model. We argue that un-alignment provides a complementary audit of alignment robustness...

### 中文翻译
大视觉语言模型（LVLM）易受**组合式危害**（compositional harm）的影响：一张图片和一个提示各自单独来看都是良性的，但通过模型的跨模态推理，二者组合后却变得有害。近期对齐方法在组合安全基准上报告了很高的分数，但 LLM 裁判只衡量观测到的行为，无法揭示安全性被整合进模型的**鲁棒程度**。本文主张"**反对齐**（un-alignment）"是对齐鲁棒性的一种互补审计手段……

### 核心要点提炼
- **研究背景**：LVLM 组合安全（图像+文本跨模态组合产生危害）是当前对齐的关键难点
- **研究动机**：benchmark 分数无法区分"真鲁棒"与"表面安全"，需要一个能探测内在脆弱性的审计工具
- **核心方法**：CMaG——训练无关的反对齐方法，通过激活梯度测量拒绝意图并隔离跨模态交互项
- **主要结果**：在 Qwen2.5-VL-7B / LLaVA-1.5-7B 上降低组合安全分数最多 65 分，98% 输出保持连贯
- **研究意义**：证明"行为层面安全"与"内部机制脆弱"可以并存，为对齐审计提供新视角

## 研究背景与动机

### 领域现状
LVLM 的对齐研究已从纯文本扩展到多模态，出现了大量组合安全基准与对齐方法（多阶段 RL 流水线等）。主流评估依赖 LLM 裁判对模型输出的行为级打分。

### 现有方法的局限性
1. **裁判只测行为**：LLM judge 只能看到输出，看不到模型内部如何整合安全约束
2. **分数不可区分鲁棒性**：论文发现，最简的监督微调（SFT）即可达到与多阶段 RL pipeline 相当的 judge 分数，因此"高分"无法自证鲁棒
3. **识别与拒绝解耦**：九种方向探测方法都能以 AUC=0.99 检测组合危害，但消融这些方向要么不改变拒绝行为、要么破坏模型连贯性——说明"识别到危害"和"执行拒绝"并非同一套内部机制

### 研究动机
需要一个**训练无关、可定位内部机制**的审计方法，用来回答"模型的安全对齐到底有多脆弱"。

## 研究问题

### 核心研究问题
组合安全能否在**行为上看起来很强**、却在**内部机制上仍然脆弱**？如何用最小的介入暴露出这种脆弱性？

## 方法概述

### 核心思想
不通过训练破坏对齐，而是**直接操纵模型内部表征**，找到一条"把拒绝翻转为顺从"的方向，从而证明对齐鲁棒性的缺失。

### 方法框架

![[2611.07216_fig1.png|600]]

> 图1：CMaG 方法概览——通过测量拒绝 vs 顺从的对数概率边际，对其关于中层激活求梯度，并与部分良性样本对对比，隔离出跨模态交互项。

#### 各模块详细说明

**模块1：拒绝意图测量**
- **功能**：量化模型当前"想拒绝"还是"想顺从"的倾向
- **关键指标**：refusal-versus-compliance 的对数概率边际（log-probability margin）
- **作用**：提供一个连续的"安全意图"信号，而非二值输出

**模块2：激活梯度提取**
- **功能**：对中层激活（mid-layer activations）求上述边际的梯度
- **产出**：一个可指导模型内部表征转向的"反对齐方向"
- **特点**：单次反向传播（single backward pass）即可得到方向，无需训练

**模块3：跨模态交互隔离**
- **功能**：与"部分良性样本对"（partially benign pairs）对比，把纯文本项与跨模态交互项分离
- **目的**：定位到"图像×文本组合"这一组合危害的来源，而非笼统的安全方向

### 关键创新
- 提出**训练无关**（training-free）的反对齐审计范式，与训练型攻击互补
- 用**对数概率边际 + 激活梯度**将"拒绝意图"连续化、可定位
- 通过**部分良性对比**显式隔离跨模态交互项，是方法可解释性的关键

## 实验结果

### 实验设置
- **模型**：Qwen2.5-VL-7B、LLaVA-1.5-7B
- **规模**：80 个示例即可得到稳定方向
- **基线**：基于梯度的反对齐方法

### 主要结果
- CMaG 降低组合安全分数**最多 65 分**，同时 **>98% 的输出保持连贯**，优于梯度基线
- 得到的方向**迁移到文本显式危害**（text-explicit harm），且能反对齐经过防御性训练的模型
- 消融实验表明：单纯移除识别方向不改变拒绝行为，证明**识别与拒绝是解耦**的两套机制

### 结果分析
核心结论是"组合安全可以在行为上看似强大、却在内部干预下仍然脆弱"——这直接挑战了仅依赖 benchmark 分数的对齐评估范式。

## 深度分析

### 研究价值评估
- **理论贡献**：提出 un-alignment 作为对齐鲁棒性的**互补审计**，把讨论从"行为对齐"推进到"机制对齐"
- **实际应用**：为红队测试、模型安全审计提供了一种低成本、可解释的工具
- **领域影响**：对齐评估、AI 安全、多模态可解释性

### 局限性分析
1. **仅覆盖两层模型规模**：只在 7B 级别验证，更大模型的机制可能不同
2. **审计≠修复**：揭示了脆弱性，但未给出强化对齐的具体方案
3. **伦理敏感**：论文含不安全输出示例（包括自残相关），需要谨慎使用

### 与相关论文对比
- 相比**训练型攻击**（如 GCG 类）：CMaG 无需训练、更可控、可解释性更强
- 相比**纯行为审计**：直接定位内部机制，能发现行为审计遗漏的脆弱性

## 我的综合评价

### 总体评分
**8.5/10** - 视角新颖、方法干净，为"对齐鲁棒性审计"提供了一个有洞察力的新范式，但实验规模与"审计→修复"闭环仍有空间

### 分项评分
| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | un-alignment 审计范式 + 跨模态交互隔离，视角独特 |
| 技术质量 | 8/10 | 训练无关、可解释，方法设计严谨 |
| 实验充分性 | 7/10 | 两个 7B 模型充分验证，但缺少更大规模 |
| 写作质量 | 9/10 | 逻辑清晰，"识别-拒绝解耦"论证有力 |
| 实用性 | 8/10 | 红队与安全审计可直接借鉴 |

## 相关论文
- 直接相关：LVLM 组合安全基准、多模态红队、activation steering 方法
- 背景相关：多模态对齐、RLHF、表示工程（representation engineering）

> [!warning] 注意事项
> - 论文含不安全模型输出示例（包括自残相关），阅读时注意
> - 该方法是**审计工具**，不应被用于生成有害内容
