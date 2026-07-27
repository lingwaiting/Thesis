---
date: "2025-08-06"
paper_id: "arXiv:2508.13167"
title: "Chain-of-Agents: End-to-End Agent Foundation Models via Multi-Agent Distillation and Agentic RL"
authors: "30位作者"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Agent-Foundation-Model
  - Multi-Agent
  - Distillation
  - Agentic-RL
  - Chain-of-Agents
quality_score: "8.0/10"
created: "2026-07-27"
updated: "2026-07-27"
status: analyzed
---

# Chain-of-Agents: End-to-End Agent Foundation Models via Multi-Agent Distillation and Agentic RL

## 核心信息
- **论文ID**：arXiv:2508.13167
- **作者**：30位作者
- **机构**：--
- **发布时间**：2025-08-06
- **链接**：[arXiv](https://arxiv.org/abs/2508.13167) | [PDF](https://arxiv.org/pdf/2508.13167)
- **引用**：64（Semantic Scholar）

## 摘要翻译

### 英文摘要
Recent advances in large language models (LLMs) and multi-agent systems have demonstrated remarkable capabilities in complex problem-solving tasks such as deep research, vibe coding, and mathematical reasoning. However, most existing multi-agent systems are built upon manual prompt/workflow engineering with sophisticated agent frameworks, making them computationally inefficient, less capable, and can not benefit from data-centric learning. In this work, we introduce Chain-of-Agents (CoA), a novel paradigm of LLM reasoning that enables native end-to-end complex problem-solving in the same way as a multi-agent system within one model. In chain-of-agents problem-solving, the model dynamically activates different tool agents and role-playing agents to simulate multi-agent collaboration in an end-to-end fashion. To elicit end-to-end chain-of-agents problem-solving abilities in LLMs, we introduce a multi-agent distillation framework to distill state-of-the-art multi-agent systems into chain-of-agents trajectories for agentic supervised fine-tuning. We then use agentic reinforcement learning on verifiable agentic tasks to further improve the models' capabilities. We call the resulting models Agent Foundation Models (AFMs). Our empirical studies demonstrate that AFM establishes new state-of-the-art performance across diverse benchmarks in both web agent and code agent settings. We make the entire research, including the model weights, code for training and evaluation, and the training data, fully open-sourced.

### 中文翻译
大语言模型和多智能体系统的最新进展在深度研究、vibe coding 和数学推理等复杂问题求解任务中展现了卓越能力。然而，大多数现有多智能体系统建立在手工提示词/工作流工程和复杂的 Agent 框架之上，导致计算效率低下、能力受限，且无法从数据中心学习中获益。本文提出 Chain-of-Agents (CoA)——一种全新的 LLM 推理范式，在单一模型内以端到端方式原生地完成多智能体式的复杂问题求解。在 CoA 求解过程中，模型动态激活不同的工具 Agent 和角色扮演 Agent，以端到端方式模拟多智能体协作。为激发 LLM 的端到端 CoA 能力，我们引入多智能体蒸馏框架，将 SOTA 多智能体系统蒸馏为 CoA 轨迹用于 Agentic SFT。随后使用 Agentic RL 在可验证的智能体任务上进一步提升模型能力。所得模型称为 Agent Foundation Models (AFMs)。实验表明 AFM 在 Web Agent 和 Code Agent 场景的多个基准上均达到新的 SOTA。我们将模型权重、训练/评估代码和训练数据全部开源。

### 核心要点提炼
- **研究背景**：多智能体系统依赖手工框架工程，缺乏端到端学习能力
- **研究动机**：将多智能体能力原生集成到单一模型中
- **核心方法**：多智能体蒸馏（SFT）+ Agentic RL 两阶段训练
- **主要结果**：Web Agent 和 Code Agent 双场景 SOTA
- **研究意义**：代表了从"框架工程"到"模型原生"的范式转变，全面开源

## 研究背景与动机

### 领域现状
当前的多智能体系统（如 AutoGen、CrewAI 等）通过手工设计的提示词和工作流编排多个 LLM 实例协作求解。虽然有效，但这种"框架工程"方法存在根本性问题。

### 现有方法的局限性
- **计算低效**：多个 LLM 实例反复调用，token 消耗大
- **手工设计脆弱**：提示词和工作流难以泛化
- **无法数据中心学习**：框架无法从更多数据中自动改进
- **能力上限受限**：受限于基座模型 + 框架，而非端到端优化

### 研究动机
能否训练一个单一模型，使其原生地具备多智能体协作的推理能力？

## 研究问题

### 核心研究问题
1. 能否将多智能体系统的协作能力蒸馏进单一模型？
2. Agentic RL 能否在可验证任务上进一步提升 Agent 能力？
3. 端到端训练的 AFM 能否超越基于框架的多智能体系统？

## 方法概述

### 核心思想
**从"框架工程"到"模型原生"**——通过多智能体蒸馏将协作能力编码进模型权重，再通过 Agentic RL 在可验证任务上强化。

![[main_page1.png|800]]

> 图1：Chain-of-Agents 整体框架——多智能体蒸馏 + Agentic RL 两阶段训练

### 方法框架

**阶段1：多智能体蒸馏（Agentic SFT）**
- 收集 SOTA 多智能体系统的推理轨迹
- 将多轮多工具协作转化为 CoA 轨迹格式
- 使用这些轨迹对基座 LLM 进行监督微调
- 模型学习：何时调用工具、何时切换角色、如何编排子任务

**阶段2：Agentic RL**
- 在可验证的 Agent 任务上使用强化学习
- 奖励信号来自任务完成验证器
- 进一步提升模型的端到端 Agent 能力
- 结果：Agent Foundation Model (AFM)

### 关键创新
1. **CoA 范式**：单一模型内动态激活工具 Agent 和角色 Agent
2. **蒸馏方法**：从多智能体系统蒸馏知识到单一模型
3. **Agentic RL**：在 Agent 任务上首次大规模应用 RL
4. **全面开源**：权重、代码、数据全部开放

## 实验结果

### 评估场景
- **Web Agent**：网页浏览、信息检索、表单填写
- **Code Agent**：代码生成、调试、软件工程任务

### 主要结果
- AFM 在 Web Agent 和 Code Agent 基准上均达到 SOTA
- 端到端模型超越了基于多智能体框架的系统
- 推理效率显著提升（单模型 vs 多实例）

## 深度分析

### 研究价值评估

#### 理论贡献
- **范式创新**：首次证明多智能体能力可蒸馏进单一模型
- **训练方法**：提出 Agentic SFT + Agentic RL 的通用训练框架
- **开源贡献**：为后续研究提供完整的起点

#### 实际应用价值
- 降低多智能体系统的部署成本和复杂度
- 提高推理效率（单模型 vs 多实例）
- 提供可复现的开源实现

### 局限性
- 训练数据和轨迹质量依赖上游多智能体系统
- 在全新类型的 Agent 任务上可能需要重新训练
- 模型规模与推理成本的权衡

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** — 代表了从框架工程到模型原生的重要范式转变，全面开源增加了实际价值

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | CoA + AFM 范式创新，蒸馏+RL双阶段训练 |
| 技术质量 | 8/10 | 方法扎实，两阶段训练设计合理 |
| 实验充分性 | 7/10 | 覆盖 Web 和 Code 两个场景，但可扩展领域 |
| 写作质量 | 7/10 | 清晰完整 |
| 实用性 | 9/10 | 全面开源，可直接使用和复现 |

> [!tip] 关键启示
> 多智能体协作能力可以通过蒸馏+RL嵌入单一模型权重——这预示着 Agent 开发范式的根本转变。

> [!warning] 注意事项
> - 训练数据来自现有多智能体系统，存在继承偏见的风险
> - 需要在更大规模的任务类型上验证泛化性
> - AFM 的训练成本可能较高

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！Agent 基础模型方向的开创性工作，全面开源使其成为该方向的首选起点。

## 相关论文
- [[MAG-RPO]] — 多智能体强化学习 + LLM
- [[AutoGen]] — 多智能体框架
- [[SWE-Agent]] — 代码 Agent

## 外部资源
- 论文主页：https://arxiv.org/abs/2508.13167
- 代码和模型：论文中提供
