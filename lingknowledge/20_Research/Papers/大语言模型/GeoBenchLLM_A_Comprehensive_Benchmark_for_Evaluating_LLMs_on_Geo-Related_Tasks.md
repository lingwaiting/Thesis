---
date: "2026-08-10"
paper_id: "arXiv:2608.07411"
title: "GeoBenchLLM: A Comprehensive Benchmark for Evaluating LLMs on Geo-Related Tasks"
authors: "Rodrigo Ferreira Rodrigues, Karim Radouane, Jose G Moreno, Lynda Tamine"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Benchmark
  - 地理空间推理
  - LLM-Evaluation
  - Geo-Spatial
  - 地理AI
quality_score: "7.5/10"
created: "2026-08-10"
updated: "2026-08-10"
status: analyzed
---

# GeoBenchLLM: A Comprehensive Benchmark for Evaluating LLMs on Geo-Related Tasks

## 核心信息
- **论文ID**：arXiv:2608.07411
- **作者**：Rodrigo Ferreira Rodrigues, Karim Radouane, Jose G Moreno, Lynda Tamine
- **机构**：University of Toulouse, France
- **发布时间**：2026-08-07
- **会议/期刊**：--（ACM 格式，可能在投）
- **链接**：[arXiv](http://arxiv.org/abs/2608.07411v1) | [PDF](https://arxiv.org/pdf/2608.07411v1)
- **代码**：https://github.com/Rfr2003/GeoBenchLLM

## 摘要翻译

### 英文摘要
In the context of geodata, existing Large Language Models have often been studied in a homogeneous setting, which has considerably limited insights into their generalization capabilities. In this paper, we present GeoBenchLLM, a comprehensive benchmark for probing LLMs on geo-related tasks. We leverage a careful selection of twelve publicly available datasets from diverse geo-related tasks and domains, and evaluate a set of LLMs on geo-spatial and temporal understanding using our benchmark. Our results show that reasoning and size have a strong impact on overall performance.

### 中文翻译
在地理数据领域，现有的大语言模型研究通常局限于单一设置，这严重限制了对其泛化能力的理解。本文提出了 GeoBenchLLM，一个用于探测大语言模型在地理相关任务中能力的综合基准。我们从多样化的地理任务和领域中精心选择了 12 个公开数据集，并利用该基准评估了一系列 LLM 在地理空间和时间理解方面的表现。结果表明，推理能力和模型规模对整体性能有显著影响。

### 核心要点提炼
- **研究背景**：现有地理 LLM 基准规模小、任务覆盖不全面、问题格式受限
- **研究动机**：缺乏全面评估 LLM 地理空间能力的综合基准
- **核心方法**：整合 12 个公开数据集，划分为 17 个子数据集，覆盖 8 个任务和 3 个认知层级
- **主要结果**：GPT-OSS 120B 表现最佳；思考（thinking）模式能让小模型缩小与大模型的差距
- **研究意义**：为 LLM 地理理解评估提供首个世界级、多格式、大規模的综合基准

## 研究背景与动机

### 领域现状
地理相关任务指涉及地理实体或概念的问题，通常需要空间操作来解决，范围从事实性问题（"巴黎在图卢兹北边吗？"）到路径规划问题（"给我一条从巴黎到图卢兹的路线"）。由于几何不确定性以及语言的歧义性，这些任务对问答系统极具挑战性。

LLM 在处理复杂跨领域任务方面展现出强大能力。在地理领域，已有多个基准测试被提出。

### 现有方法的局限性
现有基准各有不足：
- **GeoBenchmark**：缺少应用认知层级，仅覆盖知识和推理
- **STBench**：过分侧重空间推理，缺少坐标预测、回归和复杂场景问答
- **Xu et al.**：仅有 900 题，规模太小；评估的是工具使用而非内在知识
- **CityEval**：限制在城市环境中
- 多数基准仅支持 Yes/No 和 MCQ 格式，限制了模型的自由表达能力

### 研究动机
需要一个覆盖三个认知层级、世界级规模、支持多种问题格式（生成式、回归、Yes/No、MCQ）的综合基准来全面评估 LLM 的地理空间能力。

## 研究问题

### 核心研究问题
如何在三个认知层级（Knowledge、Reasoning、Application）上全面、公平地评估大语言模型的地理空间和时间理解能力？

## 方法概述

### 核心思想
GeoBenchLLM 是一个高度可访问的综合基准，整合了 12 个公开数据集，进一步划分为 17 个纯文本子数据集，覆盖 8 种不同任务，并按照三个认知层级（知识、推理、应用）进行组织。

### 方法框架

#### 整体架构

![[2608.07411_fig1.png|800]]

> 图1：GeoBenchLLM 基准概览——12 个数据集按三个认知层级组织，覆盖 8 类地理相关任务。

#### 基准构成

**三个认知层级**：

1. **Knowledge（知识层）**
   - Coordinates Prediction（坐标预测）：GeoQuestions1089_coord
   - Yes/No Questions（是非问答）：GeoQuestions1089_YN
   - Regression（回归）：GeoQuestions1089_regress., GeoQuery_regress.
   - Place Prediction（地点预测）：GeoQuestions1089_place, GeoQuery_place, MS-Marco_place

2. **Reasoning（推理层）**
   - Complex Scenario QA（复杂场景问答）：GeoSQA, GKMC
   - Spatial Reasoning（空间推理）：SpatialEvalLLM, SpartUN, StepGame

3. **Application（应用层）**
   - POI Recommendation（POI 推荐）：TourismQA, NY-POI
   - Pathfinding（路径规划）：GridRoute, PPNL_single, PPNL_multi

**数据统计**：总计 421,041 题（训练集 203,014 + 验证集 26,220 + 测试集 191,807）。

**评估指标**（按任务类型设计）：
- 坐标预测：Coordinates Accuracy (C Acc)
- 是非判断：Accuracy
- 回归：Median Error (↓)
- 地点预测：Median Rank (↓)、BLEU-1
- 空间推理：Accuracy
- POI 推荐：BLEU-1、Accuracy
- 路径规划：Optimal Ratio (O Rat)

## 实验结果

### 评估模型
- **Qwen3 系列**：0.6B, 1.7B, 8B（各有 w/o thinking 和 with thinking 两种模式）
- **GPT-OSS 系列**：20B, 120B（有限思考能力）

### 主要结果

| 认知层级 | 最佳模型 | 最佳分数 |
|----------|---------|---------|
| Knowledge | GPT-OSS 120B | 坐标预测 C Acc: 0.72 |
| Knowledge | GPT-OSS 20B | Yes/No Acc: 0.77 |
| Reasoning | Qwen3 8B (thinking) | GeoSQA: 0.63; GKMC: 0.82 |
| Reasoning | GPT-OSS 120B | SpartUN: 0.86 |
| Application | Qwen3 8B (thinking) | GridRoute O Rat: 0.81; PPNL: 0.92 |

### 关键发现
1. **Total wins**：GPT-OSS 120B 赢得 6 个子数据集的最佳，Qwen3 8B (thinking) 赢得 5 个，GPT-OSS 20B 赢得 3 个
2. **Total runner-up**：GPT-OSS 120B 在 9 个子数据集上排名第二
3. **Thinking（思考/推理）的影响**：
   - Qwen3 0.6B + thinking 从 0 wins → 2 wins，证明了思考的价值
   - 对 Knowledge 层级的任务提升不明显，对 Reasoning 和 Application 层级提升显著
4. **SpatialEvalLLM** 对所有模型都极具挑战（最高 Acc 仅 0.37）
5. **PPNL_multi**（多点路径规划）对非思考模型几乎不可能（Acc ≤ 0.04），但 Qwen3 8B thinking 达到 0.62

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：首个覆盖三个认知层级的综合地理 LLM 基准
  - 创新点：将任务按认知复杂度分层（Knowledge → Reasoning → Application）
  - 学术价值：为地理 AI 研究提供了标准化的评估框架
  - 影响范围：LLM 评估、地理信息系统、空间推理

- **贡献2**：引入针对不同任务类型的定制化评估指标
  - 创新点：不同于简单地用 Accuracy 评估所有任务
  - 对路径规划引入 Optimal Ratio、Compliance Ratio 等细粒度指标

#### 实际应用价值
- **LLM 选型参考**：帮助开发者了解不同规模和类型 LLM 在地理任务上的优劣势
- **地理 AI 系统设计**：为构建地理问答系统提供性能基准
- **思维链研究**：提供关于 thinking/reasoning 模式对空间推理影响的实验证据

#### 领域影响
- **短期**：成为地理 LLM 评估的标准基准
- **中期**：推动 LLM 在 GIS、导航、城市规划等领域的应用
- **长期**：启发空间智能与语言智能融合的研究方向

### 方法优势详解

1. **综合性强**：8 个任务 × 3 个认知层级，远超现有基准
2. **规模大**：421,041 题，训练/验证/测试集划分清晰
3. **格式多样**：生成式、回归、Yes/No、MCQ 全覆盖
4. **开箱即用**：完全纯文本（不需要外部工具或 API），评估的是 LLM 内在知识

### 局限性分析

1. **模型范围有限**
   - 描述：仅评估了 Qwen 和 GPT-OSS 两个系列
   - 原因：实验资源限制
   - 影响：对其他流行 LLM（如 Llama、DeepSeek 等）的表现未知
   - 可能的解决方案：社区贡献更多模型的结果

2. **纯文本的限制**
   - 描述：基准仅为纯文本，不涉及地图/图像理解
   - 原因：为了标准化和可复现性
   - 影响：无法评估多模态地理理解能力
   - 可能的解决方案：未来扩展为多模态基准

3. **数据集偏差**
   - 描述：数据集以英文和西方地理为主
   - 原因：公开数据集的语言分布不均衡
   - 影响：可能低估 LLM 在非西方地理上的表现
   - 可能的解决方案：增加其他语言和地区的数据

4. **SpatialEvalLLM 难度过大**
   - 描述：所有模型在此子数据集上表现都很差（最高 0.37）
   - 原因：合成数据可能超出当前 LLM 的空间推理能力
   - 影响：该子基准的区分度有限

## 技术路线定位

### 所属技术路线
本文属于 **LLM 评估基准构建** 路线，专注于地理空间领域。核心特点是：
- 从认知科学视角（Bloom 分类学的变体）进行任务分层
- 强调"内在知识"评估而非工具增强评估
- 重视评估指标的多样性和针对性

### 技术路线发展历程
```
GeoBenchmark (2024) → STBench (2024) → CityEval (2024) → Xu et al. (2025) → GeoBenchLLM (2026)
     ↑                    ↑                ↑                 ↑                   ↑
   39K题/2层级          83K题/MCQ       955K题/城市       900题/工具增强      421K题/世界/3层级
```

## 我的综合评价

### 价值评分

#### 总体评分
**7.5/10** — 一项扎实的基准构建工作，填补了地理 LLM 评估的重要空白，但创新性主要体现在工程层面。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 6/10 | 数据集整合和认知分层有贡献，但方法论本身较为常规 |
| 技术质量 | 8/10 | 数据集选择、预处理、评估指标设计严谨，代码开源 |
| 实验充分性 | 7/10 | 模型覆盖有限，但思维模式的消融分析有价值 |
| 写作质量 | 8/10 | 结构清晰、对比充分、图表详尽 |
| 实用性 | 8/10 | 对地理 AI 研究社区有直接实用价值 |

### 重点关注

- Qwen3 8B thinking 在多个任务上超越 GPT-OSS 120B——思考/推理的威力
- Thinking 模式对 Knowledge 级任务帮助不大，对 Application 级任务帮助巨大
- 小模型 + 思维链的性价比可能优于大模型直推

## 相关论文

### 直接相关
- [[Geo-Spatial_Concept_Probing_of_Large_Language_Models_Abstraction,_Compositionality,_and_Grounding|Geo-Spatial Concept Probing]] — 同一团队（Karim Radouane, Jose G Moreno, Lynda Tamine），从另一个角度（概念探测）研究 LLM 地理能力

### 背景相关
- GeoBenchmark
- STBench
- CityEval / CityGPT

> [!tip] 关键启示
> LLM 的地理空间能力评估需要从"知道什么"（Knowledge）、"如何推理"（Reasoning）到"如何应用"（Application）三个层级系统考量；模型规模和推理能力（thinking）对空间推理尤其关键——小模型思考胜过不思考，但大模型的先天优势仍然显著。

> [!warning] 注意事项
> - 基准是纯文本的，不评估多模态地理理解
> - 实验模型仅限于 Qwen 和 GPT-OSS 系列
> - SpatialEvalLLM 子数据集可能对当前 LLM 过难

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！如果你研究 LLM 评估、地理 AI 或空间推理，这是目前最全面的地理 LLM 基准。
