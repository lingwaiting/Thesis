---
date: "2026-10-15"
paper_id: "arXiv:2610.22316"
title: "Learning to Steer LLM Agents and Multi-Agent Systems"
authors: "Leyang Shen, Yaxin Gong, Yang Zhang, Chenxiao Fan, Jiakai Wang, Dong Wang, Yang Liu, Fuli Feng, Chun Kai Ling, Tat-Seng Chua"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - LLM-agent
  - multi-agent
  - goal-drift
  - steering
  - GRPO
  - reinforcement-learning
quality_score: "8.5/10"
created: "2026-10-17"
updated: "2026-10-17"
status: analyzed
---

# Learning to Steer LLM Agents and Multi-Agent Systems

## 核心信息
- **论文ID**：arXiv:2610.22316
- **作者**：Leyang Shen, Yaxin Gong, Yang Zhang, Chenxiao Fan, Jiakai Wang, Dong Wang, Yang Liu, Fuli Feng, Chun Kai Ling, Tat-Seng Chua
- **机构**：新加坡国立大学（NUS）、中国科学技术大学（USTC）、阿里巴巴 Qwen 事业部
- **发布时间**：2026-10-15
- **会议/期刊**：Preprint
- **链接**：[arXiv](http://arxiv.org/abs/2610.22316v1) | [PDF](https://arxiv.org/pdf/2610.22316v1)
- **分类**：cs.LG, cs.AI, cs.MA

## 摘要翻译

### 英文摘要
Large language model (LLM)-based agents and multi-agent systems increasingly tackle complex tasks through long-horizon tool use and inter-agent collaboration. However, these systems are prone to deviating from the overall goal as execution unfolds, a failure termed goal drift. We propose a steering agent that supervises execution on the fly and steers the system back on track when drift occurs. The steering agent observes the execution context, deciding when to intervene with a low-cost fast path and how to intervene with a high-cost slow path. To unlock its steering intelligence, we jointly train the two paths with reinforcement learning, combining a pairwise ranking objective with group-relative policy optimization (GRPO).

### 中文翻译
基于大语言模型（LLM）的智能体和多智能体系统越来越多地通过长程工具调用和智能体间协作来处理复杂任务。然而，这些系统在执行过程中容易偏离总体目标，浪费执行资源并导致不理想的结果，这种失败模式被称为**目标漂移（goal drift）**。本文提出一个**转向智能体（steering agent）**，在执行过程中实时监督，并在发生漂移时把系统拉回正轨。转向智能体观察执行上下文，通过低成本的**快路径（fast path）**决定何时干预，通过高成本的**慢路径（slow path）**决定如何干预。为了解锁其"转向智能"，本文用强化学习联合训练两条路径，将成对排序目标与组相对策略优化（GRPO）相结合。

### 核心要点提炼
- **研究问题**：LLM 智能体在多步执行中会发生目标漂移，规划（planning）无法保证执行不走偏，验证（verification）事后补救代价高昂且可能不可逆
- **核心方法**：外置 steering agent，冻结执行器（executor），快路径（单 token 判断 steering priority）+ 慢路径（CoT 生成 steering action），用 RL 联合训练
- **训练算法**：快路径用 pairwise ranking loss（Burges et al.），慢路径用 GRPO（将 unsteered baseline 纳入组内归一化）
- **主要结果**：7 个 QA benchmark 上 RL steering 平均 +6.66 分（zero-shot 仅 +1.84），平均每任务仅 0.93 次 steering action
- **泛化/迁移**：zero-shot 泛化到 Frames/GAIA/xBench-DeepSearch；迁移到 3 个未见执行器（+15.78/+12.01/+5.92）；多智能体场景 +5.34

## 研究背景与动机

### 领域现状
LLM 智能体（如 Claude Code、Codex）和多智能体系统越来越多地通过长程工具调用和智能体间协作处理复杂任务，覆盖信息检索、软件工程、科学发现等场景。

### 现有方法的局限性
- **规划（Planning）**：把目标分解为子目标指导执行，但"计划不能保证可靠执行"，执行仍可能偏离规划的路线
- **验证（Verification）**：在终止前检查目标是否达成，但这是事后补救，可能已花费大量资源，且后果不可逆
- **目标漂移的难点**：漂移往往由"局部看似合理"的动作逐渐累积而成，比明显的错误更难实时检测和纠正

### 研究动机
现有工作（如 SupervisorAgent）以检测为主，或只针对"易修复的问题"（错误、重复动作）。本文针对更难的**目标漂移**——它从局部合理的动作中逐步浮现——提出一个联合学习漂移检测与纠正的 steering agent。

## 研究问题

### 核心研究问题
1. **RQ1**：steering agent 能否提升任务性能？RL 能否增强其转向智能？
2. **RQ2**：学到的转向能力能否泛化到域外任务、迁移到未见执行器、支持可控的干预频率？
3. **RQ3**：steering 能否改进多智能体系统（目标漂移源于专业角色间的协作）？

## 方法概述

### 核心思想
不微调执行任务的 LLM（冻结 executor），而是训练一个**外部 steering agent** 实时监督执行，在漂移发生时把系统拉回总体目标。这一思想借鉴组织管理中的"直接监督"、自动化系统中的"监控控制"、以及人类认知中的"元认知监控"。

### 方法框架

#### 整体架构
```
执行上下文 ot = O(g, st)
        │
        ▼
   Fast Path（单 token 判断）── priority vθ(ot)
        │
   pass（不干预）/ 触发 slow path
        │
        ▼
   Slow Path（CoT 推理 → 生成 steering action）
```

#### 各模块详细说明

**转向接口（Steering Interface）**：定义观察空间 O（暴露目标 + 执行上下文）和动作空间 A_steer = {pass} ∪ A_int。将转向过程形式化为 POMDP，被监督的智能体系统充当环境。

**快路径（Fast Path）**：在单次 LLM 前向中判断是否需要干预，估计 steering priority vθ(ot)（衡量该状态下干预能多大程度改变最终结果）。通过读取 yes/no 两个 token 的概率 log-odds 计算，并用 soft threshold（Bernoulli 触发）决定是否进入慢路径。

**慢路径（Slow Path）**：被触发后进行 chain-of-thought 推理，生成 steering action（如 note、retry 等干预）。

**RL 训练管线**：
- 未转向的 rollout 初始化 state bank
- 每轮选择最多 k 个状态（高 priority 概率大，保留探索）
- 每个状态采样 G 个 steering action，executor 从每个状态续跑 N 次降方差
- **快路径**：pairwise ranking loss 学习按 steering priority 排序
- **慢路径**：GRPO，将 unsteered baseline bs 纳入组内归一化，优势 >0 才视为正改进

## 实验结果

### 主要结果（7 个 in-domain benchmark）

| 方法 | 平均 F1 | Δ 增益 | #Act. | Turns |
|------|---------|--------|-------|-------|
| No Intervention | 42.70 | — | — | 6.4 |
| Planning | 43.74 | +1.04 | 1.00 | 8.0 |
| Verification | 41.57 | −1.13 | 1.22 | 7.8 |
| Steering (zero-shot) | 44.54 | +1.84 | 1.11 | 6.5 |
| **Steering (w/ RL)** | **49.36** | **+6.66** | **0.93** | **6.2** |

### 关键发现
- zero-shot steering 在三种监督形式中增益最大，且几乎不增加 executor turns
- RL 提升三种监督形式，但 **steering 受益最多**（+1.84 → +6.66），且用最少的 steering action 和 turns
- 对比 filtered BC（离线），在线 RL 更优，验证了在线强化学习的必要性

### 泛化、迁移与可控性（RQ2）
- **域外泛化**：Frames、GAIA、xBench-DeepSearch 上 steering 均提升 Avg@4 和 Pass@4
- **迁移性**：同一 steering agent 监督 Llama-3.1-8B、Mistral-7B、Phi-4，分别 +15.78/+12.01/+5.92，证明转向智能与执行器解耦
- **可控性**：部署时调节 threshold 即可控制干预频率，无需重训；最高频率下增益达 +17.11 分

### 多智能体（RQ3）
在五角色多智能体系统（TacoMAS）中，zero-shot 监督反而退化，RL-trained steering agent 实现 +5.34 分增益，证明 steering 在多智能体场景有效但更难。

## 深度分析

### 研究价值评估

#### 理论贡献
- **目标漂移的形式化刻画**：明确区分单智能体（临时子目标压倒总体目标）与多智能体（角色目标压倒集体目标，如 verifier 过度拒绝）
- **外置监督范式**：冻结执行器、只训练监督者，与 end-to-end 多智能体 RL 形成互补，规避长程 rollout 昂贵和过拟合问题
- **快慢双路径设计**：把"何时干预"和"如何干预"分离，把干预频率变成部署期可调旋钮

#### 实际应用价值
- 对 Claude Code、Codex 等长程执行系统具有直接借鉴意义——在不改动执行模型的前提下提升可靠性
- 转向智能可跨模型族迁移，降低部署成本

### 方法优势与局限
**优势**：设计优雅（冻结 executor、低成本快路径）、实验全面（泛化/迁移/可控性/多智能体）、可控性强。

**局限**：实验聚焦信息检索类任务；steering action 空间相对有限；RL 训练仍需大量 rollout 采样；多智能体场景增益（+5.34）低于单智能体，说明粗粒度监督更困难。

![[2610.22316_page1.png|600]]
