---
date: "2026-11-27"
paper_id: "arXiv:2611.34928"
title: "Diverse Alternative Reasoning Traces for Hard Math Problems"
authors: "Lukas Aichberger, Adam Golinski, Arno Blaas, Arwen Bradley, Jacob Springer, Mehrdad Farajtabar, Michael Kirchhof, Sinead Williamson"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 数学推理
  - 后训练
  - 推理轨迹
quality_score: "8.0/10"
related_papers: []
created: "2026-11-27"
updated: "2026-11-27"
status: analyzed
---

# Diverse Alternative Reasoning Traces for Hard Math Problems

## 核心信息
- **论文ID**：arXiv:2611.34928
- **作者**：Lukas Aichberger 等 8 人
- **机构**：--
- **发布时间**：2026-11-25
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.34928) | [PDF](https://arxiv.org/pdf/2611.34928)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Post-training LLMs for mathematical reasoning often relies on accepted traces whose final answers pass a verifier. For hard math problems, such traces can be rare under independent Monte Carlo sampling. We propose DART, a trace-mining method that starts from a rejected trace, decomposes it into reasoning steps, ranks them by position-weighted uncertainty, generates deliberately distinct alternative candidates for highly ranked steps using reference-answer guidance and a meta-thought, then completes the traces.

### 中文翻译
面向数学推理的大语言模型后训练，通常依赖"最终答案通过验证器"的被接受轨迹。但对难题而言，在独立蒙特卡洛采样下这类轨迹极为稀少。为此我们提出 DART——一种轨迹挖掘方法：从一个被拒绝的轨迹出发，将其分解为推理步骤，按"位置加权不确定性"对步骤排序，借助参考答案引导与"元思考"为高排名步骤生成刻意不同的备选候选，再补全轨迹。

### 核心要点提炼
- **研究背景**：数学后训练靠"答对"的轨迹，难题下答对轨迹在 MC 采样中十分稀少。
- **研究动机**：被拒绝的轨迹里其实蕴含有用推理步骤，只是"差一步"才失败。
- **核心方法**：DART 从被拒轨迹中按不确定性定位易错步骤，定向生成多样化备选并补全。
- **主要结果**：比 MC 采样多恢复 37% 难题，单条被接受轨迹成本降 30%。
- **研究意义**：把"失败轨迹"变废为宝，显著扩大难题覆盖与后训练数据。

## 研究背景与动机

### 领域现状
LLM 数学能力提升高度依赖后训练（SFT/RL），而训练数据的核心是"推理轨迹"。传统做法是采样大量轨迹，只保留最终答案正确的轨迹。难题场景下，正确答案的轨迹占比极低，采样效率成为瓶颈。

### 现有方法的局限性
- MC 采样对难题"命中率"低，大量计算被浪费在最终失败的轨迹上。
- 简单丢弃失败轨迹，忽略了其中局部正确的推理步骤。
- 缺乏对"轨迹在哪一步出错"的结构化诊断能力。

### 研究动机
失败轨迹往往只在一两个关键步骤出错。若能定位这些易错步骤并有针对性地生成替代方案，就能把失败轨迹"抢救"成有效训练数据。

## 研究问题

核心研究问题：**如何从大量被拒绝的推理轨迹中，高效挖掘出可用于后训练的高质量多样化轨迹，从而扩大难题覆盖？**

## 方法概述

### 核心思想
把"轨迹挖掘"建模为"定位易错步骤 + 定向多样化补全"，而非盲目重新采样整条轨迹。

### 方法框架

![[fig_01_dart_overview_page1.png|800]]

> 图1：DART 方法总览。从被拒轨迹出发，分解步骤 → 按位置加权不确定性排序 → 对高排名步骤生成多样化备选 → 补全轨迹。

#### 关键流程
1. **轨迹分解**：将被拒绝的轨迹切分为若干推理步骤。
2. **不确定性排序**：用"位置加权不确定性"给每步打分，找出最可能出错的步骤。
3. **多样化候选生成**：对高排名步骤，结合参考答案引导与"元思考"（meta-thought），生成刻意不同的备选。
4. **轨迹补全**：在替换候选后补全后续推理，得到新的完整轨迹。

### 关键创新
- **失败轨迹再利用**：不丢弃被拒轨迹，而是从中定位并修复关键步骤。
- **位置加权不确定性**：结构化诊断"哪一步最可能错"。
- **刻意多样化**：通过参考答案引导+元思考生成"不同而非相似"的备选，避免同质化。

## 实验结果

### 主要结果
- 在 greedy-unsolved 的 DeepMath-103K 难题上，DART 在**相同生成轨迹数**下比 MC 采样**多恢复 37% 的问题**。
- 每个被恢复问题的生成成本几乎相同，而**每条被接受轨迹的成本降低 30%**。
- 用 DART 挖掘的轨迹后训练 Llama-3.1-8B-Instruct，在 MATH-500 与 AIME 的 6 项评估中 **5 项取得最高点估计**。

![[fig_10c_posttraining_aime_2024_2025.png|800]]

> 图2：后训练结果，展示 DART 轨迹训练的模型在 AIME 2024/2025 上的表现。

## 深度分析

### 研究价值
- **理论贡献**：提出"失败轨迹结构化解剖 → 定向修复"的轨迹挖掘范式，区别于盲目重采样。
- **实际价值**：直接提升数学后训练的数据效率，降低采样成本，普适性强。

### 局限性
- 依赖**参考答案**做引导，无答案或答案不可靠的场景受限。
- 不确定性排序与元思考机制带来额外推理开销。
- 主要验证在数学域，向其他推理域的迁移待考察。

### 适用场景
- 有验证器/参考答案的数学、代码等可验证推理任务。
- 难题占比高、采样成本敏感的后训练数据构建。

## 我的综合评价

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 失败轨迹再利用+结构化诊断，思路清晰 |
| 技术质量 | 8/10 | 方法完备，消融充分 |
| 实验充分性 | 8/10 | 多基准+成本分析，对比扎实 |
| 写作质量 | 8/10 | 结构清晰，数据翔实 |
| 实用性 | 8/10 | 对后训练数据工程有直接价值 |

> [!tip] 关键启示
> 失败轨迹不是废料——只要知道"错在哪一步"，就能定向修复，把难题覆盖与数据效率同时提升。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐。方法实用、结果扎实，是做 LLM 数学推理后训练者的必读。
