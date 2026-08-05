---
date: "2026-08-05"
paper_id: "arXiv:2608.02365"
title: "Faster-WAM: Do World Action Models Need Deep Action Modules?"
authors: "Liheng Ma, Rui Heng Yang, Zhanguang Zhang, Mateo Clemente, Ziwen Hu, Tongtong Cao, Yingxue Zhang"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - World-Action-Model
  - Robot-Learning
  - Video-World-Model
  - Efficient-Architecture
  - Dock-of-Transformer
quality_score: "8.3/10"
created: "2026-08-05"
updated: "2026-08-05"
status: analyzed
---

# Faster-WAM: Do World Action Models Need Deep Action Modules?

## 核心信息
- **论文ID**：arXiv:2608.02365
- **作者**：Liheng Ma, Rui Heng Yang, Zhanguang Zhang, Mateo Clemente, Ziwen Hu, Tongtong Cao, Yingxue Zhang
- **机构**：--
- **发布时间**：2026-08-03
- **会议/期刊**：arXiv（投稿中）
- **链接**：[arXiv](http://arxiv.org/abs/2608.02365v1) | [PDF](https://arxiv.org/pdf/2608.02365v1)
- **领域**：具身智能、世界动作模型、高效架构

## 摘要翻译

### 英文摘要
World Action Models (WAMs) couple robot action prediction with video world models. Existing WAMs with shared-backbone and Mixture-of-Transformers designs generally tie the depth of the action module to that of the video backbone, resulting in substantial computational overhead and high inference latency. We introduce Dock of Transformer (DoT), a video-centric design principle that treats a pretrained video Transformer as a representation hub and connects lightweight output-heads through docking interfaces. We then introduce Faster-WAM, an instantiation of DoT for WAMs, which docks a single-layer action head onto a 30-layer video backbone. Without additional embodied pretraining, Faster-WAM achieves competitive performance on LIBERO and RoboTwin 2.0 while demonstrating strong out-of-distribution generalization on LIBERO-Plus. Faster-WAM also achieves the lowest end-to-end latency, requiring only 66.5 ms per inference — a 3.2× speedup over Fast-WAM.

### 中文翻译
世界动作模型（WAMs）将机器人动作预测与视频世界模型耦合。现有 WAM 采用共享骨干和混合 Transformer 设计，通常将动作模块的深度与视频骨干的深度绑定，导致大量计算开销和高推理延迟。我们提出 Dock of Transformer（DoT），一种以视频为中心的设计原则，将预训练视频 Transformer 视为表示枢纽，通过 docking 接口连接轻量级输出头。我们进一步提出 Faster-WAM（DoT 在 WAM 上的实例化），将单层动作头对接在 30 层视频骨干上。无需额外具身预训练，Faster-WAM 在 LIBERO 和 RoboTwin 2.0 上达到竞争性能，在 LIBERO-Plus 上展现强分布外泛化能力，端到端延迟仅 66.5ms——3.2 倍加速于 Fast-WAM。

### 核心要点提炼
- **研究背景**：世界动作模型将视频预测与动作预测耦合，但深层动作模块带来严重推理延迟
- **研究动机**：质疑"深层动作模块是否必要"这一隐含假设
- **核心方法**：DoT 设计原则——视频骨干作为表示中心，轻量动作头通过 docking 接口连接
- **主要结果**：3.2× 加速，66.5ms 端到端延迟，性能竞争 + OOD 泛化

## 研究背景与动机

### 领域现状
WAM 将机器人控制建模为两个耦合任务：(1) 预测未来视频帧；(2) 预测机器人动作。现有架构（如 UniPi、Susie、Fast-WAM）使用共享骨干或 MoE 设计，动作模块深度与视频模块相当。

### 核心问题
**Do World Action Models Need Deep Action Modules?** —— 标题即核心问题。深层动作模块是否必要？如果不需要，可以大幅降低推理延迟。

### 关键洞察
视频预测需要深层 Transformer 处理丰富视觉信息，但动作预测可能只需要访问视频骨干各层已提取的表示——不需要独立的深层处理。

## 方法概述

### Dock of Transformer (DoT) 设计原则

![[overview_diagram_v5_page1.png|800]]

> 图1：Faster-WAM 架构概览。DoT 将预训练视频 Transformer 作为表示枢纽，通过 docking 接口连接轻量动作头。

**核心组件**：

1. **视频骨干（Video Backbone）**：30 层预训练视频 Transformer，负责生成丰富的层次化视觉表示
2. **Docking 接口**：融合所有视频层的 key/value 表示，应用 RoPE 重对齐，形成"表示码头"
3. **轻量动作头**：仅 1 层 Transformer，从 docking 接口接收融合的多层表示，预测机器人动作

### 为什么"浅"也可以？

![[dot_diagram_page1.png|800]]

> 图2：DoT 设计图。视频骨干的多层表示通过 docking 接口融合，为浅层动作头提供丰富的多尺度视觉特征。

1. **表示复用**：视频骨干已从各层提取了不同抽象级别的特征
2. **KV 融合**：Docking 接口融合所有层的 key/value，单层动作头同时访问浅层纹理和深层语义
3. **RoPE 重对齐**：确保位置编码在融合多尺度特征后仍然保持一致

### 与 Fast-WAM 的关键差异

| 维度 | Fast-WAM | Faster-WAM |
|------|----------|------------|
| 动作模块深度 | 与视频骨干同深 | 仅 1 层 |
| 参数共享 | 部分共享 | 视频骨干冻结 |
| 推理延迟 | 高（~213ms） | 低（66.5ms，3.2×） |
| 表示访问 | 逐层对应 | 全层融合 |

## 实验结果

### 性能
- **LIBERO**：与 Deep WAM 竞争的性能水平
- **RoboTwin 2.0**：与实际部署相当的操控性能
- **LIBERO-Plus（OOD）**：强分布外泛化能力

### 效率
- 端到端延迟：**66.5 ms**（3.2× 加速）
- 无需额外具身预训练数据

## 深度分析

### 研究价值
**8.3/10** - 通过挑战"深层动作模块必要"的隐含假设，以简洁的架构设计实现显著加速。DoT 原则可推广至其他视频理解任务。

### 方法优势
1. **架构解耦**：视频和动作模块解耦，允许独立优化
2. **延迟友好**：单层动作头使推理极快，满足实时机器人控制需求
3. **无需额外预训练**：直接利用预训练视频 Transformer

### 局限性
1. **视频骨干依赖**：性能上界受限于视频骨干的质量
2. **仅验证单层**：未系统探索动作头最优深度（1-vs-2-vs-3 层）
3. **缺乏真实机器人验证**：主要在仿真基准上评估

## 相关论文
- [[Fast-WAM]] - 前驱工作，使用深层动作模块的 WAM
- [[UniPi]] - 统一视频-动作策略的先驱
- [[Susie]] - 使用 MoE 的 WAM 变体

> [!tip] 关键启示
> 在耦合系统中，"解耦"往往比"联合优化"更高效——视频需要深层，动作不一定需要。DoT 的洞察在于：把视频骨干当作"表示公共服务"，各任务头只需"接入"即可。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！如果你关注具身智能的实时推理或视频-动作耦合架构设计，这篇论文提供了清晰的工程洞察和可复用的设计原则。
