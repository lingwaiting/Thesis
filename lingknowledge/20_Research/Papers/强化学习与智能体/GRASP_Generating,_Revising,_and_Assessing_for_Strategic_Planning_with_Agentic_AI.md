---
date: "2026-09-26"
paper_id: "arXiv:2609.30147"
title: "GRASP: Generating, Revising, and Assessing for Strategic Planning with Agentic AI"
authors: "Arunabh Srivastava, Mohammad A. Khojastepour, Srimat Chakradhar, Sennur Ulukus"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - Strategic-Planning
  - Agentic-AI
  - LLM-Planning
  - Multi-Stage-Planning
  - Task-Decomposition
quality_score: "8.5/10"
created: "2026-09-26"
updated: "2026-09-26"
status: analyzed
---

# GRASP: Generating, Revising, and Assessing for Strategic Planning with Agentic AI

## 核心信息
- **论文ID**：arXiv:2609.30147
- **作者**：Arunabh Srivastava, Mohammad A. Khojastepour, Srimat Chakradhar, Sennur Ulukus
- **机构**：--
- **发布时间**：2026-09-24
- **会议/期刊**：arXiv 预印本（cs.AI, cs.CL, cs.LG, cs.MA）
- **链接**：[arXiv](http://arxiv.org/abs/2609.30147v1) | [PDF](https://arxiv.org/pdf/2609.30147v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Large Language Models (LLMs) typically exhibit a performance profile where reliability degrades as task complexity increases. We address the challenge of generating high-quality natural language executable plans for complex tasks by introducing GRASP, a strategy-aware, multi-stage planning framework. GRASP decouples the planning pipeline across specialized, context-isolated modules: it pre-compiles global macro-guidelines (GenPlan), explores alternative localized strategies within isolated context windows (RevPlan), and independently evaluates trajectories using a multi-criteria discriminator (VerPlan). Empirical evaluations show that GRASP consistently establishes a new state-of-the-art frontier across diverse datasets, yielding substantial accuracy gains over direct LLM planners on Natural Plan Calendar Scheduling (~12.4%↑), ZebraLogic (~30.8%↑), and SciBench Math. Crucially, under multi-task scaling—where standard planners suffer immediate performance collapse—GRASP completely flattens the multi-task degradation penalty. In interleaved dual-task environments, GRASP achieves an absolute accuracy gain of up to 16.7% over direct LLM planners. Furthermore, by isolating context and enforcing strict macro-regularization, GRASP outperforms frontier reasoning models (such as GPT-5-mini) by a margin of 14.5%.

### 中文翻译
大语言模型（LLM）通常表现出一种性能特征：随着任务复杂度的增加，可靠性会下降。本文通过提出 GRASP——一种策略感知的多阶段规划框架——来解决为复杂任务生成高质量自然语言可执行计划这一挑战。GRASP 将规划流水线解耦到专门的、上下文隔离的模块中：它预编译全局宏观准则（GenPlan），在隔离的上下文窗口内探索替代的局部策略（RevPlan），并使用多准则判别器独立评估轨迹（VerPlan）。实证评估表明，GRASP 在多个数据集上持续建立了新的最前沿，在 Natural Plan Calendar Scheduling（约 12.4%↑）、ZebraLogic（约 30.8%↑）和 SciBench Math 上相对直接 LLM 规划器取得了显著准确率提升。关键的是，在多任务扩展下——标准规划器会立即出现性能崩溃——GRASP 完全抹平了多任务退化惩罚。在交错双任务环境中，GRASP 相对直接 LLM 规划器取得了高达 16.7% 的绝对准确率提升。此外，通过隔离上下文并强制严格的宏观正则化，GRASP 以 14.5% 的幅度超越了前沿推理模型（如 GPT-5-mini）。

### 核心要点提炼
- **研究背景**：LLM 在简单任务上可靠，但在复杂/多任务规划场景下可靠性和准确率急剧下降
- **研究动机**：现有 LLM 规划器在任务复杂度上升时可靠性退化，且多任务扩展时性能崩溃
- **核心方法**：三阶段解耦框架 GenPlan（宏观准则）→ RevPlan（局部策略探索）→ VerPlan（多准则判别评估）
- **主要结果**：在 Calendar Scheduling、ZebraLogic、SciBench 等多个基准上 SOTA，多任务退化被抹平，超越 GPT-5-mini
- **研究意义**：证明"上下文隔离 + 宏观正则化"是缓解 LLM 复杂规划退化的有效范式

## 研究背景与动机

### 领域现状
LLM 已成为任务规划（planning）的重要工具，可将自然语言指令分解为可执行步骤。但大量工作表明，LLM 规划能力随任务步骤数、约束数量、任务多样性增加而显著退化，尤其在需要同时维护多条约束或交叉多个子目标时表现脆弱。

### 现有方法的局限性
- **单窗口规划瓶颈**：直接让单个 LLM 在单一上下文窗口内生成完整计划，会因上下文过长、约束过多而失真
- **多任务退化**：当把多个任务交织进同一上下文时，标准规划器性能立即崩溃，存在严重的多任务退化惩罚
- **缺乏策略级抽象**：直接生成步骤级计划缺少"全局战略"与"局部战术"的分离，导致局部决策偏离全局目标
- **评估与生成耦合**：生成与评估在同一过程中进行，难以独立客观地判断计划质量

### 研究动机
能否通过"生成—修订—评估"三阶段解耦、并让每个阶段在隔离上下文中独立运作，来系统性地缓解 LLM 在复杂与多任务规划中的退化？

## 研究问题

### 核心研究问题
如何在复杂、多任务、交织场景下，让 LLM 生成高质量、可执行、且不随复杂度退化而崩溃的战略计划？

## 方法概述

### 核心思想
将规划流水线解耦为三个上下文隔离的专用模块——先由 **GenPlan** 生成全局宏观准则，再由 **RevPlan** 在独立窗口内探索局部策略，最后由 **VerPlan** 用多准则判别器独立评估候选轨迹——从而避免单窗口规划中的上下文污染与多任务退化。

### 方法框架

#### 整体架构

![[GRASP_Top_Level_Block_Diagram_page1.png|800]]

> 图1：GRASP 顶层模块图，展示 GenPlan → RevPlan → VerPlan 三阶段解耦流水线

#### 各模块详细说明

**模块1：GenPlan（宏观准则预编译）**
- **功能**：为复杂任务预编译全局宏观准则（macro-guidelines）
- **输入**：复杂任务的自然语言描述
- **输出**：全局战略级准则，作为后续局部规划的约束与指导
- **处理流程**：
  1. 分析任务的全局目标与顶层约束
  2. 生成可复用的宏观准则/策略模板
  3. 将宏观准则注入后续模块作为正则化信号
- **关键技术**：宏观正则化（macro-regularization），将全局约束与局部决策解耦

**模块2：RevPlan（局部策略探索）**
- **功能**：在隔离的上下文窗口内探索替代的局部策略
- **输入**：GenPlan 输出的宏观准则 + 当前任务子问题
- **输出**：针对局部子问题的候选策略
- **处理流程**：
  1. 在独立、隔离的上下文窗口中聚焦局部子任务
  2. 生成多种替代策略
  3. 结合宏观准则筛选与修订策略
- **关键技术**：上下文隔离（context isolation）窗口，防止局部探索污染全局

**模块3：VerPlan（多准则判别评估）**
- **功能**：用多准则判别器独立评估候选轨迹的质量
- **输入**：候选规划轨迹
- **输出**：轨迹质量的判别评分
- **处理流程**：
  1. 定义多维评估准则（正确性、可行性、效率等）
  2. 独立地对每条候选轨迹打分
  3. 依据判别结果选择最优轨迹
- **关键技术**：多准则判别器（multi-criteria discriminator），将评估与生成解耦

### 方法架构图
![[GRASP_Top_Level_Block_Diagram_page1.png|800]]

## 实验结果

### 实验目标
验证 GRASP 在复杂规划任务上的准确率提升，以及在多任务扩展下消除退化惩罚的能力。

### 数据集与基线
- **数据集**：Natural Plan Calendar Scheduling、ZebraLogic、SciBench Math
- **基线**：直接 LLM 规划器、前沿推理模型（如 GPT-5-mini）

### 主要结果

| 任务 | 相对直接 LLM 规划器提升 |
|------|--------------------------|
| Natural Plan Calendar Scheduling | ~12.4% ↑ |
| ZebraLogic | ~30.8% ↑ |
| 交错双任务 | 绝对准确率 +16.7% |
| 对比 GPT-5-mini | +14.5% |

### 关键发现
- **多任务退化被抹平**：在多任务扩展下，标准规划器性能立即崩溃，而 GRASP 完全抹平了多任务退化惩罚
- **超越前沿推理模型**：通过上下文隔离 + 宏观正则化，以 14.5% 的幅度超越 GPT-5-mini
- **隔离上下文的收益**：GenPlan/RevPlan/VerPlan 的模块化隔离是性能稳定的关键

## 深度分析

### 研究价值评估

#### 理论贡献
- **规划流水线解耦范式**：将"生成—修订—评估"三阶段显式分离，为 LLM 规划提供可复用的架构模板
- **宏观正则化思想**：证明全局宏观准则作为正则化信号能有效缓解局部决策偏离
- **多任务退化的实证刻画**：清晰展示了标准规划器的多任务性能崩溃现象，并给出有效解法

#### 实际应用价值
- **复杂任务自动化**：适用于日程调度、逻辑推理、科学计算等需要多步、多约束规划的场景
- **Agent 系统设计**：为构建可靠的多步智能体规划器提供模块化蓝图
- **可解释性**：三阶段分离使规划过程更透明、可审计

### 方法优势详解
- **优势1：上下文隔离缓解污染**——每个模块独立窗口运作，避免长上下文与多约束干扰
- **优势2：评估与生成解耦**——VerPlan 独立判别，提高计划质量的可信度
- **优势3：多任务鲁棒性**——宏观正则化使其在多任务交织下不崩溃

### 局限性分析
- **局限1：模块化开销**——三阶段流水线引入额外推理开销与延迟
- **局限2：宏观准则质量依赖**——GenPlan 生成的宏观准则质量直接决定下游性能上限
- **局限3：基准覆盖面**——主要验证于规划/推理类基准，未覆盖开放域真实任务

## 技术路线定位

本文属于 **LLM 复杂任务规划** 技术路线，核心思路是"分而治之 + 评估解耦"：
- **承上**：继承 LLM 任务分解（task decomposition）与多步规划（multi-step planning）的积累
- **启下**：为后续模块化、可审计、多任务鲁棒的智能体规划架构提供模板
- **关键节点**：将"策略级抽象（宏观准则）"与"步骤级执行（局部策略）"显式分离，是缓解规划退化的关键一步

## 相关论文
- 直接相关：[[20_Research/Papers/强化学习与智能体/Chain-of-Agents_End-to-End_Agent_Foundation_Models_via_Multi-Agent_Distillation_and_Agentic_RL|Chain-of-Agents]] - 多智能体端到端规划
- 背景相关：LLM Planning / Task Decomposition 系列工作

## 外部资源
- arXiv: http://arxiv.org/abs/2609.30147

> [!tip] 关键启示
> LLM 复杂规划退化的根源是"单窗口 + 生成评估耦合"；用"上下文隔离 + 宏观正则化 + 独立判别"三阶段解耦可系统性地抹平多任务退化。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读：为 LLM 复杂/多任务规划提供了一个清晰、可复用的三阶段解耦框架，实验扎实、提升显著。
