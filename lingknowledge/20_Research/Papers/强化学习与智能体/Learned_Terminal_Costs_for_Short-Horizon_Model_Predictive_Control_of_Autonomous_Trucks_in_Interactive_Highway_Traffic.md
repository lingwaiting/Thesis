---
date: "2026-10-31"
paper_id: "arXiv:2610.40257"
title: "Learned Terminal Costs for Short-Horizon Model Predictive Control of Autonomous Trucks in Interactive Highway Traffic"
authors: "Hooman Hamze, Benjamin Houshmand, Deepthi Pathare, Erik Börve, Morteza Haghir Chehreghani, Nikolce Murgovski"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 模型预测控制
  - 自动驾驶
  - 混合控制
quality_score: "7.5/10"
created: "2026-10-31"
updated: "2026-10-31"
status: analyzed
---

# Learned Terminal Costs for Short-Horizon Model Predictive Control of Autonomous Trucks

## 核心信息
- **论文ID**：arXiv:2610.40257
- **作者**：Hooman Hamze, Benjamin Houshmand, Deepthi Pathare, Erik Börve, Morteza Haghir Chehreghani, Nikolce Murgovski
- **机构**：查尔姆斯理工大学（Chalmers University of Technology）、哥德堡大学（University of Gothenburg）、沃尔沃集团（Volvo Group）
- **发布时间**：2026-10-29
- **类别**：cs.LG, cs.AI, cs.RO, eess.SY
- **链接**：[arXiv](https://arxiv.org/abs/2610.40257) | [PDF](https://arxiv.org/pdf/2610.40257)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Autonomous highway driving for heavy-duty trucks is a challenging control problem. The greater mass, dimensions and intricate dynamics increase the requirements for cooperation from surrounding traffic in critical maneuvers, requiring a careful balance between safety constraints and efficient long-horizon decision making. Model Predictive Control (MPC) provides rigorous constraint satisfaction but relies on terminal cost which can limit its effectiveness at short, computationally affordable horizons. Reinforcement Learning (RL) captures long-term driving behavior but offers no constraint guarantees and tends toward overly conservative policies when trained under sparse collision penalties. In this paper, we propose a hybrid MPC and RL framework for highway driving in autonomous trucks, where a finite-horizon MPC, solved via dynamic programming, uses a pre-trained RL model for terminal cost estimation. We evaluate the framework in a simulation environment with reactive surrounding traffic and compare it against pure MPC with zero terminal cost, rollout with terminal cost estimation using a base policy, and pure RL. Results show that the hybrid approach achieves zero collisions with the lowest cost of any configuration.

### 中文翻译
重型卡车的自主高速公路驾驶是一个极具挑战的控制问题。更大的质量、尺寸与复杂的动力学特性，在关键操纵中提高了对周围交通协同配合的要求，需要在安全约束与高效的长时域决策之间取得精细平衡。模型预测控制（MPC）能提供严格的约束满足，但依赖于终端代价（terminal cost），这在计算可负担的短时域下会限制其效果。强化学习（RL）能捕捉长期驾驶行为，但无法提供约束保证，且在稀疏碰撞惩罚下训练时倾向于过于保守的策略。本文提出一种面向自主卡车高速公路驾驶的 MPC 与 RL 混合框架：一个通过动态规划求解的有限时域 MPC，使用预训练的 RL 模型来做终端代价估计。作者在带有反应式周围交通的仿真环境中评估该框架，并与"零终端代价纯 MPC"、"用基策略估计终端代价的 rollout"以及"纯 RL"进行对比。结果表明，混合方法在实现零碰撞的同时，取得所有配置中最低的代价。

### 核心要点提炼
1. **问题**：MPC 短时域下终端代价难以准确估计，RL 无约束保证且稀疏奖励下过于保守。
2. **方法**：用预训练 RL 价值函数作为 MPC 的终端代价，实现"约束由 MPC 保证、长期价值由 RL 提供"的混合。
3. **贡献**：验证 RL 学到的终端代价能显著优于零终端代价与 rollout 估计。

## 方法概述

### 方法架构
![[framework_page4.png|800]]

框架将自动驾驶卡车决策分解为两层：
- **MPC 层（约束保证）**：在有限预测时域内，用动态规划求解带约束的轨迹优化问题，严格满足碰撞避免、物理约束等安全要求。
- **RL 终端代价（长期价值）**：预训练一个 RL 策略/价值网络，用其价值函数估计 MPC 时域末端的"后续期望代价"，从而弥补短时域的短视性。

### 关键创新
1. **学习终端代价（Learned Terminal Cost）**：把 RL 学到的长期价值注入 MPC，而非手工设计终端代价（如零代价或基于基策略的 rollout）。
2. **互补式混合**：MPC 提供硬约束与最优性，RL 提供对时域外长期结果的估计，二者解耦、可独立训练。
3. **面向重型卡车**：针对大质量、大尺寸、复杂动力学下的交互式高速公路场景设计。

## 实验结果

- **评测环境**：带反应式周围交通的仿真环境（interactive highway traffic）。
- **对比基线**：零终端代价纯 MPC、基于基策略 rollout 的终端代价估计、纯 RL。
- **关键结果**：混合方法实现 **零碰撞** 且代价最低（lowest cost of any configuration）。

## 深度分析

### 优势
- 将 RL 与 MPC 的优点结合，既保留了 MPC 的约束满足能力，又弥补了其短时域的短视缺陷。
- 终端代价从数据中学习，避免了手工调参和 rollout 的高计算开销。

### 局限与开放问题
- 实验限于仿真环境，真实卡车的 sim-to-real 迁移仍需验证。
- RL 终端代价的泛化性（不同交通密度、不同道路拓扑）尚未充分讨论。
- 未深入比较不同 RL 价值估计器（如分布价值、离线 RL）对终端代价质量的影响。

### 与相关工作的关系
- 相比"MPC + 零终端代价"：学习到的终端代价能编码长期驾驶行为，显著降低总代价。
- 相比"纯 RL"：弥补了 RL 无约束保证、稀疏奖励下保守的问题。
