---
date: "2026-07-21"
paper_id: "arXiv:2607.17331"
title: "Agentic ERP: Multi-Agent Large Language Model Architecture for Autonomous Enterprise Resource Planning"
authors: "Zhihao Liu, Tianyu Wang, Xi Vincent Wang, Lihui Wang"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Multi-Agent-System
  - LLM-Agent
  - Enterprise-Resource-Planning
  - Graph-Orchestration
  - Human-in-the-Loop
quality_score: "8.2/10"
created: "2026-07-21"
updated: "2026-07-21"
status: analyzed
---

# Agentic ERP: Multi-Agent LLM Architecture for Autonomous ERP

## 核心信息
- **论文ID**：arXiv:2607.17331
- **作者**：Zhihao Liu, Tianyu Wang, Xi Vincent Wang, Lihui Wang
- **机构**：--
- **发布时间**：2026-07-19
- **会议/期刊**：arXiv 预印本（cs.AI, cs.MA）
- **链接**：[arXiv](http://arxiv.org/abs/2607.17331v1) | [PDF](https://arxiv.org/pdf/2607.17331v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Enterprise Resource Planning (ERP) systems record transactions reliably but still delegate almost all operational decision-making to human specialists, because classical rule-based automation cannot reason about exceptions and monolithic AI assistants degrade when asked to coordinate across functional boundaries. This paper presents Agentic ERP, an expert-system architecture that combines role-aligned large-language-model (LLM) agents with a risk-tiered human-in-the-loop harness and a graph-based orchestrator to execute end-to-end business workflows on a production ERP backend. The system is evaluated at three levels: a scenario-based task suite, a comprehensive comparison of six orchestration paradigms on cross-functional crisis tasks, and a 365-day agent-in-the-loop simulation against rule-based RPA and no-intervention baselines. Across these levels the proposed multi-agent method is significantly better than the baseline, and the system sustains a simulated year of operation with zero stockouts while the rule-based baseline accumulates hundreds.

### 中文翻译
企业资源规划（ERP）系统可靠地记录交易，但仍将几乎所有运营决策委托给人类专家，因为经典的基于规则的自动化无法处理异常情况，而单体 AI 助手在需要跨职能边界协调时会退化。本文提出 Agentic ERP，一种专家系统架构，将角色对齐的大语言模型（LLM）Agent 与风险分级的人工干预机制和图编排器相结合，在生产 ERP 后端上执行端到端业务工作流。系统在三个层面进行评估：基于场景的任务套件、跨职能危机任务上六种编排范式的全面对比，以及针对基于规则 RPA 和无干预基线的 365 天 agent-in-the-loop 仿真。在所有层面上，所提多智能体方法显著优于基线，系统在模拟的一年运营中保持零缺货，而基于规则的基线在相同需求流下积累数百次缺货。

### 核心要点提炼
- **研究背景**：ERP 系统是企业的核心运营基础设施，但决策仍需人类专家，规则自动化无法处理异常
- **研究动机**：单体 AI 助手在跨职能协调时退化，需多智能体协作架构
- **核心方法**：角色对齐 LLM Agent + 风险分级人工干预 + 图编排器（Planner-Executor-Reflector-Responder）
- **主要结果**：365 天仿真零缺货，六种编排范式对比中显著优于基线
- **研究意义**：首次展示 LLM Agent 可将 ERP 系统从"被动记录交易"升级为"主动执行运营决策"

## 研究背景与动机

### 领域现状
ERP 系统是现代企业的数字骨干，整合财务、供应链、制造、人力资源等核心业务流程。然而当前 ERP 存在"自动化鸿沟"：
- **交易处理层**已高度自动化（记录订单、更新库存）
- **运营决策层**仍严重依赖人类专家（库存补货、采购决策、异常处理）

传统的基于规则的自动化（RPA）无法处理需要上下文推理的异常情况，而集中式 AI 助手在跨职能协调时面临"上下文污染"和决策退化。

### 核心研究问题
如何构建一个多智能体 LLM 架构，使 ERP 系统能够自主执行跨职能的运营决策，同时保持人类对关键决策的监督？

## 方法概述

### 核心思想
将企业运营决策建模为约束序贯决策问题，通过角色对齐的 LLM Agent 分解复杂性——每个 Agent 负责特定职能（采购、销售、库存等），图编排器协调 Agent 间交互，风险分级机制决定何时升级至人类。

### 方法框架

![[architecture.pdf|800]]

> 图1：Agentic ERP 系统架构 — 展示多智能体组织结构和图编排流程

#### 核心组件

**1. 角色对齐 LLM Agent**
- 每个 Agent 绑定特定企业职能（如采购 Agent、库存 Agent、销售 Agent）
- Agent 具有职能特定的工具集和决策权限
- 角色对齐通过职能特定的系统提示和知识库实现

**2. Graph-Based Orchestrator（图编排器）**
- **Planner**：接收任务，分解为子任务 DAG
- **Executor**：将子任务路由到对应 Agent，收集结果
- **Reflector**：评估执行结果，检测异常和冲突
- **Responder**：生成最终决策或升级请求
- 采用 sprint contract 机制：每个编排周期有明确目标和验收标准

**3. Risk-Tiered Human-in-the-Loop**
- 决策按风险分级：低风险自动执行、中风险请求确认、高风险强制人工审批
- 风险等级由决策金额、影响范围、历史异常率综合计算

![[langgraph_workflow.pdf|600]]

> 图2：LangGraph 工作流 — 展示多 Agent 协作的消息传递和状态转换

## 实验结果

### 评估体系
三层评估设计：

1. **场景任务套件**：标准化的 ERP 运营场景测试
2. **编排范式对比**：六种编排范式在跨职能危机任务上的全面比较
3. **365 天仿真**：agent-in-the-loop 长期运营仿真

### 主要结果

![[yearly_simulation.pdf|600]]

> 图3：年度仿真结果 — 零缺货 vs 规则基线数百次缺货

| 评估层面 | Agentic ERP | 规则 RPA | 无干预基线 |
|----------|:---:|:---:|:---:|
| 场景任务 | **显著优** | 基线 | 不可用 |
| 跨职能危机 | **最优** | 部分 | 不可用 |
| 365天缺货次数 | **0** | 数百次 | 严重 |

![[orchestration_comparison.pdf|600]]

> 图4：六种编排范式对比 — Agentic ERP 的图编排器显著优于其他范式

## 深度分析

### 研究价值评估

| 维度 | 评分 | 说明 |
|------|:---:|------|
| 创新性 | 8/10 | 首次将多智能体 LLM 应用于 ERP 运营决策自动化 |
| 技术质量 | 8/10 | 架构设计合理，三层评估体系严谨 |
| 实验充分性 | 8/10 | 365 天仿真极具说服力，多范式对比全面 |
| 实用性 | 9/10 | 直接面向生产环境，具有极高的企业应用价值 |
| 综合 | **8.2/10** | 企业 AI 自动化的重要探索，架构参考价值高 |

### 方法优势
- **角色对齐降低复杂度**：每个 Agent 只需掌握特定职能知识
- **图编排解耦决策**：Planner-Executor-Reflector-Responder 分离关注点
- **风险分级确保安全**：不是全自动，而是按风险智能升级
- **365 天零缺货**：相对于规则 RPA 的压倒性优势

### 局限性
- 依赖模拟环境，真实生产部署中的边缘情况可能更多
- 仅基于一个 ERP 后端验证，跨系统通用性待确认
- LLM 推理延迟可能影响实时决策场景

## 技术路线定位
本文开创了 **LLM Agent for Enterprise Operations** 新方向，将多智能体编排从对话/游戏领域扩展到企业核心运营系统。

> [!tip] 关键启示
> ERP 的下一步进化不是"更好的规则引擎"，而是"能推理异常情况的多智能体系统"——角色对齐 + 图编排 + 风险分级是三个关键设计原则。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！企业 AI 自动化方向的开创性工作，365 天零缺货的结果令人印象深刻。
