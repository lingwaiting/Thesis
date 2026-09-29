---
date: "2026-09-29"
paper_id: "arXiv:2609.33872"
title: "Robot-GST: geometry-aware spatial-temporal robot policy representation and evaluation"
authors: "Sichao Liu, Zekun Wang, Lixuan Tang, Yiming Li, Xiaohan Wang, Hanzhi Zhang, Daqiang Guo, Peng Zhou, Lihui Wang"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 机器人操作
  - 3D高斯泼溅
  - 世界模型
  - 视觉语言模型
  - Real-to-Sim
quality_score: "8.4/10"
created: "2026-09-29"
updated: "2026-09-29"
status: analyzed
---

# Robot-GST: geometry-aware spatial-temporal robot policy representation and evaluation

## 核心信息
- **论文ID**：arXiv:2609.33872
- **作者**：Sichao Liu, Zekun Wang, Lixuan Tang, Yiming Li, Xiaohan Wang, Hanzhi Zhang, Daqiang Guo, Peng Zhou, Lihui Wang
- **机构**：KTH、EPFL、Idiap 研究院、北京航空航天大学、香港科技大学（广州）、大湾区大学、香港理工大学
- **发布时间**：2026-09-27
- **链接**：[arXiv](https://arxiv.org/abs/2609.33872) | [PDF](https://arxiv.org/pdf/2609.33872) | [项目页](https://robot-gst.github.io)
- **分类**：cs.RO, cs.AI, cs.LG

## 摘要翻译

### 英文摘要
Robotic manipulation policies are advancing rapidly with increasing reliance on vision-language models for end-to-end decision making. However, reliable deployment remains challenging because many policies lack explicit mechanisms for predicting task outcomes and evaluating whether generated actions will achieve desired final states, causing execution errors to accumulate during long-horizon manipulation. We present Robot-GST, a geometry-aware spatio-temporal behaviour representation and evaluation framework that constructs a Gaussian-SAM robotic environment for real-to-sim policy verification and improves the reliability of real-world manipulation deployment. Our approach constructs a high-fidelity robotic environment from RGB-D observations using 3D Gaussian Splatting and SAM3D, enabling "simulation and evaluation before acting". It integrates visual observations and language instructions with spatio-temporal reasoning for long-horizon task planning using large vision-language models. To bridge high-level planning and real-world execution, we introduce Gaussian-aware final-state estimation through geometric sampling and state-based trajectory planning. Before execution, candidate action sequences are simulated and evaluated in the Gaussian-SAM environment to filter infeasible behaviours.

### 中文翻译
机器人操作策略发展迅速，越来越多地依赖视觉语言模型做端到端决策。然而，可靠部署依然困难，因为许多策略缺乏预测任务结果、评估"所生成动作能否达到期望最终状态"的显式机制，导致长程操作中执行误差不断累积。作者提出 **Robot-GST**，一个几何感知的时空行为表征与评估框架，构建 **Gaussian-SAM 机器人环境**用于 real-to-sim 策略验证，提升真实世界操作部署的可靠性。该方法利用 **3D Gaussian Splatting 与 SAM3D**，从 RGB-D 观测构建高保真机器人环境，实现"先仿真评估、后执行"。它结合视觉观测与语言指令，用大型视觉语言模型进行时空推理，完成长程任务规划。为弥合高层规划与真实执行之间的鸿沟，作者引入**高斯感知的最终状态估计**（通过几何采样）与**基于状态的轨迹规划**。在执行前，候选动作序列会在 Gaussian-SAM 环境中被仿真与评估，以过滤不可行的行为。

### 核心要点提炼
- **研究背景**：机器人操作策略依赖 VLM 做端到端决策，但缺乏结果预测/执行评估机制，长程任务误差累积。
- **研究动机**：需要一个"先仿真、后执行"的可验证环境，过滤不可行动作。
- **核心方法**：用 3DGS + SAM3D 从 RGB-D 构建 Gaussian-SAM 环境，结合 LVLM 时空推理与高斯感知最终状态估计。
- **主要结果**：在刚性、软体、可变形物体的代表性操作任务上验证，提升操作可靠性。
- **研究意义**：把几何感知重建 + 高质量渲染/仿真结合，为机器人操作行为评估提供可扩展方案。

## 研究背景与动机

### 领域现状
机器人操作策略日益依赖视觉语言模型（VLM）做端到端决策。但端到端黑盒的一个关键缺陷是：**模型不预测结果、不评估动作是否可行**，动作序列直接落地，长程任务中误差逐步累积。

### 现有方法的局限性
现有方法往往缺少"执行前验证"环节：要么无法预测任务最终状态，要么无法评估候选动作序列的可行性，导致不可行动作被直接执行、需要反复试错。

### 研究动机
作者提出"**simulation and evaluation before acting**"（先仿真评估、后执行）的理念：在执行前先在重建的高保真环境中模拟候选动作、过滤不可行行为，从而提升真实部署的可靠性。

## 研究问题

如何构建一个几何感知的、可仿真的机器人环境，使策略在执行前就能预测最终状态、评估候选动作的可行性，从而提升长程操作的真实世界可靠性？

## 方法概述

### 核心思想
用 3DGS + SAM3D 从 RGB-D 重建高保真机器人环境（Gaussian-SAM），结合 LVLM 时空推理做长程规划，并在执行前用几何采样的最终状态估计 + 轨迹规划 + 仿真评估来过滤不可行行为。

### 方法框架

#### 整体架构

![[overview_page1.png|600]]

> 图1：Robot-GST 整体框架，展示"重建 → 规划 → 评估 → 执行"的完整流程。

**流程**：
1. **环境重建**：从 RGB-D 观测，用 3D Gaussian Splatting + SAM3D 构建高保真 Gaussian-SAM 机器人环境。
2. **长程规划**：结合视觉观测与语言指令，用 LVLM 进行时空推理，生成高层任务计划。
3. **最终状态估计**：高斯感知的最终状态估计（几何采样），预测动作序列的最终状态。
4. **轨迹规划**：基于状态的轨迹规划，把高层计划转化为可执行轨迹。
5. **执行前评估**：候选动作序列在 Gaussian-SAM 环境中仿真，过滤不可行行为。
6. **真实执行**：只执行通过评估的动作序列。

#### 关键机制说明

**机制1：Gaussian-SAM 环境构建**
- 3D Gaussian Splatting 提供高质量可微渲染，SAM3D 提供语义分割，共同构建可仿真的高保真环境。

**机制2：高斯感知最终状态估计**
- 通过几何采样估计动作序列的最终状态，显式预测"动作会导向何种状态"，弥补端到端黑盒的缺陷。

**机制3：执行前仿真过滤**
- 候选动作先仿真、再评估，过滤不可行行为，从源头减少长程任务中的误差累积。

### 方法架构图

![[plan-sim_page1.png|600]]

> 图2：规划-仿真流程示意，展示候选动作如何在 Gaussian-SAM 环境中被评估与过滤。

![[gs-reconstruction_page1.png|600]]

> 图3：高斯重建（3DGS）示意图，展示从 RGB-D 到高保真环境的重建过程。

## 实验结果

### 实验目标
验证几何感知时空推理 + 状态感知执行能否提升不同物体类别（刚性/软体/可变形）上的操作可靠性。

### 实验设置
- **任务**：立方体放置（cube placing）、玩具装箱（toy packing）、鸭子重排（duck rearrangement）等代表性操作任务。
- **物体类别**：刚性、软体、可变形物体。

### 主要结果
- **可靠性提升**：几何感知时空推理 + 状态感知执行，提升了不同物体类别上的操作可靠性。
- **可扩展性**：几何感知重建 + 高质量渲染/仿真，为机器人操作行为评估提供了可扩展方案。

### 实验结果图

![[success_rate_page1.png|600]]

> 图4：不同任务上的成功率对比，展示 Robot-GST 的可靠性提升。

![[real-sim_page1.png|600]]

> 图5：真实-仿真（real-to-sim）对应关系展示。

![[real-sim-cube-sloth_page1.png|600]]

> 图6：立方体与软体物体的真实-仿真对比，展示重建保真度。

![[replan_page1.png|600]]

> 图7：重规划（replan）机制示意，展示执行前评估如何触发重新规划。

## 深度分析

### 研究价值评估

#### 理论贡献
- **"先仿真、后执行"的可靠性范式**：把结果预测与执行评估显式纳入机器人操作流程。
- **Gaussian-SAM 环境**：3DGS + SAM3D 的组合，为 real-to-sim 验证提供高保真、可语义分割的环境。
- **高斯感知最终状态估计**：用几何采样显式预测动作后果，弥补 VLM 端到端的黑盒缺陷。

#### 实际应用价值
- **真实机器人部署**：执行前过滤不可行行为，降低长程任务失败率。
- **跨物体类别泛化**：刚性/软体/可变形物体均验证，适用范围广。

### 方法优势详解
- **可靠性**：显式的结果预测 + 执行前评估，减少误差累积。
- **可扩展性**：3DGS 重建 + 渲染/仿真，无需复杂物理引擎即可验证。
- **语义感知**：SAM3D 提供语义信息，支持几何与语义的联合推理。

### 局限性分析
- **重建质量依赖 RGB-D**：低质量观测会影响 Gaussian-SAM 环境保真度。
- **仿真-现实差距**：虽然高保真，但仍可能存在 real-to-sim 与 sim-to-real 的双向差距。
- **计算开销**：3DGS 重建 + 逐候选仿真评估，实时性可能受限。

## 技术路线定位

### 所属技术路线
本文属于 **机器人操作 + 3D 重建（3DGS）+ 视觉语言模型** 的交叉方向，核心特点是"几何感知的仿真评估先于执行"。

### 本文在技术路线中的位置
- **承上**：继承 3D Gaussian Splatting 的场景重建与 VLM 的端到端决策。
- **启下**：为"可验证的机器人操作"提供了可扩展环境构建范式，可与更强 VLA 策略结合。

## 我的综合评价

### 价值评分

#### 总体评分
**8.4/10** — 工程完成度高、理念清晰（先仿真后执行）、跨物体类别验证扎实，但计算开销与 sim2real 差距需进一步缓解。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | Gaussian-SAM 环境 + 执行前评估组合新颖 |
| 技术质量 | 9/10 | 重建、规划、评估各环节完整 |
| 实验充分性 | 8/10 | 三类物体多任务验证，含消融 |
| 写作质量 | 8/10 | 动机清晰，配图丰富 |
| 实用性 | 8/10 | 真实部署导向，可靠性提升明确 |

### 重点关注
- **高斯感知最终状态估计**的实现细节：几何采样如何估计最终状态。
- **执行前评估的过滤标准**：如何判定一个候选动作序列"不可行"。

## 相关论文

### 直接相关
- [[20_Research/Papers/强化学习与智能体/Achieve_What_You_Imagined_Learning_to_Align_Actions_with_Visual_Plans|Achieve What You Imagined]] - 同一团队（Sichao Liu）的视觉-动作对齐工作

### 背景相关
- [[20_Research/Papers/大语言模型/Diffusion_Reward_Models|Diffusion Reward Models]] - 生成式/分布式建模的交叉思想

## 外部资源
- 项目页：https://robot-gst.github.io

> [!tip] 关键启示
> 端到端黑盒的可靠性瓶颈，可以用"执行前仿真评估"来兜底——先在高保真重建环境中验证动作，再落地执行。

> [!success] 推荐指数
> ⭐⭐⭐⭐☆ 推荐：机器人操作 / 3DGS / real-to-sim 方向读者值得关注，工程完成度高。
