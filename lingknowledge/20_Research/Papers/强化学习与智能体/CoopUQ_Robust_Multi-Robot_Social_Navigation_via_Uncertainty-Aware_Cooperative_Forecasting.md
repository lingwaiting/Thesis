---
date: "2026-10-21"
paper_id: "arXiv:2610.26852"
title: "CoopUQ: Robust Multi-Robot Social Navigation via Uncertainty-Aware Cooperative Forecasting"
authors: "Zehao Wang, Jianpeng Yao, Yuping Wang, Yiping Li, Jiaqi Li, Zhixu Li, Kanghoon Lee, Jinfeng Wang, Cong Liu, Zhaowei Tan, Jinkyoo Park, Jiachen Li"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - 多智能体
  - Multi-Robot
  - Social-Navigation
  - Multi-Agent-RL
  - Uncertainty-Aware
quality_score: "7.9/10"
related_papers: []
created: "2026-10-21"
updated: "2026-10-21"
status: analyzed
---

# CoopUQ: Robust Multi-Robot Social Navigation via Uncertainty-Aware Cooperative Forecasting

## 核心信息
- **论文ID**：arXiv:2610.26852
- **作者**：Zehao Wang, Jianpeng Yao, Yuping Wang, Yiping Li, Jiaqi Li, Zhixu Li, Kanghoon Lee, Jinfeng Wang, Cong Liu, Zhaowei Tan, Jinkyoo Park, Jiachen Li
- **机构**：--
- **发布时间**：2026-10-19
- **会议/期刊**：arXiv（cs.RO / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.26852) | [PDF](https://arxiv.org/pdf/2610.26852)
- **引用**：--

## 摘要翻译

### 英文摘要
Reinforcement learning enables mobile robots to make fast and scalable navigation decisions in constrained human-shared environments. However, directly extending single-robot approaches to multi-robot settings often fails to ensure safety and efficiency, especially under occlusions and out-of-distribution (OOD) scenarios, where individual robots may overlook potential risks or make inaccurate predictions. Leveraging cooperation effectively for both prediction and navigation remains a major challenge. We propose CoopUQ, a multi-robot social navigation framework that bridges uncertainty-aware cooperative occupancy forecasting and robust decision making. CoopUQ predicts future occupancy grid maps through cross-robot spatiotemporal feature fusion and further leverages forecasts and their uncertainty to guide policy via constrained multi-agent reinforcement learning, where the cost penalizes navigation through regions predicted to be occupied or highly uncertain. This design enables the policy to adapt its risk sensitivity under distribution shifts. Experimental results across multiple Unity3D-based simulation environments with both in-distribution and OOD settings, including diverse layouts, pedestrian densities, and rushing behavior, demonstrate improved prediction performance and safety over representative baselines. Real-robot experiments further demonstrate its robustness across various scenes. Our code and videos are available at https://coopuq.github.io/.

### 中文翻译
强化学习让移动机器人能够在受限的、与人类共享的环境中做出快速且可扩展的导航决策。然而，把单机器人方法直接扩展到多机器人场景往往无法保证安全与效率，尤其是在遮挡与分布外（OOD）场景下——此时单个机器人可能忽略潜在风险或做出错误预测。如何在预测与导航两个层面都有效利用"合作"，仍是一大挑战。作者提出 CoopUQ，一个多机器人社交导航框架，把"不确定性感知的协作占据预测"与"鲁棒决策"连接起来。CoopUQ 通过跨机器人的时空特征融合来预测未来占据栅格图，并进一步利用预测及其不确定性，通过"带约束的多智能体强化学习"来引导策略——其中代价函数惩罚穿越"被预测为占据或高度不确定"区域的导航行为。这一设计使策略能够在分布偏移下自适应调节风险敏感度。在多个基于 Unity3D 的仿真环境中（含分布内与 OOD 设置，覆盖多样布局、行人密度与突发行为）的实验表明，其预测性能与安全性优于代表性基线。真实机器人实验进一步验证了其在多种场景下的鲁棒性。代码与视频见 https://coopuq.github.io/。

### 核心要点提炼
- **研究背景**：多机器人在人群共享空间中的社交导航需要安全、高效、可扩展的决策。
- **研究动机**：单机器人方法直接扩展到多机器人时，在遮挡与 OOD 场景下易漏风险/预测不准，而"合作"如何在预测与导航两层被有效利用仍待解决。
- **核心方法**：CoopUQ = 不确定性感知的协作占据预测（跨机器人时空特征融合）+ 带约束的多智能体 RL（代价惩罚占据/高不确定区域）。
- **主要结果**：在 Unity3D 仿真（分布内 + OOD）与真实机器人上均优于代表性基线，预测性能与安全性双提升。
- **研究意义**：把"预测不确定性"显式引入多机器人社交导航的决策，提供分布偏移下自适应风险敏感度的机制。

## 研究背景与动机

### 领域现状
移动机器人在医院、机场、仓储等人类共享环境中的自主导航，是服务机器人落地的关键能力。社交导航（social navigation）要求机器人在避开静态障碍的同时，理解并礼让行人等动态障碍。强化学习因其能直接从交互中学习策略、且推理速度快，成为主流方法之一。

### 现有方法的局限性
- **单→多的扩展失效**：单机器人策略直接复制到多机器人时，机器人之间缺乏协调，可能互相阻塞或争抢路径；
- **遮挡与 OOD 脆弱**：在遮挡下，单个机器人看不到被挡住的动态障碍，容易漏判风险；在分布外（异常行人密度、突发行为）下，预测模型会失准；
- **预测与决策脱节**：现有方法往往把"预测未来占据"与"导航决策"分开处理，预测的不确定性没有进入决策的代价函数。

### 研究动机
作者提出"合作"应同时作用于预测与导航两层：预测层用跨机器人时空融合提升精度并量化不确定性；决策层把预测及其不确定性纳入约束式多智能体 RL 的代价，使策略在分布偏移下能自适应地"更谨慎"。

## 研究问题

### 核心研究问题
如何设计一个多机器人社交导航框架，在遮挡与分布外场景下，同时利用"协作预测的不确定性"与"带约束的多智能体强化学习"，实现安全、高效、鲁棒的导航决策？

## 方法概述

### 核心思想
CoopUQ 的关键是把"预测的不确定性"变成"决策的代价信号"：先通过跨机器人的时空特征融合预测未来占据图并估计其不确定性，再把"导航穿越被预测为占据或高不确定的区域"写入代价函数，通过带约束的多智能体 RL 优化，让策略自动学会在不确定处"绕行/减速"。

### 方法框架

#### 整体架构
CoopUQ 由两个协同模块构成：

1. **不确定性感知的协作占据预测（Cooperative Occupancy Forecasting）**：跨机器人时空特征融合 → 未来占据栅格图 + 不确定性估计；
2. **带约束的多智能体 RL 决策（Constrained MARL）**：以预测与不确定性为代价，优化导航策略。

![[2610.26852_fig1.png|600]]

> 图1：CoopUQ 框架示意——协作占据预测提供未来占据图与不确定性，带约束 MARL 据此引导多机器人导航。

#### 各模块详细说明

**模块1：协作占据预测**
- **功能**：融合多机器人观测的时空特征，预测未来的占据栅格图并量化不确定性
- **输入**：各机器人的感知观测
- **输出**：未来占据图 + 逐区域不确定性
- **关键技术**：跨机器人时空特征融合（cross-robot spatiotemporal feature fusion）

**模块2：带约束 MARL 决策**
- **功能**：利用预测与不确定性引导导航策略
- **输入**：占据预测、不确定性、机器人状态
- **输出**：导航动作
- **关键技术**：约束式多智能体强化学习，代价惩罚穿越"被预测占据或高不确定"区域

## 实验结果

### 实验目标
验证 CoopUQ 在分布内与分布外场景下的预测性能、安全性与鲁棒性。

### 实验设置
- **环境**：多个 Unity3D 仿真环境，覆盖多样布局、行人密度、突发（rushing）行为
- **设置**：分布内（in-distribution）与分布外（OOD）
- **验证**：仿真 + 真实机器人实验

### 主要结果
- 预测性能与安全性均**优于代表性基线**；
- OOD 设置下鲁棒性显著，真实机器人实验跨场景验证鲁棒性；
- 代码与视频开源（https://coopuq.github.io/）。

## 深度分析

### 研究价值评估

#### 理论贡献
- 把"预测不确定性"显式接入多智能体导航的代价函数，提供分布偏移下自适应风险敏感度的机制；
- 将"协作"从单层扩展到"预测 + 决策"双层。

#### 实际应用价值
- 对仓储多机器人、服务机器人编队、人群密集环境的导航有直接价值；
- 真实机器人验证增强了方法的落地可信度。

### 方法优势详解
- **OOD 鲁棒**：不确定性驱动的代价使策略在分布偏移下自动变谨慎；
- **双层协作**：预测与决策两层都利用跨机器人信息；
- **可落地**：Unity3D 仿真 + 真实机器人双重验证，代码开源。

### 局限性分析
- 摘要未给绝对量化指标（如安全率、成功率的具体数值），需读正文确认；
- 多机器人通信/融合的带宽与延迟开销未在摘要中说明；
- 真实机器人实验的场景规模与数量需进一步确认。

### 适用性与场景分析
- **适用**：多机器人在人群共享空间中的导航（仓储、服务、交通）；
- **不适用**：无动态障碍/无协作需求的单机器人导航，可能过度设计。

## 技术路线定位

本文属于 **多智能体强化学习 + 机器人社交导航（Multi-Agent RL for Social Navigation）** 技术路线。相比单机器人社交导航，本文把协作扩展到预测与决策双层，并引入不确定性感知。

```
单机器人社交导航 → 多机器人协作导航 → 不确定性感知 + 双层协作（CoopUQ） → 未来方向
```

### 具体子方向
不确定性感知的协作预测与约束式多智能体决策。

## 未来工作建议

1. **量化指标公开对比**：补充具体安全率/成功率数值与统计显著性；
2. **通信受限场景**：研究有限通信带宽下的协作预测鲁棒性；
3. **更大规模编队**：扩展到大数量机器人与高密度人群。

## 我的综合评价

### 总体评分
**7.9/10** —— 问题明确、双层协作 + 不确定性感知的思路合理，仿真与真实机器人双重验证，但摘要缺绝对量化指标。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 预测不确定性进入决策代价，双层协作有巧思 |
| 技术质量 | 8/10 | 时空融合 + 约束 MARL 设计清晰 |
| 实验充分性 | 7/10 | 仿真 + 真机，但摘要缺绝对数值 |
| 写作质量 | 7/10 | 结构清晰，但指标呈现不足 |
| 实用性 | 8/10 | 真实机器人验证 + 开源，落地价值高 |

## 相关论文

### 直接相关
- 多机器人协同导航（如集中式/分布式 MARL 导航方法）
- 不确定性感知的轨迹预测

### 背景相关
- 社交导航（Social Force、ORCA 等传统方法）
- 占用栅格预测（occupancy forecasting）

## 外部资源
- [项目主页](https://coopuq.github.io/)
- [arXiv 页面](https://arxiv.org/abs/2610.26852)

> [!tip] 关键启示
> 多机器人导航的鲁棒性，来自把"预测的不确定性"当作"决策的代价"——让策略学会在不确定处自动谨慎，而不是靠事后补救。

> [!warning] 注意事项
> - 摘要未给绝对量化指标，读正文需关注安全率/成功率
> - 多机器人通信带宽开销未说明
> - 真机场景规模需确认
