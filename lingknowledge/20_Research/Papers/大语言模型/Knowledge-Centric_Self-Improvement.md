---
date: "2026-07-21"
paper_id: "arXiv:2607.19592"
title: "Knowledge-Centric Self-Improvement"
authors: "Xuefei Julie Wang, Lauren Hyoseo Yoon, Chengrui Qu, Amanda Zichang Wang, Atharva Sehgal, Eric Mazumdar, Yisong Yue"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM
  - Self-Improvement
  - Knowledge-Base
  - Agent
  - RAG
  - Multi-Agent
quality_score: "8.0/10"
created: "2026-07-23"
updated: "2026-07-23"
status: analyzed
---

# Knowledge-Centric Self-Improvement

## 核心信息
- **论文ID**：arXiv:2607.19592
- **作者**：Xuefei Julie Wang, Lauren Hyoseo Yoon, Chengrui Qu, Amanda Zichang Wang, Atharva Sehgal, Eric Mazumdar, Yisong Yue
- **机构**：Caltech (推断自 Mazumdar/Yue 隶属)
- **发布时间**：2026-07-21
- **会议/期刊**：NeurIPS 2026
- **链接**：[arXiv](http://arxiv.org/abs/2607.19592v1) | [PDF](https://arxiv.org/pdf/2607.19592v1)
- **代码**：https://github.com/recursive-knowledge/KSI
- **评分**：9.33 (start-my-day 推荐评分)

## 摘要翻译

### 英文摘要
Self-improving AI systems typically treat the agent as the object that improves, by optimizing prompts, workflows, harnesses, or even the agent's own code. This agent-centric view can make improvements expensive to maintain and difficult to transfer, because gains become tied to a particular agent design, task distribution, or adaptation run. We study a complementary paradigm: knowledge-centric self-improvement, in which agents remain generic and disposable while the persistent object is a curated knowledge base that agents can leverage for future tasks. We conduct controlled case studies to operationalize this idea via a simple protocol. Agents attempt one task, then contribute evidence-grounded insights to a shared knowledge base via task-level and cross-task forums, followed by knowledge distillation. Because self-improvement is contained in the knowledge rather than the agent, improvement can be more inspectable, transferable, and portable. Across abstract reasoning, coding, and terminal benchmarks, this protocol improves solve rates while reducing dollar cost relative to agent-centric baselines. The resulting distilled knowledge also transfers to held-out tasks and across LLM families, indicating that the improvement is not merely an LLM- or run-specific behavior.

### 中文翻译
自改进 AI 系统通常将 Agent 视为改进对象，通过优化提示词、工作流、编排框架甚至 Agent 自身代码来实现。这种"以 Agent 为中心"的视角使得改进维护成本高且难以迁移，因为收益绑定于特定的 Agent 设计、任务分布或适应运行。我们研究了一个互补的范式：**知识中心的自改进**——Agent 保持通用且可丢弃，而持久对象是一个精心策划的知识库，Agent 可以利用该知识库完成未来的任务。我们通过一个简单协议进行了受控案例研究来操作化这一思想。Agent 尝试完成任务，然后通过任务级和跨任务论坛将证据支撑的见解贡献到共享知识库，随后进行知识蒸馏。由于自改进被包含在知识而非 Agent 中，改进更加可检查、可迁移、可跨平台移植。在抽象推理、编程和终端 benchmark 上，该协议提升了解决率，同时相对于 Agent 中心基线降低了成本。蒸馏后的知识还能够迁移到 held-out 任务和不同 LLM 家族，表明这种改进不仅仅是一种 LLM 或运行特定的行为。

### 核心要点提炼
- **研究背景**：现有自改进系统以 Agent 为中心（优化 prompt/code/workflow），收益难以迁移
- **研究动机**：将改进从 Agent 转移到知识，使收益持久化、可迁移
- **核心方法**：Agent 完成任务 → 贡献 evidence-grounded insight 到共享知识库 → task-level + cross-task 论坛讨论 → 知识蒸馏
- **主要结果**：提升解决率，降低成本；蒸馏知识可跨任务、跨 LLM 家族迁移
- **研究意义**：提出 AI 自改进的新范式——改进的载体是知识而非 Agent

## 方法概述

### 核心思想
翻转传统 Agent 中心的自改进范式：Agent 是通用的、一次性的工具，而真正的持久资产是知识库。通过让多个 Agent 协作积累和蒸馏知识，实现可检查、可迁移、可移植的自改进。

### 方法框架

![[framework_page1.png|800]]

协议包含三个关键阶段：
1. **任务执行**：通用 Agent 尝试完成任务，生成 evidence-grounded 的解决方案
2. **论坛讨论**：Agent 通过 task-level 论坛（单任务内）和 cross-task 论坛（跨任务）分享见解
3. **知识蒸馏**：从论坛讨论中提取结构化知识，存入持久知识库

## 实验结果
- 在抽象推理、编程和终端 benchmark 上提升解决率
- 成本低于 Agent 中心基线（因为 Agent 可复用，无需为每项改进重新优化）
- 蒸馏知识成功迁移到 held-out 任务和不同 LLM 家族

## 深度分析

### 研究价值
- **范式创新**：首次明确提出"知识中心自改进"并将其与 Agent 中心方法系统对比
- **可迁移性**：知识跨 LLM 家族迁移的能力是核心亮点——说明改进是"真知识"而非模型特化
- **实际价值**：为构建可长期演化的 AI 系统提供了新思路

### 局限性
- 论坛机制的可扩展性待验证（大规模任务下的协调成本）
- 知识蒸馏的质量和一致性保证机制未充分讨论
- 实验规模相对有限

## 我的综合评价

**8.0/10** - 提出了有价值的范式转变，实验验证了可迁移性这一关键 claim

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 范式级创新，翻转了自改进的思考方式 |
| 技术质量 | 7/10 | 协议设计清晰，但论坛/蒸馏细节待完善 |
| 实验充分性 | 7/10 | 多 benchmark 验证，但规模可扩大 |
| 写作质量 | 8/10 | 概念清晰，motivation 充分 |
| 实用性 | 8/10 | 知识可迁移性是极其实用的特性 |

> [!tip] 关键启示
> 自改进的未来不在于更聪明的 Agent，而在于更好的知识积累和迁移机制。让知识而非 Agent 成为持久资产。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！对 AI 系统设计和自改进方向有深刻启发。
