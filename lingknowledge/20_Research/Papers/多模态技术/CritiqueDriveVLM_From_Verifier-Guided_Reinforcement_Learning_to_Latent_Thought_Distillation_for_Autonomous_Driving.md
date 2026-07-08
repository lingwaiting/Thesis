---
date: "2026-07-08"
paper_id: "arXiv:2607.04179"
title: "CritiqueDriveVLM: From Verifier-Guided Reinforcement Learning to Latent Thought Distillation for Autonomous Driving"
authors: "Zhaohong Liu, Hao Ye, Xianlin Zhang, Mengshi Qi"
institutions: "Beijing University of Posts and Telecommunications (北京邮电大学)"
domain: "多模态技术"
tags:
  - 论文笔记
  - 多模态技术
  - 强化学习
  - VLM
  - 自动驾驶
  - 知识蒸馏
  - Chain-of-Thought
  - ECCV
quality_score: "7.8/10"
created: "2026-07-08"
updated: "2026-07-08"
status: analyzed
---

# CritiqueDriveVLM: From Verifier-Guided Reinforcement Learning to Latent Thought Distillation for Autonomous Driving

## 核心信息
- **论文ID**：arXiv:2607.04179
- **作者**：Zhaohong Liu, Hao Ye, Xianlin Zhang, Mengshi Qi
- **机构**：北京邮电大学，网络与交换技术国家重点实验室
- **发布时间**：2026-07-05
- **会议/期刊**：**Accepted at ECCV 2026**
- **链接**：[arXiv](https://arxiv.org/abs/2607.04179) | [PDF](https://arxiv.org/pdf/2607.04179)
- **代码**：https://github.com/MICLAB-BUPT/CritiqueDriveVLM

## 摘要翻译

### 英文摘要
End-to-end Vision-Language Models (VLMs) show immense potential in autonomous driving. However, standard Supervised Fine-Tuning (SFT) often suffers from reasoning hallucinations and conservative biases. While traditional tool-augmented frameworks and Chain-of-Thought (CoT) approaches mitigate these issues, they incur exorbitant token consumption and unacceptable latency, rendering real-time deployment impractical. To resolve this reliability-efficiency trade-off, we propose CritiqueDriveVLM, a novel unified three-stage framework internalizing reasoning directly into the VLM. First, we introduce Critique-Driven Multi-Turn Reinforcement Learning (RL) guided by a multi-dimensional verifier. By providing granular scalar feedback and a multi-turn penalty, we force the policy to internalize logical deduction, cultivating a robust System-2 Teacher that achieves high accuracy without fragile external tools. Subsequently, we propose Latent Thought Distillation to overcome the latency bottleneck. By aligning the Student's latent representations with the Teacher's fully converged reasoning states, we compress deep logical capabilities into a fast, CoT-free System-1 Student. Extensive experiments on the widely-used DriveLMM-o1 benchmark demonstrate remarkable improvements. Compared to the base model, our tool-free Teacher significantly boosts Multiple Choice Quality (MCQ) from 55.54% to a state-of-the-art 76.54%. Crucially, our distilled Student preserves competitive reasoning depth while drastically minimizing generation length to an average of merely 28 tokens. This slashes inference latency by 88% (from 3482 ms to 416 ms), paving a highly robust pathway for low-latency autonomous driving.

### 中文翻译
端到端视觉语言模型（VLM）在自动驾驶中展现了巨大潜力。然而，标准的监督微调（SFT）往往遭受推理幻觉和保守偏差的困扰。传统的工具增强框架和链式思考（CoT）方法虽能缓解这些问题，但会带来高额的token消耗和不可接受的延迟，使实时部署不切实际。为了解决这种**可靠性-效率权衡**，我们提出了**CritiqueDriveVLM**，一个新颖的统一三阶段框架，将推理能力直接内化到VLM中。首先，我们引入由多维验证器引导的**批判驱动多轮强化学习（RL）**。通过提供细粒度的标量反馈和多轮惩罚，我们迫使策略内化逻辑演绎，培养出鲁棒的**System-2教师模型**，该模型无需脆弱的外部工具即可实现高准确率。随后，我们提出了**隐式思维蒸馏**来克服延迟瓶颈。通过将学生模型的隐表示与教师模型完全收敛的推理状态对齐，我们将深层逻辑能力压缩为快速的、无CoT的**System-1学生模型**。在广泛使用的DriveLMM-o1基准上的广泛实验展示了显著的改进。与基础模型相比，我们的无工具教师模型将MCQ准确率从55.54%显著提升到75.54%的SOTA水平。关键的是，蒸馏后的学生模型保持了有竞争力的推理深度，同时将生成长度大幅缩短至平均仅28个token。这使推理延迟降低了**88%**（从3,482ms降至416ms），为低延迟自动驾驶铺平了高效鲁棒的路径。

### 核心要点提炼
- **研究背景**：VLM在自动驾驶中的推理（CoT）带来高延迟，无法满足实时性要求
- **研究动机**：需要在不牺牲推理质量的前提下，将慢思考蒸馏为快反应
- **核心方法**：三阶段框架——(1) 验证器引导多轮RL构建System-2教师 (2) 隐式思维蒸馏 (3) System-1学生端到端推理
- **主要结果**：MCQ准确率55.54%→76.54%（SOTA），推理延迟3,482ms→416ms（88%↓）
- **研究意义**：为实时VLM推理提供了"从慢思考到快反应"的可行路径

## 研究背景与动机

### 领域现状
自动驾驶中的VLM面临核心矛盾：
- **CoT推理**：提供深层逻辑能力，但token消耗大（3,482ms），无法实时部署
- **轻量推理**：速度快但容易产生幻觉和保守偏差
- **工具增强**：依赖外部工具（检测器、规划器等），系统复杂且脆弱

### 研究动机
能否将System-2的深层推理能力"蒸馏"为System-1的快速前馈计算，在保持推理质量的同时实现实时推理？

## 方法概述

### 核心思想
构建"System-2教师→System-1学生"的知识传递管道：先通过RL+验证器训练一个具有强推理能力的教师VLM，再通过隐空间对齐将推理能力蒸馏为前馈模式。

![[1_page1.png|600]]

> 图1：CritiqueDriveVLM 三阶段框架：验证器引导RL构建System-2教师，隐式思维蒸馏压缩为System-1学生。

### 方法框架

#### 阶段1：Critique-Driven Multi-Turn RL
- **多维验证器**：提供细粒度标量反馈（准确性、安全性、效率等维度）
- **多轮惩罚机制**：鼓励模型在推理过程中自我修正
- **目标**：让VLM内化逻辑演绎能力，无需外部工具

#### 阶段2：Latent Thought Distillation
- **隐空间对齐**：将学生的隐状态与教师完全收敛的推理状态对齐
- **无需CoT文本**：直接传递隐式推理模式
- **关键优势**：避免了传统的文本蒸馏中的token膨胀问题

#### 阶段3：System-1 Fast Inference
- **CoT-free**：学生模型直接输出答案，无需中间推理文本
- **平均28 tokens**：极简输出，远少于典型的CoT推理
- **416ms延迟**：满足实时自动驾驶需求

### 关键创新
- **三阶段统一框架**：RL→蒸馏→推理的完整流水线
- **多维验证器引导**：比单维奖励信号更稳定的RL训练
- **隐式思维蒸馏**：不依赖文本CoT的知识传递

## 实验结果

### 主要结果（DriveLMM-o1基准）

| 模型 | MCQ准确率 | 推理延迟 | 生成长度 |
|------|----------|----------|----------|
| 基础模型 (SFT) | 55.54% | -- | -- |
| System-2 Teacher (RL后) | **76.54%** | 3,482ms | 长CoT |
| System-1 Student (蒸馏后) | 竞争性深度 | **416ms** | **28 tokens** |

### 关键发现
- RL训练带来 +21% MC准确率提升
- 蒸馏实现 88% 延迟降低
- 学生模型保留教师的推理深度

![[2_page1.png|600]]

> 图2：推理延迟与准确率的权衡分析。

## 深度分析

### 方法优势
1. **端到端可部署**：学生模型无需外部工具链，单一VLM完成端到端推理
2. **推理深度不降**：在88%延迟降低的同时保留竞争性推理能力
3. **RL训练稳定性**：多维验证器提供更丰富的训练信号

### 局限性分析
1. **场景局限**：仅验证了自动驾驶场景，泛化到其他VLM任务待探索
2. **教师依赖**：学生质量上限受限于教师模型的推理能力
3. **真实部署**：416ms在极端场景下可能仍不够快

## 我的综合评价

### 总体评分
**7.8/10** — 提出了从CoT到隐式推理的实用路径，实验结果显著，对实时VLM部署有重要参考价值。

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 隐式思维蒸馏+多维验证器RL的组合是新颖的 |
| 技术质量 | 8/10 | 三阶段设计合理，工程实现扎实 |
| 实验充分性 | 7/10 | DriveLMM-o1实验充分，但缺少更多场景验证 |
| 写作质量 | 8/10 | 动机清晰，方法描述详细 |
| 实用性 | 8/10 | 直接可部署，开源代码，对自动驾驶有实用价值 |

> [!tip] 关键启示
> "慢思考"的推理能力可以通过隐空间蒸馏转化为"快反应"——这对于所有需要实时推理的VLM应用都有借鉴意义。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！为VLM实时推理提供了可复现的解决方案，RL+蒸馏的组合范式值得关注。
