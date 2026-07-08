---
date: "2026-07-08"
paper_id: "arXiv:2607.06560"
title: "Vision as Unified Multimodal Generation"
authors: "Xiaoyang Han, Jianhua Li, Kewang Deng, Zukai Chen, Xuanke Shi, Sihan Wang, Boxuan Li, Linyan Wang, Siyi Xie, Xin You, Jinsheng Quan, Zhongang Cai, Haiwen Diao, Ziwei Liu, Lei Yang, Dahua Lin, Quan Wang"
institutions: "SenseTime Research (商汤), Nanyang Technological University, CUHK, 北京大学, 上海交通大学, 浙江大学"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态技术
  - 计算机视觉
  - 统一生成
  - 目标检测
  - 分割
  - 深度估计
quality_score: "7.5/10"
created: "2026-07-08"
updated: "2026-07-08"
status: analyzed
---

# SenseNova-Vision: Vision as Unified Multimodal Generation

## 核心信息
- **论文ID**：arXiv:2607.06560
- **作者**：Xiaoyang Han, Jianhua Li, Kewang Deng, Zukai Chen 等（商汤研究院 + NTU + CUHK + PKU + SJTU + ZJU）
- **机构**：SenseTime Research, 南洋理工大学, 香港中文大学, 北京大学, 上海交通大学, 浙江大学
- **发布时间**：2026-07-07
- **链接**：[arXiv](https://arxiv.org/abs/2607.06560) | [PDF](https://arxiv.org/pdf/2607.06560)
- **代码/模型**：https://github.com/OpenSenseNova/SenseNova-Vision

## 摘要翻译

### 英文摘要
We formulate computer vision as unified multimodal generation, where heterogeneous visual tasks are expressed through the native text and image generation spaces of a unified multimodal model (UMM), without task-specific architectures. With this formulation, the single model SenseNova-Vision matches leading task-specialized systems across structured visual understanding, dense geometric prediction, segmentation, and multi-view visual geometry. Natural-language instructions and optional visual prompts specify the task, target regions or views, and decoding convention. Responses are then generated as text for symbolic records, images for dense spatial targets, or mixed outputs for compositional tasks. To enable large-scale training, we convert heterogeneous computer vision annotations into instruction-response examples compatible with these native generation spaces. This conversion yields the SenseNova-Vision Corpus, a computer-vision instruction-response corpus spanning text, image, and mixed text-and-image targets.

### 中文翻译
我们将**计算机视觉形式化为统一的多模态生成**：异构的视觉任务通过统一多模态模型（UMM）的原生文本和图像生成空间来表达，无需任务特定架构。通过这种形式化，单一模型SenseNova-Vision在结构化视觉理解、密集几何预测、分割和多视图视觉几何等任务上匹配领先的专用系统。自然语言指令和可选的视觉提示指定任务、目标区域或视图以及解码约定。响应以文本（符号记录）、图像（密集空间目标）或混合输出（组合任务）的形式生成。为了支持大规模训练，我们将异构的CV标注转换为与这些原生生成空间兼容的指令-响应样本。此转换产生了**SenseNova-Vision Corpus**——一个覆盖文本、图像和混合文本-图像目标的CV指令-响应语料库。

### 核心要点提炼
- **研究背景**：传统CV需要为每个任务设计专用架构和head
- **研究动机**：能否用单一生成模型统一所有CV任务？
- **核心方法**：将所有CV任务（检测、OCR、分割、深度、关键点、姿态等）表达为文本+图像生成
- **主要结果**：单一模型在多个CV任务上匹配专用模型性能
- **研究意义**：标志着CV从"识别范式"向"生成范式"的根本转变

## 研究背景与动机

### 领域现状
传统的计算机视觉由一系列**任务特定架构**组成：
- 目标检测：Faster R-CNN, DETR, YOLO → 专用检测头
- 分割：Mask R-CNN, SAM → 专用掩码头
- 深度估计：DPT, ZoeDepth → 专用回归头
- OCR/关键点：各自的专用框架

### 核心问题
每个任务需要不同的模型、不同的head、不同的训练流程——这从根本上限制了CV向通用基础模型演进的路径。

### 研究动机
如果LLM可以通过单一模型（GPT）统一所有NLP任务，CV能否走同样的路？SenseNova-Vision 的回答是：**将CV也变成生成问题**。

## 方法概述

### 核心思想
将任何CV任务表达为统一多模态模型的原生生成空间中的输入-输出对：
- **输入**：图像 + 自然语言指令（如"检测所有人"）+ 可选的视觉提示
- **输出**：文本（坐标、类别）、图像（分割掩码、深度图）、或混合输出

![[fig3_system_overview_page1.png|800]]

> 图1：SenseNova-Vision 系统架构。通过自然语言指令将异构CV任务统一为文本+图像生成。

### 方法框架

#### 任务统一化
CV任务被映射到三种输出模式：

| 输出模式 | CV任务 | 生成格式 |
|----------|--------|----------|
| **文本** | 检测、OCR、关键点 | 序列化坐标+类别文本 |
| **图像** | 分割、深度、法线 | 像素级密集预测图像 |
| **混合** | 交互式分割、实例分割 | 文本掩码描述+分割图 |

#### SenseNova-Vision Corpus
- 将异构CV标注统一转换为指令-响应对
- 覆盖文本、图像、混合三种目标
- 大规模多源数据整合

#### 训练策略
- 从现成预训练UMM出发
- 主要在SenseNova-Vision Corpus上训练
- 辅助多模态数据作为能力保持混合
- **无需任何任务特定head或架构修改**

### 关键创新
- **范式统一**：首次将检测、分割、深度估计、OCR、关键点、姿态估计等全部统一为生成任务
- **原生多模态输出**：支持文本+图像+混合输出，超越纯文本VLM
- **无需任务特定架构**：单一模型匹配所有专用系统

## 实验结果

### 覆盖任务
- 结构化视觉理解（检测、OCR、关键点）
- 密集几何预测（深度、法线、点云）
- 分割（语义、实例、全景、交互式）
- 多视图视觉几何（相机姿态）
- 组合任务（类别+颜色+区域+视觉线索）

### 核心结果
- 单一模型在**所有任务**上匹配或超越专用模型
- 支持语言定义的变体（组合类别、颜色、区域等视觉线索）
- 开源模型和数据集

![[fig1_benchmark_overview_page1.png|800]]

> 图2：基准测试总览。单一SenseNova-Vision模型在异构CV任务族上实现竞争性性能。

## 深度分析

### 方法优势
1. **架构统一**：消除所有任务特定head，单一模型覆盖所有CV
2. **扩展性强**：新任务只需添加数据，无需修改模型架构
3. **交互灵活**：自然语言指令+视觉提示的组合提供丰富的交互模态

### 局限性分析
1. **生成效率**：逐像素生成分割掩码可能比专用架构慢
2. **精度极限**：在极端精度要求场景（如医学图像）可能不及专用模型
3. **数据依赖**：大规模统一训练数据是核心壁垒

## 我的综合评价

### 总体评分
**7.5/10** — 提出了CV领域的"GPT时刻"愿景：用统一生成范式替代任务特定架构。工程规模宏大，数据-模型双开源，对CV领域有重要影响。

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 统一生成范式的思路并非全新，但覆盖范围之广前所未有 |
| 技术质量 | 8/10 | 工程实现扎实，数据处理流水线完善 |
| 实验充分性 | 8/10 | 任务覆盖广泛，对比充分 |
| 写作质量 | 7/10 | 清晰的技术报告风格 |
| 实用性 | 8/10 | 开源模型+数据，直接可用 |

> [!tip] 关键启示
> CV的"识别→生成"范式转变正在加速。当所有视觉任务都能用生成来解，专用检测头/分割头/关键点头的时代可能即将终结。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！代表了CV向通用基础模型演进的重要一步，开源资源丰富，适合快速上手。
