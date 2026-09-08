---
date: "2026-09-08"
paper_id: "arXiv:2609.04865"
title: "CoSkill: Joint Reinforcement Learning of Reasoning and Meta-Skill Agents for Hierarchical Skill Evolution"
authors: "Jinyuan Feng, Dongmin Li, Yiqun Chen, Yang Gao, Xing Chen, Huimu Wang, Zhiqiang Pu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - 多智能体
  - 技能学习
  - LLM-Agent
  - 分层技能
  - 协同适应
quality_score: "8.0/10"
created: "2026-09-08"
updated: "2026-09-08"
status: analyzed
---

# CoSkill: Joint RL of Reasoning and Meta-Skill Agents

## 核心信息
- **论文ID**：arXiv:2609.04865
- **作者**：Jinyuan Feng, Dongmin Li, Yiqun Chen, Yang Gao, Xing Chen, Huimu Wang, Zhiqiang Pu
- **机构**：Institute of Automation, Chinese Academy of Sciences; Renmin University of China; ByteDance
- **发布时间**：2026-09-04
- **会议/期刊**：arXiv（cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2609.04865) | [PDF](https://arxiv.org/pdf/2609.04865)
- **代码**：https://github.com/jinyuan-cookie/CoSkill
- **类型**：多智能体强化学习 / LLM Agent 技能进化

## 摘要翻译

### 英文摘要
Skill libraries improve the sample efficiency of agentic reinforcement learning (RL) by enabling large language model (LLM) agents to reuse procedural knowledge. Yet existing paradigms exhibit structural shortcomings: they either decouple skill evolution from policy optimization or instantiate meta-skills as fixed workflows. Both treat skills as passive objects to be managed, limiting the flexible evolution of skills and their co-adaptation with the reasoning agent. To address the limitations, we propose CoSkill, a unified multi-agent RL framework that recasts the static meta-skill workflow as a learnable Meta-Skill Agent and jointly trains it with a Reasoning Agent over a hierarchical skill library. By modeling the Reasoning and Meta-Skill Agents as a cooperative team sharing a single backbone, CoSkill enables end-to-end co-adaptation: the Reasoning Agent conditions its actions on a retrieved task skill and step skills selected from its child set, while its task performance guides the Meta-Skill Agent in refining those step skills. Experiments on ALFWorld and WebShop show that CoSkill substantially outperforms prior skill-based and RL baselines, achieving success rates of 98.4% and 90.6%, respectively (+3.5 and +6.2 pp).

### 中文翻译
技能库（skill libraries）通过让大语言模型（LLM）智能体复用过程性知识，提升了智能体强化学习（RL）的采样效率。然而，现有范式存在结构性缺陷：它们要么把技能进化与策略优化解耦，要么把元技能（meta-skill）固化为固定工作流。二者都把技能当作"被管理的被动对象"，限制了技能的灵活进化及其与推理智能体的协同适应。为克服这些局限，本文提出 CoSkill——一个统一的多智能体 RL 框架，把静态的元技能工作流重构为一个**可学习的元技能智能体（Meta-Skill Agent）**，并让它与**推理智能体（Reasoning Agent）**在分层技能库上联合训练。通过把推理智能体与元技能智能体建模为共享单一 backbone 的合作团队，CoSkill 实现了端到端的协同适应：推理智能体基于检索到的任务技能及其子集中的步骤技能来条件化其动作，而其任务表现又反过来引导元技能智能体精化这些步骤技能。在 ALFWorld 与 WebShop 上的实验表明，CoSkill 显著优于先前的技能基与 RL 基线，成功率分别达到 98.4% 与 90.6%（+3.5 与 +6.2 pp）。

### 核心要点提炼
- **研究背景**：技能库能提升 LLM 智能体 RL 的采样效率，但现有范式把技能当作被动对象，阻碍其与推理智能体协同进化。
- **研究动机**：把"元技能工作流"从固定流程升级为"可学习的元技能智能体"，实现技能与推理的端到端协同适应。
- **核心方法**：CoSkill = 元技能智能体 + 推理智能体共享 backbone + 分层技能库上的联合 RL。
- **主要结果**：ALFWorld 98.4%、WebShop 90.6%，显著优于技能基与 RL 基线；早期采样效率、渐近性能、wall-clock 效率均更优。
- **研究意义**：为 LLM 智能体的技能学习提供"技能与推理协同进化"的新范式。

## 研究背景与动机

### 领域现状
LLM 智能体在 RL 训练中面临采样效率低的瓶颈。技能库（skill library）通过存储与复用可迁移的过程性知识来缓解这一问题：智能体不再从零探索，而是检索已学技能来引导动作。现有技能库范式大致分为两类：一类把"技能进化"与"策略优化"解耦（技能库静态或独立更新）；另一类把"元技能"（决定何时/如何组合技能的高层流程）实例化为固定工作流。

### 现有方法的局限性
- **技能被当作被动对象**：技能库要么静态，要么独立于策略优化更新，无法与推理智能体实时协同。
- **元技能固化**：高层"如何组织技能"的流程被写死为固定工作流，缺乏自适应的学习能力。
- **缺乏协同适应**：推理智能体无法反过来影响技能的进化，导致技能库与任务需求逐渐脱节。

### 研究动机
作者的核心洞察是：**技能不应是被管理的静态资产，而应是与推理智能体共同进化的活性组件**。为此，把"元技能"本身升级为一个可学习的智能体，让推理与技能组织在同一训练目标下相互塑造。

## 研究问题

### 核心研究问题
**如何让 LLM 智能体的"推理能力"与"技能库的组织/进化"在同一框架下端到端地协同适应，从而同时提升采样效率与最终性能？**

## 方法概述

### 核心思想
把分层技能库的"组织者"（元技能工作流）重构为一个**可学习的 Meta-Skill Agent**，与执行任务的 **Reasoning Agent** 组成共享 backbone 的合作团队，在同一 RL 目标下联合训练——推理智能体用检索到的技能指导动作，其任务表现作为反馈引导元技能智能体精化技能。

### 方法框架

#### 整体架构
![[overview_skillrl_page1.png|800]]

> 图1：CoSkill 整体架构——Reasoning Agent 与 Meta-Skill Agent 共享 backbone，在分层技能库上联合 RL，实现推理与技能的端到端协同适应。

#### 各模块详细说明

**模块1：Reasoning Agent（推理智能体）**
- **功能**：根据当前任务状态，检索并应用合适的技能，产生具体动作。
- **输入**：任务观测 + 检索到的任务技能（task skill）及其子集中的步骤技能（step skills）。
- **输出**：可执行的动作序列。
- **关键技术**：以检索到的技能作为动作的条件化上下文，而非从头规划。

**模块2：Meta-Skill Agent（元技能智能体）**
- **功能**：学习"如何组织、选择、精化步骤技能"的高层策略，替代固化的元技能工作流。
- **输入**：推理智能体的任务表现反馈。
- **输出**：对步骤技能的调整/精化信号。
- **关键技术**：将原本静态的 meta-skill workflow 参数化为可学习策略。

**模块3：分层技能库（Hierarchical Skill Library）**
- **功能**：存储"任务技能 → 步骤技能"的分层结构。
- **输入**：任务的技能需求。
- **输出**：可检索的任务技能及其子步骤技能集合。
- **关键技术**：任务技能作为父节点，步骤技能作为子集，支持分层检索与复用。

**模块4：共享 Backbone 的联合 RL**
- **功能**：让两个智能体共享同一模型 backbone，在统一奖励信号下联合优化。
- **关键技术**：合作团队建模，端到端 co-adaptation，避免技能进化与策略优化的解耦。

## 实验结果

### 实验目标
验证 CoSkill 相对技能基与 RL 基线的性能优势，以及其在采样效率、渐近性能与 wall-clock 效率上的提升。

### 数据集与环境
- **ALFWorld**：文本交互的具身家务任务环境。
- **WebShop**：模拟电商购物环境。

### 主要结果
- **ALFWorld**：成功率 **98.4%**（+3.5 pp），显著优于先前的技能基与 RL 基线。
- **WebShop**：成功率 **90.6%**（+6.2 pp）。
- **效率**：早期采样效率、渐近性能、wall-clock 效率均优于基线（见论文 Figure 1）。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出"技能是活性组件、应与推理共同进化"的范式，突破了"技能库作为静态资产"的既有认知。
- 用"共享 backbone 的合作团队"统一了技能组织与策略优化，给出一个干净的多智能体 RL 抽象。

#### 实际应用价值
- **应用场景**：需要长期任务执行与技能复用的 LLM 智能体系统（如具身智能、自主购物助手、流程自动化）。
- **潜在影响**：提升 LLM 智能体 RL 的采样效率，降低从零训练的成本。

### 方法优势详解
- **端到端协同适应**：技能进化与策略优化不再脱节，技能库能随任务需求动态调整。
- **共享 backbone 的高效性**：两智能体共享参数，降低训练与推理开销。
- **强实证**：在两个代表性环境上均有显著提升，且开源代码。

### 局限性分析
- **局限1：环境规模有限**——只在 ALFWorld、WebShop 两个相对简单的环境上验证，扩展到长时程、开放域任务的效果待验证。
- **局限2：单 backbone 的共享机制**——两智能体共享参数可能带来表征冲突，如何平衡二者优化目标值得深入。
- **局限3：技能库冷启动**——初始技能库如何构建（是否需要先验/预训练技能）在摘要中未展开。

### 适用性与场景分析
- **适用场景**：有明确任务结构、可分解为子步骤技能，且需要反复执行相似任务的智能体系统。
- **不适用场景**：一次性、无重复结构、或难以定义"技能"粒度的任务。

## 技术路线定位

### 所属技术路线
属于 **LLM Agent 强化学习 + 技能学习（Skill Learning）** 路线，与 Skill-RL、AgentSkill、以及多智能体协作框架相邻。

### 本文在技术路线中的位置
- **承上**：继承技能库提升采样效率的思想，但批评了"技能作为被动对象"的局限。
- **启下**：把"元技能"参数化为可学习智能体，为技能库的自主进化开辟方向。

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 范式立意清晰（技能与推理协同进化），实现干净（共享 backbone 联合 RL），实证扎实且开源；在更大、更开放环境上的验证是下一步关键。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 把元技能工作流升级为可学习智能体，视角有突破 |
| 技术质量 | 8/10 | 共享 backbone 联合 RL 的实现干净高效 |
| 实验充分性 | 7/10 | 两个环境 + 多基线，但环境规模偏小 |
| 写作质量 | 8/10 | 问题动机清晰，框架描述到位 |
| 实用性 | 8/10 | 开源 + 显著提升 + 效率优势，落地价值高 |

### 重点关注
- 需关注"共享 backbone 下两个智能体优化目标的平衡"这一设计细节，以及技能库冷启动策略。

## 相关论文
- 与 Skill-RL、AgentSkill 等 LLM 智能体技能学习工作，以及多智能体 RL（MARL）直接相关。

## 外部资源
- 代码：https://github.com/jinyuan-cookie/CoSkill

> [!tip] 关键启示
> 技能不该是"被管理的静态资产"，而应是"与推理共同进化的活性组件"——把技能的组织者本身变成可学习智能体，是提升 LLM 智能体采样效率的关键一跃。

> [!success] 推荐指数
> ⭐⭐⭐⭐ LLM 智能体 + 技能学习方向的读者值得精读，范式与实现都很有参考价值。
