---
date: "2026-10-15"
paper_id: "arXiv:2610.22343"
title: "GeoState: Learning View-Decoupled Geometry States for 3D World Modeling"
authors: "Hongchang Liu, Jian Liu, Yujuan Ding, Liang Peng, Xun Jiang, Fei Ma, Xing Xu, Yi Bin"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - world-model
  - 3D
  - geometry-foundation-model
  - autonomous-driving
  - view-decoupling
quality_score: "8.0/10"
created: "2026-10-17"
updated: "2026-10-17"
status: analyzed
---

# GeoState: Learning View-Decoupled Geometry States for 3D World Modeling

## 核心信息
- **论文ID**：arXiv:2610.22343
- **作者**：Hongchang Liu, Jian Liu, Yujuan Ding, Liang Peng, Xun Jiang, Fei Ma, Xing Xu, Yi Bin
- **机构**：同济大学（Tongji University）、香港理工大学（Hong Kong Polytechnic University）、腾讯（Tencent）、广东省人工智能与数字经济实验室（深圳）
- **发布时间**：2026-10-15
- **会议/期刊**：Preprint
- **链接**：[arXiv](http://arxiv.org/abs/2610.22343v1) | [PDF](https://arxiv.org/pdf/2610.22343v1)
- **分类**：cs.CV, cs.AI, cs.LG, cs.RO
- **项目主页**：https://hongchang-liu.github.io/GeoState-project-page/

## 摘要翻译

### 英文摘要
Recent 3D world models increasingly use features from geometry foundation models (GFMs) as predictive world states. However, the view dependence of these features entangles scene dynamics with camera-induced variation, making state evolution harder to predict. We propose GeoState, a view-decoupled geometry representation for predictive 3D world modeling that separates scene-state evolution from camera-dependent observation. A scene tokenizer aggregates multi-view GFM features into a view-decoupled scene state, which a geometry decoder queries using camera rays. An autoregressive world model then predicts future scene states, while camera motion is modeled separately.

### 中文翻译
近期的 3D 世界模型越来越多地使用几何基础模型（GFM）的特征作为预测世界状态。然而，这些特征的**视角依赖性**将场景动态与相机引起的变动纠缠在一起，使状态演化更难预测。本文提出 **GeoState**，一种用于预测性 3D 世界建模的**视角解耦几何表示**，将场景状态演化与相机依赖的观测分离开。具体地，一个 scene tokenizer 将多视角 GFM 特征聚合为视角解耦的场景状态，几何解码器用相机射线查询该状态。自回归世界模型随后预测未来场景状态，而相机运动被单独建模。

### 核心要点提炼
- **核心问题**：GFM 特征围绕观测视角构建，相机视角变化会在特征空间中引起大幅变化（即使物理场景未变），使潜动态更难长期预测
- **核心方法**：GeoState = Scene Tokenizer（聚合多视角 GFM 特征 → 共享场景坐标系的紧凑场景状态）+ Query-conditioned Geometry Decoder（仅解码时引入视角）
- **关键设计**：分离应"内建在表示本身"——状态描述"世界是什么"，相机只决定"世界如何被观测"
- **主要结果**：Waymo 上超越先前 3D 世界模型（深度预测全时域领先），zero-shot 泛化到 KITTI 和 nuScenes
- **额外优势**：全程保持单一上下文尺度，无需逐帧尺度对齐

## 研究背景与动机

### 领域现状
3D 世界模型从像素空间预测（存在表示冗余、几何一致性难维持）转向几何锚定的状态空间。几何基础模型（GFM）通过大规模预训练学到了强几何先验，成为有吸引力的世界状态空间。VGGT-World 等已开始把冻结的 VGGT 特征作为世界状态预测未来演化。

### 现有方法的局限性
- **视角依赖（view dependence）**：GFM 特征围绕观测视角构建，相机移动即使场景静止也会引起特征空间的大幅变化
- **动态纠缠**：直接用 GFM 特征作预测状态时，时序模型必须同时解释"真实场景演化"和"相机引起的变动"两个不同来源
- **FR3D 的不足**：虽然分离了 ego-motion 与场景预测，但其预测状态仍继承重建表示的视角依赖结构

### 研究动机
作者主张：分离应当"内建在表示本身"——状态描述"世界是什么"，相机只决定"如何观测"。由此提出视角解耦的几何状态 GeoState。

## 研究问题

### 核心研究问题
1. 如何构造一个视角解耦的几何状态，将场景内容与观测方式显式分离？
2. 如何直接在场景状态空间中建模 3D 世界演化，同时独立建模相机演化？
3. 该表示能否支持长程几何预测、跨视角一致查询、以及跨数据集 zero-shot 泛化？

## 方法概述

### 核心思想
将视角条件化的 GFM 特征转化为"场景几何显式分离于相机依赖观测"的表示，然后直接在场景状态空间中进行 3D 世界建模。

### 方法框架

#### 整体架构
```
多视角观测 → Scene Tokenizer → 视角解耦场景状态（共享场景坐标系）
                                      │
                              Geometry Decoder（用查询相机射线）
                                      │
                            任意视角的几何预测
        （时序：autoregressive world model 预测未来场景状态，相机运动单独建模）
```

#### 各模块详细说明

**Scene Tokenizer**：给定多视角观测，将 GFM 几何特征压缩为一个紧凑的场景状态，表示在共享场景坐标系中；查询视角仅在解码时才被引入。

**Geometry Decoder**：接收场景状态 + 查询相机的射线，预测该视角观测到的几何。同一场景状态可被不同视角反复查询而无需重新编码。

**时序建模**：自回归 world model 只关注预测场景动态，相机演化独立建模。推理时，预测的未来状态可用"单独预测的相机轨迹"或"用户控制的相机"解码。

**尺度一致性**：整个 rollout 保持单一上下文尺度，避免逐帧尺度对齐。

## 实验结果

### 主要结果

| 维度 | 结果 |
|------|------|
| 深度预测（Waymo, in-domain） | 在所有报告时域上超越对比的 3D 世界模型 |
| 跨视角查询 | 几何一致的跨视角查询 |
| Zero-shot 泛化 | 泛化到 KITTI、nuScenes |
| 消融 | 视角解耦使场景表示更可预测 |

### 关键发现
- 视角解耦使场景表示更可预测（消融实验验证）
- 全程保持一致的尺度，无需逐帧尺度对齐
- 支持跨驾驶数据集的 zero-shot 泛化

## 深度分析

### 研究价值评估

#### 理论贡献
- **视角解耦状态的提出**：明确识别"视角耦合"作为 GFM 特征用于世界状态时的核心局限，并给出表示层面的解决方案
- **"状态描述世界，相机决定观测"的原则**：把分离从"时序建模层面"下沉到"表示层面"，是一个清晰且可泛化的设计哲学

#### 实际应用价值
- 对自动驾驶、机器人、具身智能等需要长期几何预测的场景直接相关
- 支持用户控制相机，为交互式世界模拟提供可能

### 方法优势与局限
**优势**：问题动机清晰（视角依赖是真实痛点）、设计原则简洁、实验覆盖 in-domain + zero-shot + 消融。

**局限**：评估主要聚焦自动驾驶驾驶场景的几何（深度）预测；GFM 特征本身的质量仍是上限；对更复杂动态（如非刚性形变、可交互物体）的适用性未验证。

![[2610.22343_page1.png|600]]
