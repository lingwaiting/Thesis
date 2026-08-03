---
date: "2026-08-03"
paper_id: "arXiv:2607.29055"
title: "Autonomous Repair for Multi-Agent Systems via Monte-Carlo Tree Search"
authors: "Hanxiao Lu, Tianyi Zhang (Purdue University)"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - Multi-Agent-Systems
  - MCTS
  - MAS-Repair
  - Agentic-AI
  - Failure-Attribution
quality_score: "8.2/10"
created: "2026-08-03"
updated: "2026-08-03"
status: analyzed
---

# MARS: Autonomous Repair for Multi-Agent Systems via Monte-Carlo Tree Search

## 核心信息
- **论文ID**：arXiv:2607.29055
- **作者**：Hanxiao Lu, Tianyi Zhang
- **机构**：Purdue University
- **发布时间**：2026-07-31
- **会议/期刊**：AAAI 2027 (投稿)
- **链接**：[arXiv](https://arxiv.org/abs/2607.29055) | [PDF](https://arxiv.org/pdf/2607.29055)
- **引用**：--

## 摘要翻译

### 英文摘要
Multi-agent systems (MAS) are increasingly deployed to solve complex tasks. In case of incorrect or unsatisfactory outputs, users have to manually locate agent mistakes by inspecting agent trajectories (failure attribution) and provide feedback to refine the outputs (repair). Despite some recent work in MAS failure attribution, automated mechanisms to recover from such mistakes remain largely unexplored. To bridge this gap, we propose MARS, a search-based framework that formulates MAS repair as a Monte Carlo Tree Search (MCTS) process and navigates the vast space of potential repairs via diagnosis-guided expansion with taxonomy-augmented evaluation. Unlike standard MCTS, which evaluates a complete simulation via full rollout, MARS evaluates the agent trajectory using partial rollout to reduce token consumption. Furthermore, we introduce StateMAS, a large-scale MAS repair benchmark with 1,310 replayable multi-agent failure trajectories spanning four types of agent architectures and four LLM backbones. Experiments on StateMAS demonstrate that MARS consistently outperforms state-of-the-art methods, achieving an absolute improvement from 3.0% to 12.1% across all settings, while maintaining a comparable token consumption cost.

### 中文翻译
多智能体系统（MAS）越来越多地被部署用于解决复杂任务。当MAS产生不正确或不令人满意的输出时，用户需要手动检查智能体轨迹来定位错误（即故障归因），并提供反馈以优化输出（即修复）。尽管最近有一些关于MAS故障归因的工作，但从错误中自动恢复的机制仍然很大程度上未被探索。为填补这一空白，我们提出MARS，一个基于搜索的框架，将MAS修复形式化为蒙特卡洛树搜索（MCTS）过程，通过诊断引导扩展和分类法增强评估来导航庞大的修复空间。与标准MCTS评估完整模拟不同，MARS使用部分展开评估智能体轨迹以减少token消耗。此外，我们引入StateMAS，一个大规模MAS修复基准，包含1310条可回放的多智能体失败轨迹，跨越四种智能体架构和四种LLM后端。

### 核心要点提炼
- **研究背景**：多智能体系统日益普及，但错误修复仍以手动为主
- **研究动机**：现有方法（DoVer）采用顺序探索策略，搜索空间受限且token成本高
- **核心方法**：将MAS修复建模为MCTS过程，使用部分展开和分类法增强评估
- **主要结果**：在GAIA和AssistantBench上实现3.0%-12.1%的绝对提升
- **研究意义**：首个系统性的MAS自动修复框架，提出可复现基准StateMAS

## 研究背景与动机

### 领域现状
多智能体系统（MAS）被广泛部署于复杂任务中（WebWalker, WebPilot, ChatDev, MetaGPT等）。当输出不正确时，用户需要手动：1）检查智能体轨迹定位错误（故障归因）；2）提供反馈优化输出（修复）。

### 现有方法的局限性
- **DoVer**是唯一自动MAS修复的工作，但存在三个问题：
  1. 顺序探索策略限制了搜索空间
  2. 聚焦于编排器级别修复，不适用于无中心化编排器的系统
  3. 依赖Magentic-One编排框架，通用性受限
- 现有故障归因数据集（AgentDebugger等）的轨迹不可回放，无法用于修复评估

### 研究动机
从手动修复到自动修复的转变至关重要。需要：1）更强大的搜索策略探索修复空间；2）通用的、架构无关的修复方法；3）可复现的评估基准。

## 研究问题

### 核心研究问题
如何在多智能体系统中自动定位故障并生成有效修复，同时保持合理的token消耗和通用性？

子问题包括：
- 如何高效搜索庞大的修复空间（而非顺序探索）？
- 如何在部分轨迹（非完整执行）上评估修复质量？
- 如何构建可复现的MAS修复评估基准？

## 方法概述

### 核心思想
将MAS修复建模为蒙特卡洛树搜索（MCTS）过程——每个节点代表一个智能体轨迹状态，通过三种专门动作（回滚、引导修复、继续执行）来扩展和探索可能的修复路径。

### 方法框架

#### 整体架构

![[mars_page1.png|800]]

> 图1：MARS框架架构。MCTS树中的每个节点代表一个轨迹状态，通过Rollback（回滚）、Guided Repair（引导修复）和Continuation（继续）三种动作扩展搜索空间。分类法增强评估提供动态奖励信号。

#### 各模块详细说明

**模块1：诊断引导扩展（Diagnosis-Guided Expansion）**
- **功能**：根据故障诊断结果，引导MCTS的节点扩展方向
- **三种动作**：
  - **Rollback**：回滚到错误发生前的状态
  - **Guided Repair**：基于诊断结果生成修复指令，引导智能体修正错误
  - **Continuation**：从修复后的状态继续执行
- **关键技术**：部分展开（Partial Rollout）——仅重新执行k步而非完整执行到结束，大幅减少token消耗

**模块2：分类法增强评估（Taxonomy-Augmented Evaluation）**
- **功能**：在部分展开无法产生完整轨迹的情况下，提供质量评估信号
- **方法**：
  - 定义常见失败模式分类（如工具调用错误、推理错误、消息格式错误等）
  - 对每个部分展开节点，根据失败模式分类给予奖励/惩罚
  - 生成动态奖励信号驱动MCTS搜索决策
- **关键创新点**：无需完整执行结果即可评估节点质量

**模块3：StateMAS基准**
- **功能**：可复现的MAS修复评估基准
- **构成**：
  - 1310条可回放的多智能体失败轨迹
  - 覆盖4种智能体架构和4种LLM后端
  - 基于GAIA和AssistantBench两个流行基准
- **特性**：
  - 捕获每个智能体步骤的完整系统状态
  - 支持确定性回放和任意步骤回滚
  - 与特定编排框架（如Magentic-One）解耦

### 方法架构图

MARS 的核心流程：
```
失败轨迹 → [故障诊断] → [MCTS树初始化] → [诊断引导扩展]
                                                  ↓
                        [部分展开执行] ← [选择动作: Rollback/Repair/Continue]
                              ↓
                        [分类法评估] → [奖励信号] → [反向传播]
                              ↓
                        [选择最佳修复路径] → 输出修复后轨迹
```

## 实验结果

### 实验设置

#### 基线方法
- **DoVer**：唯一的现有MAS自动修复方法
- **Reflexion-based**：基于口头反馈的自我反思修复
- **ReAct-based**：基于推理-行动循环的修复

#### 评估指标
- 修复成功率（Repair Success Rate）
- Token消耗（Token Consumption）
- LLM后端：GPT-4、Claude、Gemini、Llama 四种

### 主要结果

#### 主实验结果
在GAIA基准上，MARS相比DoVer实现8.5%-10.3%的绝对提升；在AssistantBench上实现6.1%-12.2%的提升。

| 设置 | 最佳基线 | MARS | 提升 |
|------|---------|------|------|
| GAIA (所有LLM) | DoVer | MARS | +8.5%~10.3% |
| AssistantBench (所有LLM) | DoVer | MARS | +6.1%~12.2% |
| 不同架构 (best baseline) | 各异 | MARS | +3.0%~12.1% |

#### Token效率
- MARS的token消耗仅比最经济的基线多5.6%
- 比最昂贵的基线少消耗高达59.1%的token
- 性能提升不以计算成本为代价

### 消融实验
- **诊断引导扩展**和**分类法增强评估**是性能提升的关键
- 部分展开设计在token效率方面贡献显著

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：首次将MAS修复形式化为MCTS搜索问题
  - 创新点：将树搜索引入多智能体轨迹修复
  - 学术价值：开辟了MAS修复的新研究范式
- **贡献2**：提出部分展开+分类法评估的实用方案
  - 解决MCTS在LLM场景中token开销过大的问题
- **贡献3**：StateMAS可复现基准
  - 为MAS修复研究提供标准化评估平台

#### 实际应用价值
- **应用场景**：生产环境中的MAS错误自动恢复（如代码生成、数据分析、Web导航等）
- **优势**：通用（不依赖特定编排框架）、高效（token可控）、有效（显著提升修复率）

### 局限性分析
- **局限1**：分类法评估依赖于预定义失败模式，对新类型失败可能不完整
- **局限2**：部分展开的k值选择缺乏理论指导
- **局限3**：实验限于GAIA和AssistantBench，在生产环境的泛化性待验证
- **局限4**：修复动作空间仍受限于预定义策略

### 适用性与场景分析
- **适用场景**：有明确子任务分解的MAS、可回放执行的系统
- **不适用场景**：实时系统（不可回滚）、完全黑盒的智能体调用

## 与相关论文对比

### [[20_Research/Papers/强化学习与智能体/Beyond_Component_Testing_Validating_Agentic_AI_Systems|Beyond Component Testing]] - Agentic AI验证综述
- **关系类型**：互补 — MARS聚焦修复，Beyond Component Testing聚焦验证
- **本文改进**：MARS提供了自动化修复机制，补充了验证框架的实践缺口

### DoVer (Ma et al., 2025)
- **关系类型**：直接改进
- **本文优势**：MCTS vs 顺序策略；通用架构 vs Magentic-One依赖；部分展开减少token

## 我的综合评价

### 价值评分

#### 总体评分
**8.2/10** - 首个系统性的MAS自动修复框架，方法新颖且实用，但受限于预定义失败分类

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次将MCTS引入MAS修复，部分展开+分类法评估设计巧妙 |
| 技术质量 | 8/10 | 方法设计严谨，三种动作覆盖主要修复场景 |
| 实验充分性 | 8/10 | 4种LLM+4种架构+2个基准的全面评估，消融实验充分 |
| 写作质量 | 8/10 | 结构清晰，问题定义明确 |
| 实用性 | 8/10 | 通用性强，token高效，有明确应用场景 |

> [!tip] 关键启示
> MAS修复不一定需要完整重新执行——部分展开配合智能评估即可做出高质量修复决策。

> [!warning] 注意事项
> - 分类法评估的覆盖范围可能限制对新失败类型的修复能力
> - StateMAS基准的构建依赖GAIA和AssistantBench，与生产环境可能有差距
> - k值选择对性能和效率有重要影响

## 相关论文
- [[20_Research/Papers/强化学习与智能体/Beyond_Component_Testing_Validating_Agentic_AI_Systems|Beyond Component Testing]] - Agentic AI验证综述
- [[20_Research/Papers/强化学习与智能体/SeekBrain_An_Autonomous_Multi-Agent_System_for_Accelerating_Neuroscience_Discovery|SeekBrain]] - 科学发现中的MAS应用
