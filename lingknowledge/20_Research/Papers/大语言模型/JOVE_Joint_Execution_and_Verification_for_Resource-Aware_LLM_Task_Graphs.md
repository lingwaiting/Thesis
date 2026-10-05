---
date: "2026-10-02"
paper_id: "arXiv:2610.03296"
title: "JOVE: Joint Execution and Verification for Resource-Aware LLM Task Graphs"
authors: "Haoran Zhang, Dongjun Kim, Seohyeon Cha, Kevin S Chan, Ananthram Swami, Gustavo De Veciana, Haris Vikalo"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Inference
  - Task-Graph
  - Resource-Allocation
  - Verification
  - Online-Learning
quality_score: "8.0/10"
created: "2026-10-05"
updated: "2026-10-05"
status: analyzed
---

# JOVE: Joint Execution and Verification for Resource-Aware LLM Task Graphs

## 核心信息
- **论文ID**：arXiv:2610.03296
- **作者**：Haoran Zhang, Dongjun Kim, Seohyeon Cha, Kevin S Chan, Ananthram Swami, Gustavo De Veciana, Haris Vikalo
- **机构**：--
- **发布时间**：2026-10-02
- **类别**：cs.AI / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.03296) | [PDF](https://arxiv.org/pdf/2610.03296)
- **推荐评分**：8.67 / 10

## 摘要翻译

### 英文摘要
Complex reasoning queries can be decomposed into directed acyclic task graphs and distributed across heterogeneous LLMs, reducing latency through parallelism and enabling smaller models to solve complex tasks. In practice, however, the suitability of an LLM for a given subtask may be a priori unknown, and execution alone does not reveal output correctness. We propose JOVE, an online framework that jointly assigns executor LLMs and selects intermediate outputs for paid verification. Verification runs asynchronously and is used to improve future allocations, so the system must balance spending on execution now against learning for later. We study how to optimize this trade-off under a long-term budget and a per-query latency constraint, with stochastic, initially unknown LLM service quality, invocation costs, and execution times.

### 中文翻译
复杂推理查询可被分解为有向无环任务图，并分布到异构 LLM 上执行，通过并行降低延迟，并让较小的模型也能解决复杂任务。然而在实践中，某个 LLM 对特定子任务的适配度可能事先未知，且仅靠执行无法揭示输出的正确性。作者提出 JOVE，一个在线框架，联合分配执行 LLM，并为付费验证选择中间输出。验证异步运行，用于改进未来的分配，因此系统必须在「当前执行的开销」与「为未来学习」之间权衡。作者研究如何在长期预算与单查询延迟约束下优化这一权衡，同时面对随机且初始未知的 LLM 服务质量、调用成本与执行时间。

### 核心要点提炼
- **研究背景**：复杂查询可分解为任务图并分布式执行，但子任务的 LLM 适配度未知、正确性难以判断。
- **研究动机**：需要在执行开销与验证学习之间做在线权衡。
- **核心方法**：JOVE 联合分配执行器并选择中间输出做付费验证，用逐查询的混合整数线性规划（MILP）+ 在线学习 + 信息增益奖励求解。
- **主要结果**：在四个推理基准上达到有竞争力的准确率，同时把平均成本与延迟降低至少 3.17 倍。
- **研究意义**：为资源受限的 LLM 任务图推理提供了可证明的学习保证（次线性 regret）。

## 研究背景与动机

### 领域现状
随着推理任务复杂度上升，「分解为任务图 + 多 LLM 协作」成为一种主流范式（如多智能体系统、检索增强生成）。它通过并行降低延迟，并让能力较弱的小模型也能参与解决复杂任务。

### 现有方法的局限性
- 子任务的 LLM 适配度事先未知，静态分配容易用错模型。
- 仅靠执行无法判断输出正确性，错误会在任务图中传播。
- 验证是有成本的（付费），何时验证、验证什么缺乏统一框架。

### 研究动机
作者希望把「执行」与「验证」作为一个联合在线决策问题：在长期预算和延迟约束下，动态决定用哪个模型执行、对哪些中间输出付费验证，从而在精度与成本之间取得最优权衡。

## 研究问题

### 核心研究问题
在 LLM 服务质量、调用成本与执行时间均随机且初始未知的条件下，如何联合优化任务图的执行分配与验证选择，以最小化长期成本同时满足延迟约束并保证精度？

## 方法概述

### 核心思想
把执行与验证决策建模为逐查询的混合整数线性规划（MILP），用在线学习基于验证反馈更新任务相关的 LLM 质量估计，并引入「信息增益奖励」把「学习价值」显式纳入分配决策。

### 方法框架

#### 整体架构
JOVE 接收分解好的 DAG 任务图，为每个子任务选择执行 LLM，并异步选择中间输出进行付费验证，验证结果回流改进后续分配。

![[overview_graph_page1.png|800]]

> 图1：JOVE 系统总览图，展示任务图、执行分配、异步验证与在线学习之间的闭环。

#### 各模块详细说明

**模块1：执行分配**
- **功能**：为每个子任务选择执行 LLM。
- **输入**：DAG 任务图 + 各 LLM 的服务质量/成本/延迟估计。
- **输出**：执行分配方案。
- **关键技术**：逐查询 MILP 求解。

**模块2：验证选择**
- **功能**：选择哪些中间输出进行付费验证。
- **输入**：执行输出 + 不确定性估计。
- **输出**：验证目标集合。
- **关键技术**：异步验证，不阻塞执行流水线。

**模块3：在线学习 + 信息增益奖励**
- **功能**：用验证反馈更新 LLM 质量估计，并把「学习价值」纳入分配。
- **处理流程**：
  1. 基于验证反馈更新任务相关的 LLM 质量估计。
  2. 计算信息增益奖励（information-gain bonus）。
  3. 将奖励纳入分配决策的优化目标。
- **关键技术**：在线学习 + 信息增益，保证次线性质量学习 regret。

## 实验结果

### 实验设置
- **基准**：四个推理基准。
- **基线**：标准推理（standard inference）baselines。
- **指标**：准确率、平均成本、平均延迟。

### 主要结果

| 维度 | JOVE | 标准推理基线 |
|------|------|--------------|
| 准确率 | 有竞争力 | -- |
| 平均成本 | 降低 ≥ 3.17× | 基准 |
| 平均延迟 | 降低 ≥ 3.17× | 基准 |

> 关键结论：在四个推理基准上，JOVE 与标准推理基线达到有竞争力的准确率，同时把平均成本与延迟降低至少 3.17 倍。在自然假设下，作者建立了次线性的质量学习 regret。

## 深度分析

### 研究价值评估

#### 理论贡献
- **次线性 regret 保证**：为「执行 + 验证」联合在线决策建立了可证明的学习保证。
- **信息增益奖励**：把探索（exploration）价值显式化为分配目标，是 bandit 思想在 LLM 任务图上的应用。

#### 实际应用价值
- **成本敏感部署**：适用于需要控制 LLM 调用成本的推理服务。
- **异构模型调度**：在混合使用大小模型的场景中动态匹配任务与模型。

### 方法优势详解

#### 优势1：执行与验证联合优化
- **描述**：不再是「先执行再验证」的两阶段，而是把两者作为同一决策问题的两个动作。
- **技术基础**：逐查询 MILP。
- **实验验证**：3.17 倍的成本/延迟降低。

#### 优势2：可证明的学习保证
- **描述**：次线性 regret 使方法在理论上可收敛到最优分配。
- **技术基础**：在线学习 + 信息增益奖励。

### 局限性
- MILP 逐查询求解的计算开销与可扩展性未充分讨论。
- 依赖「付费验证」这一成本模型，验证信号的质量假设需要进一步检验。
- 实验集中在四个推理基准，实际生产任务图的结构多样性有待覆盖。

### 相关论文对比
- 与 LLM 路由（routing）/级联（cascading）研究相关，但 JOVE 在任务图粒度上联合优化执行与验证。
- 与 bandit/在线学习在资源分配中的应用一脉相承，创新点在于把验证作为可学习的动作。

## 总结
JOVE 把 LLM 任务图的执行与验证统一为带 regret 保证的在线决策问题，为「在成本约束下高效推理」提供了理论扎实的框架。对需要大规模、低成本部署 LLM 推理的团队有直接参考价值。
