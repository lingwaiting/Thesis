---
date: "2026-09-23"
paper_id: "2609.24972"
title: "RRSI: Regularized Recursive Self-Improvement of Agent Harnesses"
authors: "Peng Xia, Rujun Han, Zifeng Wang, Yanfei Chen, Yufan Zhang, Yoonho Lee, Chengsong Huang, Han Yu, Zhongying CuiZhu, Yifei Ming, Huaxiu Yao, Burak Gokturk, Tomas Pfister, Chen-Yu Lee"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Agent
  - Self-Improvement
  - Regularization
quality_score: "8.2/10"
related_papers: []
created: "2026-09-23"
updated: "2026-09-23"
status: analyzed
---

# RRSI: Regularized Recursive Self-Improvement of Agent Harnesses

## 核心信息
- **论文ID**：2609.24972
- **作者**：Peng Xia, Rujun Han, Zifeng Wang, Yanfei Chen, Yufan Zhang, Yoonho Lee, Chengsong Huang, Han Yu, Zhongying CuiZhu, Yifei Ming, Huaxiu Yao, Burak Gokturk, Tomas Pfister, Chen-Yu Lee
- **机构**：Google Research（代码托管于 google-research/rrsi，多位作者为 Google 研究员）
- **发布时间**：2026-09-21
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2609.24972) | [PDF](https://arxiv.org/pdf/2609.24972)
- **引用**：--
- **项目主页**：https://regularized-rsi.com/
- **代码**：https://github.com/google-research/rrsi

## 摘要翻译

### 英文摘要
An LLM agent's capability is largely magnified by its harness, namely the prompts, control flow, tooling, memory, and context management surrounding the frozen backbone model. Recent methods increasingly automate this process by iteratively proposing and selecting component-wise edits of an agent harness, practically establishing a form of recursive self-improvement (RSI) at the agent-system level. However, such recursive evolution may overfit by memorizing the training tasks, showing large in-distribution gains that shrink or even vanish on out-of-distribution benchmarks. We introduce Regularized Recursive Self-Improvement of Agent Harnesses (RRSI), which incorporates the principles of regularizations into harness self-improvement by constraining the evolution candidate proposal and selection. The proposer operates with a temporally annealed budget, limiting how many edits a candidate can bundle, and it encourages unexplored trajectories based on evolution history. The selector is equipped with a critic and a pruner: the critic screens benchmark-specific proposals, while the pruner, removes changes that are too small, too expensive, or no longer useful.

### 中文翻译
LLM 智能体的能力在很大程度上被其 harness（即围绕冻结骨干模型的提示词、控制流、工具、记忆与上下文管理）所放大。近来的方法越来越多地通过迭代地提议并选择对 agent harness 的组件级编辑来自动化这一过程，实际上在智能体系统层面建立了一种递归自我改进（RSI）。然而，这种递归进化可能通过记忆训练任务而过拟合，表现出巨大的分布内增益，但在分布外基准上这些增益会缩小甚至消失。本文提出 Regularized Recursive Self-Improvement of Agent Harnesses（RRSI），通过约束进化候选的提议与选择，将正则化原则引入 harness 自我改进。提议器（proposer）以时间退火预算运行，限制候选可捆绑的编辑数量，并基于进化历史鼓励未探索的轨迹。选择器（selector）配备了一个 critic 与一个 pruner：critic 筛选针对特定基准的提议，pruner 则移除过小、过昂贵或不再有用的改动。

### 核心要点提炼
- **研究背景**：agent harness（提示词/控制流/工具/记忆/上下文）是放大冻结 LLM 能力的关键，自动优化 harness 的递归自我改进（RSI）成为热点。
- **研究动机**：现有 RSI 方法容易过拟合训练任务——分布内提升大，但分布外提升缩小甚至消失。
- **核心方法**：把"正则化"引入 harness 自我改进，同时约束候选提议（proposer 时间退火预算）与选择（selector 的 critic+pruner）。
- **主要结果**：8 个基准上，进化 split 最多 +14.1 分、5 个分布外基准最多 +4.7 分，且 harness 运行时策略 token 减少 30%。
- **研究意义**：证明"可复用的 agent 机制"优于"针对特定基准的 hack"，为 agent 系统的自动化设计提供了正则化范式。

## 研究问题

### 核心研究问题
如何在 agent harness 的递归自我改进中防止对训练任务的过拟合，使进化得到的 harness 具备跨基准的泛化能力，而非仅记住特定基准的"应试技巧"？

现有 RSI 方法的痛点：迭代地提议并选择 harness 组件编辑，往往倾向于针对当前训练基准做最优化的"记忆式"改动，导致分布内（in-distribution）性能虚高，而分布外（out-of-distribution）性能不升反降。

## 方法概述

### 核心思想
把传统机器学习中的"正则化"思想移植到 agent harness 的自我改进过程：通过约束"提议什么"（proposer）和"选择什么"（selector），让进化偏向可复用的通用机制，而不是针对特定基准的噪音或过度特化。

### 方法框架

#### 整体架构
RRSI 在 RSI 循环的两个关键环节施加正则化约束——提议器与选择器。

![[pipeline_page1.png|800]]

> 图1：RRSI 整体框架。提议器以时间退火预算生成候选 harness 编辑，鼓励探索未覆盖的进化轨迹；选择器用 critic 筛选掉针对特定基准的提议，用 pruner 移除过小/过贵/失效的改动，最终保留可复用的 agent 机制。

#### 各模块详细说明

**模块1：提议器（Proposer）——时间退火预算 + 探索鼓励**
- **功能**：在每轮进化中提议对 harness 的组件级编辑候选。
- **关键机制**：
  1. **时间退火预算（temporally annealed budget）**：限制单个候选可捆绑的编辑数量，并随时间逐步收紧，避免一次性堆叠过多改动。
  2. **探索鼓励**：基于进化历史，对未探索过的轨迹给予偏好，防止收敛到局部最优的"套路"。
- **目的**：让进化过程更稳健，减少对训练任务的记忆式过拟合。

**模块2：选择器（Selector）——critic + pruner**
- **critic（评论者）**：筛选出"针对特定基准优化"的提议，抑制那些只在一个基准上奏效的过度特化改动。
- **pruner（修剪器）**：移除三类低价值改动——过小的改动、过昂贵的改动、以及不再有用的改动。
- **目的**：让保留下来的机制是可复用、可迁移的通用组件。

**关键设计思想**：这些约束共同作用，偏好"可复用的 agent 机制"胜过"针对特定基准的 hack 或噪音"。

### 关键创新

1. **首次将正则化原则系统性地引入 harness 自我改进**——把防过拟合从参数空间迁移到 agent 系统层面。
2. **双端约束（提议+选择）**——不仅约束"生成什么候选"，还约束"保留什么候选"，形成完整的正则化闭环。
3. **可复现性优先的进化目标**——显式追求跨基准泛化，而非单一基准得分，改进了 RSI 的评估范式。

## 实验结果

### 数据集 / 基准
- **8 个基准**：覆盖 coding（代码）、agentic workspace（智能体工作空间）、engineering design（工程设计）等任务。
- **评测切分**：进化所用 split（in-distribution）+ 5 个分布外（OOD）基准。

### 主要结果
- **进化 split 提升**：最高 **+14.1 分**（对比未正则化的进化）。
- **分布外泛化**：在 5 个 OOD 基准上最高 **+4.7 分**，说明正则化确实缓解了过拟合。
- **效率提升**：得到的 harness 运行时策略 token 减少 **30%**，即更精简、更高效。

![[main_results_page1.png|800]]

> 图2：主要实验结果，展示 RRSI 在分布内与分布外基准上相对未正则化进化的提升，以及 token 效率的改善。

### 实验结论
正则化不仅没有牺牲分布内性能，反而同时提升了分布内、分布外与推理效率，验证了"可复用机制 > 应试式特化"的核心假设。

## 深度分析

### 研究价值
- **理论贡献**：将机器学习正则化的经典思想系统性地引入 agent 系统的自动设计，提出"agent 层面的过拟合"这一概念，并给出可操作的缓解方案。
- **实际应用**：为自动化构建高质量 agent harness 提供了一条更稳健的路径，降低了对人工调优 prompt/工具/控制流的依赖。
- **领域影响**：为 RSI / self-improving agent 的研究提供了新的评估标准（必须看 OOD 泛化，而非单一基准得分）。

### 优势
- **思路简洁而有效**：用两个直观的约束（退火预算 + critic/pruner）解决 RSI 过拟合，工程上容易落地。
- **评估严谨**：同时报告分布内、分布外和效率三个维度，避免了"单一基准刷分"的陷阱。
- **来自 Google Research**：代码与项目主页公开，可信度与可复现性高。

### 局限性
- **基准规模有限**：8 个基准仍难以覆盖真实 agent 任务的多样性，OOD 泛化的结论需在更广泛任务上验证。
- **正则化设计偏启发式**：退火预算、critic/pruner 的具体形式仍依赖经验设定，缺乏更理论化的分析。
- **增量式改进**：本质是对现有 RSI 框架的约束增强，而非全新的进化范式。

### 适用场景
- 需要自动化调优 agent harness（prompt、控制流、工具组合）的场景。
- 关注 self-improving agent 泛化性与稳健性的研究。

## 与相关论文对比

### [[TextGrad]] 等 agent 系统优化工作 - 对比关系
- **差异**：TextGrad 等主要通过文本梯度优化 prompt/系统组件，而 RRSI 侧重在递归进化的提议-选择环节加正则化。
- **改进**：RRSI 显式建模并缓解了优化过程中的过拟合问题。

### [[Self-Refine]] / 递归自我改进相关工作 - 对比关系
- **差异**：Self-Refine 关注单次迭代的自我修正，RRSI 关注多轮进化的泛化性。
- **改进**：引入时间退火预算与 critic/pruner 来约束进化轨迹。

## 技术路线定位

本文属于 **Agent 系统自动设计 / 递归自我改进（RSI）** 技术路线，主要关注**进化过程的泛化性与正则化**这一子方向。

```
手工设计 harness → 自动化 prompt 优化 → RSI（迭代提议-选择）→ RRSI（正则化 RSI）→ 未来方向
        ↑              ↑                    ↑                    ↑
     早期工作      TextGrad 等        本文前的 RSI 方法        本文工作
```

### 本文在技术路线中的位置
- **承上**：继承了 RSI 的"迭代提议-选择"框架。
- **启下**：确立了"正则化 + OOD 泛化评估"作为 RSI 研究的新基线。

## 未来工作建议

1. 在更广泛、更多样的真实 agent 任务上验证 RRSI 的泛化性。
2. 对退火预算、critic/pruner 的设计进行更理论化的分析。
3. 将正则化思想扩展到多智能体协作的 harness 进化场景。

## 我的综合评价

### 价值评分
- **总体评分**：**8.2/10** —— 思路清晰、工程可落地、评估严谨，是 RSI 泛化性方向上有价值的增量贡献。
- **分项评分**：
  - 创新性：8/10 —— 把正则化系统性地引入 agent harness 进化，概念上有新意。
  - 技术质量：8/10 —— 方法简洁，双端约束设计合理。
  - 实验充分性：8/10 —— 8 基准 + OOD + 效率三维度，较充分。
  - 写作质量：8/10 —— 表述清晰。
  - 实用性：8/10 —— 有公开代码与项目页，便于复现。

### 突出亮点
- "agent 层面的过拟合"这一问题的提出与命名。
- 提议器 + 选择器的双端正则化设计。
- 同时提升分布内、分布外与效率的完整证据链。

### 重点关注
- 时间退火预算与 critic/pruner 的具体实现细节。
- 分布外泛化提升（+4.7 分）在不同任务类型上的分布情况。

### 可借鉴点
- 在自我改进类系统中引入"正则化/泛化"视角。
- 用 critic+pruner 结构筛选进化候选的思路。

### 批判性思考
- 正则化设计较多依赖经验，缺乏理论保证。
- "30% 更少 token"的效率提升与性能提升之间的因果关系需进一步厘清。

## 我的笔记

[用户阅读后手动补充的内容]

## 相关论文
- [[TextGrad]] - 文本梯度优化 agent 系统组件
- [[Self-Refine]] - 递归自我修正相关工作

## 外部资源
- arXiv：https://arxiv.org/abs/2609.24972
- 代码：https://github.com/google-research/rrsi
- 项目主页：https://regularized-rsi.com/
