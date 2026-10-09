---
date: "2026-10-09"
paper_id: "2610.10787"
title: "NavGPT-3: Harnessing Context in a Hierarchical Navigation Runtime"
authors: "Gengze Zhou, Yicong Hong, Jiazhao Zhang, Xunyi Zhao, Jian Zhou, Zixing Lei, Zun Wang, Chongyang Zhao, Xionghui Chen, Stephen Gould, Anton van den Hengel, Qi Wu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 智能体
  - Agent
  - 具身导航
  - 视觉语言动作
  - VLA
quality_score: "9.6/10"
related_papers: []
created: "2026-10-09"
updated: "2026-10-09"
status: analyzed
---

# NavGPT-3: Harnessing Context in a Hierarchical Navigation Runtime

## 核心信息
- **论文ID**：2610.10787
- **作者**：Gengze Zhou, Yicong Hong, Jiazhao Zhang, Xunyi Zhao, Jian Zhou, Zixing Lei, Zun Wang, Chongyang Zhao, Xionghui Chen, Stephen Gould, Anton van den Hengel, Qi Wu
- **机构**：--
- **发布时间**：2026-10-07
- **分类**：cs.RO / cs.AI / cs.CL / cs.CV / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.10787) | [PDF](https://arxiv.org/pdf/2610.10787)

## 研究问题
经过长程 agentic 强化学习训练的语言模型，能通过推理泛化知识、表达精确动作、跨多步追求目标，提升了具身智能体"理解与决策"的上限。但**物理交互**仍属于动作策略（action policy）的领域——它提供密集、低延迟的控制。**核心问题**：如何把"高层语言模型推理"与"底层物理动作控制"这两类模型有效连接起来，让机器人既能思考又能实时反应？

## 方法概述

### 核心方法

1. **NavGPT-3 分层导航运行时（hierarchical runtime）**
   - 在其上构建了一个类似操作系统的运行时：**推理、行动、监控**作为线程运行，各自拥有独立的上下文、工具与权限
   - 运行时负责调度线程、决定哪个线程控制机器人运动
   - 通过**中断与线程切换**，机器人能对突发真实世界事件做出反应

2. **动作策略 NavGPT VLA**
   - 在 **19.28M** 样本上训练
   - 使用 **codec allocation** 按场景变化比例分配视觉 token
   - 单独 8B 模型已达 R2R-CE 74.51 SR、RxR-CE 78.19 SR

### 方法架构

![[2610.10787_fig1.png|600]]

### 关键创新

1. **OS 式分层运行时** - 将推理/行动/监控解耦为独立线程，通过中断与切换实现"思考"与"反应"的解耦
2. **codec allocation 视觉 token 分配** - 按场景变化动态分配视觉 token，在效率与理解间取得平衡
3. **人类级具身导航** - 首次让自主智能体在 RxR-CE 上达到人类跟随者水平

## 实验结果

### 数据集/基准
- **R2R-CE**（Room-to-Room，连续环境）与 **RxR-CE**（Room-across-Room）
- 完整 harness 与单独 VLA 的消融

### 主要结果
- 完整 harness：**R2R-CE 81.51 SR（SOTA）**
- **首次达到人类水平**：RxR-CE 成功率 90.43 vs 人类 90.4，路径保真度 78.47 vs 77.7 nDTW
- 效率：每 episode 1 分 22 秒，而人类约 3 分钟
- 反应时间：从每个语言模型决策 3-19 秒，降到每个动作策略步 0.5-1 秒（1-2 Hz）
- 将开源所有模型、代码与评测记录

## 深度分析

**价值**：NavGPT-3 是"语言模型推理 + 低层动作策略"解耦范式的标杆工作，其 OS 式运行时把两类模型的分工显式化——语言模型负责慢速推理与长期目标，VLA 负责快速低延迟控制，通过线程中断应对突发。这一"思考快慢系统"的工程落地，为连接前沿语言智能与物理控制提供了可复用的架构模板。

**局限与风险**：
- 运行时调度策略的泛化性（超出手工设计的线程切换规则）仍需进一步自动化
- 19.28M 训练样本的采集与成本未充分披露
- 人类级结果是特定基准（RxR-CE）上的，跨场景/跨环境鲁棒性待验证

## 相关论文对比
- 与 [[RoboJEPA_Scaling_Robotic_Latent_World_Models|RoboJEPA]] 同属具身/机器人智能体研究，但 NavGPT-3 强调运行时与 VLA 连接，RoboJEPA 强调世界模型的缩放律
- 与 [[RFChipAgent_Multi-Agentic_AI_Flow_for_Analog_RF_Chip_Design|RFChipAgent]] 同为"LLM 智能体 + 物理/工程实体"结合，但面向导航而非 EDA
