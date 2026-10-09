---
date: "2026-10-09"
paper_id: "2610.10942"
title: "StoreBench: A Live-Commerce Environment for Evaluating and Training Autonomous Operator Agents"
authors: "Daksh Raghuvanshi, Ved Vedere, Yifan Wang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - 智能体评测
  - Agent
  - 强化学习
quality_score: "9.8/10"
related_papers: []
created: "2026-10-09"
updated: "2026-10-09"
status: analyzed
---

# StoreBench: A Live-Commerce Environment for Evaluating and Training Autonomous Operator Agents

## 核心信息
- **论文ID**：2610.10942
- **作者**：Daksh Raghuvanshi, Ved Vedere, Yifan Wang
- **机构**：--
- **发布时间**：2026-10-07
- **分类**：cs.AI / cs.CL / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.10942) | [PDF](https://arxiv.org/pdf/2610.10942)

## 研究问题
强化学习环境已成为提升 LLM 后训练能力的核心杠杆，但现有 agentic 基准大多是**静态**的：世界只在智能体行动时才变化、奖励是终局一次性判定、及格线人为设定。**核心问题**：如何构建一个**动态、贴近真实运营**的环境，来评估和训练具备长程规划与经济判断能力的自主运营智能体？

## 方法概述

### 核心方法

1. **StoreBench 直播电商环境**
   - 智能体在一套生产级电商后端上运营一家中型在线服装店
   - 测试不确定环境下的长程规划与经济判断能力
   - 客户全天候下单、供应商会重新定价并可能违约、市场冲击在部分或完全无预警的情况下到来

2. **动作空间与预算设计**
   - 智能体通过人类运营者使用的同款 **29 个商户工具** 行动
   - **窗口化操作预算**：仿真时间成为所采取动作的函数，从而**模型延迟不会影响仿真时间**

3. **鲁棒评测设计**
   - 及格阈值基于脚本化锚定策略（anchor policy）校准
   - 奖励针对一系列 reward hacking 手段做了加固
   - 给定动作序列，每个 episode 可完全一致地复现

### 方法架构

![[2610.10942_page1.png|600]]

### 关键创新

1. **动态、生产级环境** - 有别于静态基准，环境持续演化、供应商与市场冲击不可控，更贴近真实运营
2. **消除延迟偏差** - 窗口化预算让"模型快慢"与"仿真时间"解耦，评测更公平
3. **反 reward hacking 与可复现性** - 奖励加固 + 确定性 replay，提升基准可信度

## 实验结果

### 评测规模
- 7 个前沿 LLM，在 11 个场景（30-45 天）与一个完整仿真年、3 个世界种子、匹配推理预算下评测

### 主要结果
- **无模型达到脚本化 smart-triage 策略的平均水平**：最佳模型 DeepSeek-V4-Pro 仅通过 49% 的 task-seed 单元，而启发式策略达 97%
- 人类专家（使用相同工具与预算）综合得分 0.708，略高于所有模型的最高分 0.700
- 在完整仿真年（Claude Code harness）下，多数模型性能显著提升
- **GRPO 后训练**：Qwen3.5-27B 仅在 5 个不重叠任务上训练，就将留出评估任务的综合得分从 0.136 提升到 0.373
- 已开源 5 个训练集任务、10 条示例轨迹与评分/验证工具；完整环境与评估集被保留以防基准污染

## 深度分析

**价值**：StoreBench 代表 agentic 评测从"静态问答/单步工具调用"向"动态、长程、经济决策"的升级。其精心设计的窗口化预算、反 reward hacking、确定性复现，直击当前 agentic 基准的三大痛点（延迟偏差、奖励漏洞、不可复现）。GRPO 后训练的大幅提升（0.136→0.373）也实证了该环境作为 RL 训练场的价值。

**局限与风险**：
- 完整环境被保留以防污染，意味着社区难以完全复现，评测生态依赖作者持续维护
- 经济判断类任务的主观性较强，"综合得分"的聚合方式需要更多透明度
- 单店场景（服装零售）是否代表更广泛的"运营智能体"能力仍待验证

## 相关论文对比
- 与 [[On_the_Clock_Towards_Punctual_and_Productive_Time-Budgeted_AI_Agents|On the Clock]] 同属 agentic 评测/训练研究，但 StoreBench 关注动态经济环境，On the Clock 关注时间预算约束
- 与 [[Decoupling_Exploration_from_Optimization_in_RLVR|Decoupling Exploration from Optimization in RLVR]] 同涉 RL 后训练，但前者关注环境设计，后者关注探索-优化解耦
