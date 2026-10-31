---
date: "2026-10-31"
paper_id: "arXiv:2610.40516"
title: "How I Aligned Your Model: Scaling Up Multilingual Safety Training with Templates"
authors: "Max Neuwinger, Hanna Yukhymenko, Imanol Schlag"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 安全对齐
  - 多语言
  - 红队测试
quality_score: "7.5/10"
created: "2026-10-31"
updated: "2026-10-31"
status: analyzed
---

# How I Aligned Your Model: Scaling Up Multilingual Safety Training with Templates

## 核心信息
- **论文ID**：arXiv:2610.40516
- **作者**：Max Neuwinger, Hanna Yukhymenko, Imanol Schlag
- **机构**：苏黎世联邦理工学院（ETH Zurich）
- **发布时间**：2026-10-29
- **类别**：cs.CL, cs.AI, cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.40516) | [PDF](https://arxiv.org/pdf/2610.40516)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Safety alignment for Large Language Models is heavily focused on English with limited multilingual representation, particularly damaging low-resource language performance. Related open training datasets are often limited in size or do not follow region-localized harmful topics to broaden the scope of alignment beyond cultures. We propose a scalable template-based augmentation pipeline to produce massive multilingual refusal and benign corpora, extracting safe templates from harmful prompts, translating them and then filling in the slots with culturally authentic entities, both harmful and benign. As a result, we release MSafeTemp, a validated corpus spanning 60 languages useful for multilingual safety alignment and evaluation. Using the dataset, we show that English-only evaluation misjudges multilingual safety: for several models, attacks that fail in English succeed once translated, while culturally localized variants expose further failures that translation alone misses. We also confirm that augmenting safety training data through templates allows for higher diversity and quality of alignment, resulting in improved safety performance beyond English. MSafeTemp and the respective pipeline are aimed at facilitating research and development of safer and more responsible models for less-represented cultures.

### 中文翻译
大语言模型的安全对齐高度集中在英语上，多语言表征有限，尤其损害低资源语言的表现。相关开放训练数据集往往规模有限，或未遵循"区域本地化有害话题"来拓宽对齐范围以覆盖不同文化。本文提出一个可扩展的、基于模板的增强流水线，用于生成大规模多语言拒答（refusal）与良性语料：从有害提示中抽取安全模板，翻译后再用文化真实的实体（有害与良性两类）填充槽位。由此发布 MSafeTemp——一个覆盖 60 种语言的经校验语料库，可用于多语言安全对齐与评估。利用该数据集，作者证明仅用英语评估会误判多语言安全性：对多个模型而言，在英语中失败的攻击一旦被翻译就能成功，而文化本地化变体则进一步暴露仅靠翻译无法发现的失败。作者还确认，通过模板增强安全训练数据能带来更高的多样性与对齐质量，从而提升英语之外的安全表现。MSafeTemp 及其流水线旨在促进为较少被代表的文文化开发更安全、更负责任模型的研究与发展。

### 核心要点提炼
1. **问题**：安全对齐过度集中于英语，翻译攻击即可绕过，低资源语言尤其脆弱。
2. **方法**：模板化增强——从有害提示抽安全模板 → 翻译 → 用文化真实实体填槽，规模化生成多语言拒答/良性语料。
3. **贡献**：发布 MSafeTemp（60 语言），证明英语-only 评估误判多语言安全，模板增强能提升对齐的多样性与质量。

## 方法概述

### 方法架构
![[pipeline_page2.jpg|800]]

流水线由三步构成：
1. **模板抽取**：从有害提示中剥离出"安全模板"（保留句式结构，去除具体有害内容）。
2. **翻译与本地化**：将模板翻译到目标语言，并适配目标文化语境。
3. **实体填充**：用文化真实的实体（既包括有害实体，也包括良性实体）填充槽位，生成成对的多语言拒答与良性训练样本。

### 关键创新
1. **文化本地化实体**：不仅翻译，还替换为目标文化特有的实体（如当地极端组织、历史事件、社区特定贬称），弥补"仅翻译"无法覆盖的失败模式。
2. **规模化模板增强**：用模板扩增而非人工标注，解决多语言安全数据稀缺问题。
3. **评估与训练一体化**：MSafeTemp 同时服务于安全对齐训练与安全评估。

## 实验结果

- **数据集**：MSafeTemp，覆盖 60 种语言。
- **关键发现**：英语-only 评估误判多语言安全；多个模型在英语中拒绝、翻译后遵从的攻击案例被系统性揭示；文化本地化变体暴露翻译所遗漏的失败。
- **对齐效果**：模板增强安全训练数据提升对齐的多样性与质量，英语之外的安全表现同步提升。

## 深度分析

### 优势
- 直击"多语言安全对齐"这一长期被忽视的盲区，方法可扩展、无需大规模人工标注。
- 文化本地化的视角超越了简单的机器翻译对抗，具有方法论意义。

### 局限与开放问题
- 模板增强的语义保真度与有害意图覆盖度需要更细的评估（模板可能稀释原始有害语义）。
- 60 语言覆盖广但单语言深度未知，低资源语言的验证标注成本依然存在。
- 与既有红队测试/越狱防御方法（如[[SCARL_Sequence_Confidence-Aligned_Reinforcement_Learning|SCARL]]等后训练对齐）的协同尚未展开。

### 与相关工作的关系
- 相比仅靠翻译的对抗评估，本文强调"文化本地化"维度，揭示翻译-only 的盲区。
- 与低资源语言红队测试工作（如约鲁巴语/尼日利亚洋泾浜语案例研究）形成互补：一个关注数据增强与对齐，一个关注评估揭示失效。
