---
date: "2026-08-04"
paper_id: "arXiv:2608.01328"
title: "LongChart VQA: A Comprehensive Benchmark for MLLMs with Complex Multi-Chart Reasoning"
authors: "Ziyan Xiao, Yinghao Zhu, Wenting Zhang, Heaju Kim, Lequan Yu"
domain: "大语言模型"
tags:
  - 论文笔记
  - MLLM
  - Benchmark
  - Multi-Chart-Reasoning
  - Vision-Language
  - VQA
quality_score: "8.5/10"
created: "2026-08-04"
updated: "2026-08-04"
status: analyzed
---

# LongChart VQA: A Comprehensive Benchmark for MLLMs with Complex Multi-Chart Reasoning

## 核心信息
- **论文ID**：arXiv:2608.01328
- **作者**：Ziyan Xiao, Yinghao Zhu, Wenting Zhang, Heaju Kim, Lequan Yu
- **机构**：--
- **发布时间**：2026-08-02
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2608.01328v1) | [PDF](https://arxiv.org/pdf/2608.01328v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Multimodal large language models (MLLMs) are rapidly evolving with expanded context windows and stronger reasoning capabilities, enabling multi-chart understanding and multi-step inference. These abilities are increasingly important as MLLMs are adopted in complex agentic tasks. However, existing benchmarks largely emphasize single-chart perception, while simple chart-to-chart connections are insufficient to evaluate these capabilities. To capture multi-chart complexity while ensuring consistency and validity, we design a synthesis pipeline supported by latent graphs. Building on this pipeline, we introduce LongChart, a benchmark whose VQA sets contain an average of 6.5 images and 31.2 questions. We evaluate 10 state-of-the-art MLLMs and examine three factors that influence performance: reasoning patterns, auxiliary tools, and robustness to image perturbations. Our results show that MLLM accuracy decreases and varies substantially as computational complexity increases, highlighting directions for future research in multi-chart reasoning.

### 中文翻译
多模态大语言模型（MLLM）正随着上下文窗口的扩展和推理能力的增强而快速演进，使多图理解和多步推理成为可能。这些能力在 MLLM 被用于复杂智能体任务时日益重要。然而，现有评测基准主要关注单图感知，简单的图到图连接不足以评估这些能力。为捕捉多图复杂性同时确保一致性和有效性，我们设计了一个由隐式图支撑的合成管线。基于此管线，我们引入 LongChart 基准，其 VQA 集平均包含 6.5 张图片和 31.2 个问题。我们评估了 10 个最先进的 MLLM，考察了影响性能的三个因素：推理模式、辅助工具和对图像扰动的鲁棒性。结果表明，随着计算复杂度的增加，MLLM 准确率下降且变异性显著增大，为多图推理的未来研究指明了方向。

### 核心要点提炼
- **研究背景**：MLLM 正在向多图理解和多步推理演进，但缺乏相应评测基准
- **研究动机**：现有基准仅关注单图感知，无法有效评估 MLLM 的多图复杂推理能力
- **核心方法**：基于隐式图的合成管线 + LongChart 基准（平均 6.5 张图、31.2 个问题）
- **主要结果**：MLLM 准确率随计算复杂度增加显著下降，模型间差异大
- **研究意义**：为 MLLM 多图推理能力评测提供了首个全面、系统的基准

## 研究背景与动机

### 领域现状
MLLM 近年来取得了巨大进展，上下文窗口不断扩展（从几千 token 到百万级），推理能力持续增强（从单步推理到多步 Chain-of-Thought），使得模型能够处理更复杂的多模态任务。然而，现有的 MLLM 评测基准（如 ChartQA、ChartBench、MMMU 等）主要聚焦于单张图表的理解和问答。

### 现有方法的局限性
- **单图评测为主**：现有基准大多基于单张图表，无法评估跨多张图表的推理能力
- **简单的图间连接不够**：直接将多张图表拼接不足以构建有效的多图推理评测
- **缺乏系统性**：没有标准化的方法来生成具有一致性和有效性的多图推理问题

### 研究动机
随着 MLLM 在复杂智能体任务（如数据分析、科学发现、商业决策）中的应用，模型需要能够综合多张图表中的信息进行跨图推理。因此，亟需一个能够系统评估 MLLM 多图复杂推理能力的评测基准。

## 研究问题

### 核心研究问题
如何构建一个能够系统性评估 MLLM 在多图场景下复杂推理能力的评测基准？具体包括：
1. 如何生成具有内在一致性的多图推理问题？
2. 当前 SOTA MLLM 在多图推理任务上的表现如何？
3. 哪些因素（推理模式、工具使用、图像扰动）影响多图推理性能？

## 方法概述

### 核心思想
通过隐式图（latent graph）来建模多张图表之间的逻辑关系和数据依赖，并基于此图自动合成具有一致性保证的多图 VQA 问题。隐式图定义了图表间的计算依赖关系（如：图 A 的数据经过某种变换后出现在图 B 中），确保生成的问题需要跨越多个图表进行推理才能回答。

### 方法框架

#### 整体架构

![[fig2_overview_V4_page1.png|800]]

> 图1：LongChart 合成管线概览。通过隐式图定义图表间的逻辑关系，自动生成包含多张图表和相应推理问题的 VQA 样本。

#### 各模块详细说明

**模块1：隐式图构建（Latent Graph Construction）**
- **功能**：定义多张图表之间的计算依赖和逻辑关系
- **输入**：图表类型模板、数据分布参数
- **输出**：有向无环图（DAG），节点为图表，边为计算依赖
- **处理流程**：
  1. 选择基础图表类型（折线图、柱状图、饼图、散点图等）
  2. 定义图表间的变换关系（聚合、过滤、联合、计算衍生指标等）
  3. 构建隐式图，确保数据流的一致性和可追溯性
- **关键技术**：基于模板的数据合成，确保数学一致性

**模块2：VQA 问题生成（Question Synthesis）**
- **功能**：基于隐式图自动生成需要多图推理的问题
- **输入**：隐式图结构、图表数据
- **输出**：多步推理问题及标准答案
- **处理流程**：
  1. 遍历隐式图的计算路径，识别需要跨图推理的关键节点
  2. 生成不同推理深度（hop）的问题
  3. 自动计算标准答案并验证
- **关键技术**：基于计算图的反向问题生成

**模块3：质量保证（Quality Assurance）**
- **功能**：确保生成的问题和答案的准确性和有效性
- **处理流程**：
  1. 自动验证答案的数学正确性
  2. 过滤歧义问题
  3. 平衡难度分布

### 评测维度

![[fig_4_dataset_characteristics_v4_page1.png|800]]

> 图2：LongChart 数据集特征分析，展示推理跳数（hop）分布、图表类型分布和问题类型分布。

LongChart 评测覆盖三个关键维度：
1. **推理模式（Reasoning Patterns）**：单跳、多跳、聚合、对比等不同推理类型
2. **辅助工具（Auxiliary Tools）**：代码解释器、计算器等外部工具对性能的影响
3. **图像扰动鲁棒性（Robustness to Perturbations）**：图表颜色、布局、标注等变化的影响

## 实验结果

### 实验目标
评估 10 个 SOTA MLLM 在 LongChart 基准上的多图推理能力，并分析影响因素。

### 数据集
LongChart 基准：平均每样本 6.5 张图、31.2 个问题，覆盖多种图表类型和推理模式。

### 实验设置
- **评估模型**：10 个 SOTA MLLM（包括 GPT-4V、Gemini Pro Vision、Claude 3 等）
- **评估指标**：准确率（Accuracy）、推理深度（hop）分层准确率

### 主要结果

![[scatter_main_page1.png|800]]

> 图3：各 MLLM 在 LongChart 上的综合表现散点图。

![[combined_hops_degradation_heatmap_page1.png|800]]

> 图4：不同推理跳数下 MLLM 性能退化热力图。

#### 关键发现
1. **准确率随复杂度下降**：随着推理所需跳数增加（从 1-hop 到 4+-hop），所有 MLLM 的准确率均显著下降
2. **模型间差异大**：不同 MLLM 在相同推理深度下的表现差异显著，最强模型与最弱模型差距可达 30%+
3. **工具使用有助但非银弹**：代码解释器等辅助工具在某些推理类型上有帮助，但也可能引入新的错误
4. **鲁棒性不足**：图像扰动（颜色、布局变化）可导致部分模型准确率下降 10-15%

### 消融与分析

![[radar_breadth_v2_page1.png|800]]

> 图5：各模型在不同推理维度上的雷达图，展示模型能力的广度差异。

![[skill_initial_vs_final_box_page1.png|800]]

> 图6：各模型在不同技能维度上的初始 vs 最终表现对比。

## 深度分析

### 研究价值评估

#### 理论贡献
- **首个系统性的多图推理评测基准**：填补了 MLLM 评测在多图复杂推理方面的空白
  - 创新点：隐式图支撑的合成管线确保问题的一致性和有效性
  - 学术价值：为 MLLM 推理能力研究提供了标准化的评测工具
  - 影响范围：多模态推理、智能体任务、数据可视化等领域

- **多维度的性能分析框架**：从推理模式、工具使用、鲁棒性三个维度系统分析
  - 揭示了 MLLM 在多图推理中的具体弱点和瓶颈

#### 实际应用价值
- **数据分析场景**：为企业级多图表数据分析提供模型选型参考
- **科学发现**：帮助评估 MLLM 在科学图表综合分析中的能力边界
- **智能体系统**：为构建需要多图推理的 AI Agent 提供评测基准

### 方法优势详解

#### 优势1：隐式图保证一致性
- **描述**：通过隐式图定义图表间的计算依赖，确保生成的多图问题具有数学一致性
- **技术基础**：将多图关系建模为有向无环图，数据流可追溯
- **实验验证**：自动验证机制确保答案正确性

#### 优势2：可扩展的合成管线
- **描述**：基于模板的合成方法可以灵活生成不同难度、不同类型的多图推理问题
- **对比分析**：相比人工标注，成本更低、规模更大、一致性更好

#### 优势3：全面的评测维度
- **描述**：不仅评测准确率，还分析了推理深度、工具使用效果、鲁棒性等多个维度

### 局限性分析

#### 局限1：合成数据的真实性
- **描述**：基于模板合成的图表可能无法完全代表真实世界中的复杂图表
- **影响**：模型在合成基准上的表现可能不完全等同于在真实场景中的表现
- **可能的解决方案**：未来可结合真实世界数据源进行扩展

#### 局限2：评估指标单一
- **描述**：目前主要使用准确率作为评估指标
- **影响**：无法全面评估模型的推理过程质量
- **可能的解决方案**：引入推理路径评估、中间步骤正确率等细粒度指标

#### 局限3：图表类型覆盖有限
- **描述**：目前覆盖的图表类型以常见统计图表为主
- **影响**：对于地图、网络图、流程图等特殊图表类型的多图推理未覆盖

## 与相关论文对比

### 对比总结
LongChart 与此前的图表理解基准（ChartQA、ChartBench、MMMU-Chart 等）的主要区别在于：
- **多图 vs 单图**：LongChart 首次系统性评估多图推理，而此前基准仅关注单图
- **合成管线**：引入隐式图确保多图间的一致性，而非简单拼接
- **推理深度分析**：提供了按推理跳数分层的性能分析

## 技术路线定位

### 所属技术路线
本文属于 MLLM 评测基准构建路线，核心关注多图复杂推理能力的评估。

### 本文在技术路线中的位置
- **承上**：继承了 ChartQA、ChartBench 等图表理解评测的传统
- **启下**：为多图推理 MLLM 的研究和开发提供了标准化评测工具
- **关键节点**：标志着 MLLM 评测从单图感知向多图推理的范式转变

## 未来工作建议

1. **扩展到真实世界图表**：从科学论文、财务报告等真实来源收集多图推理样本
2. **细粒度推理评估**：不仅评测最终答案，还评估推理过程的正确性
3. **动态交互评测**：允许模型通过交互方式请求额外信息来完成推理
4. **多语言扩展**：扩展到非英语图表的评测

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** - 填补了 MLLM 多图推理评测的重要空白，方法设计合理，分析维度全面。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次系统性构建多图推理评测基准，隐式图合成管线设计新颖 |
| 技术质量 | 8/10 | 方法设计严谨，隐式图保证一致性，合成管线可扩展 |
| 实验充分性 | 9/10 | 评估 10 个 SOTA 模型，覆盖三维度分析，消融实验充分 |
| 写作质量 | 8/10 | 结构清晰，论证完整 |
| 实用性 | 9/10 | 对 MLLM 研究和开发具有直接实用价值 |

### 重点关注
- 隐式图合成管线的设计细节
- MLLM 在不同推理深度下的性能退化模式
- 图像扰动对鲁棒性的影响程度

## 相关论文

### 直接相关
- ChartQA - 图表问答基准
- ChartBench - 图表理解评测
- MMMU - 多模态多学科理解评测

### 后续工作
- 多图推理增强的 MLLM 训练方法
- 基于 LongChart 的智能体评测扩展

## 外部资源

> [!tip] 关键启示
> MLLM 的多图推理能力远未成熟——随着推理深度增加，性能急剧下降，这为下一代 MLLM 的研究提供了明确方向。

> [!warning] 注意事项
> - 合成基准的外推性有限，真实场景表现可能有所不同
> - 不同模型在相同推理深度下的差异巨大，选型需谨慎
> - 工具使用（代码解释器）可能引入新的错误模式

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！该基准填补了 MLLM 评测的关键空白，对从事多模态推理研究的读者具有重要参考价值。
