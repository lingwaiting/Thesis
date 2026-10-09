---
date: "2026-10-09"
paper_id: "2610.10858"
title: "RFChipAgent: Multi-Agentic AI Flow for Analog/RF Chip Design"
authors: "Awani Khodkumbhe, Yunfei Feng, Raj Rangarajan, Kevin Wang, Kamal Sahota"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 智能体
  - Agent
  - 电子设计自动化
  - EDA
quality_score: "10.3/10"
related_papers: []
created: "2026-10-09"
updated: "2026-10-09"
status: analyzed
---

# RFChipAgent: Multi-Agentic AI Flow for Analog/RF Chip Design

## 核心信息
- **论文ID**：2610.10858
- **作者**：Awani Khodkumbhe, Yunfei Feng, Raj Rangarajan, Kevin Wang, Kamal Sahota
- **机构**：--
- **发布时间**：2026-10-07
- **分类**：cs.AR / cs.AI / cs.LG / cs.MA / eess.SY
- **链接**：[arXiv](https://arxiv.org/abs/2610.10858) | [PDF](https://arxiv.org/pdf/2610.10858)

## 研究问题
模拟/射频（Analog/RF）电路是数字计算与物理世界之间的关键接口，Wi-Fi 7 到 6G 等新兴标准对它们提出了苛刻的性能要求。然而，模拟/RF 设计至今仍是芯片开发中最耗时、最依赖人工经验的环节之一。**核心问题**：能否用大语言模型驱动的多智能体（multi-agent）流程，实现端到端的模拟/RF 电路设计自动化？

## 方法概述

### 核心方法

1. **多智能体编排的整体设计流**
   - RFChipAgent 是首个面向端到端模拟/RF 电路设计自动化的 LLM 多智能体流程
   - 多个 AI 智能体在人类监督下协作编排完整设计流

2. **四大技术支柱**
   - **多模态 RAG 子系统**：私有的逐文档 FAISS 索引，从现有工程文档中提取设计知识
   - **拓扑（topology）智能体 + 原理图/测试台（schematic/testbench）智能体**：驱动拓扑选择，并自动化电路与测试台搭建
   - **闭环混合电路尺寸优化引擎**：结合 Tree-structured Parzen Estimator（TPE）与 CMA-ES 优化，在 simulator-in-the-loop 框架下评估每个候选方案
   - **可信度评分仿真数据库**：累积已验证的性能数据，构建自适应优化模型，指导后续迭代

### 方法架构

![[2610.10858_page1.png|600]]

整体架构是一个分层多智能体系统：RAG 子系统负责知识检索，拓扑与原理图智能体负责电路构建，闭环优化引擎负责尺寸优化，可信度数据库负责经验沉淀与反馈。

### 关键创新

1. **首个 LLM 多智能体 EDA 流程** - 将多智能体协作范式引入模拟/RF 电路设计，此前该领域高度依赖专家手工设计
2. **simulator-in-the-loop 闭环优化** - 每个候选方案都在真实仿真器中验证，而非黑盒预测，保证 signoff 级质量
3. **可信度评分数据库** - 用累积的仿真数据自适应改进优化模型，实现"越用越聪明"

## 实验结果

### 数据集/验证平台
- 在 **GF22FDSOI 60 GHz 宽带毫米波低噪声放大器（LNA）** 拓扑族上验证
- 覆盖自动化拓扑生成、规格驱动设计空间探索、仿真器引导优化

### 主要结果
- 实现自动化拓扑生成、规格驱动设计空间探索
- 在保持 **signoff 级验证质量** 的前提下，显著降低设计工作量
- 为 LLM 驱动的模拟/RF 多智能体电子设计自动化（EDA）奠定基础

## 深度分析

**价值**：本文是 LLM 智能体向硬件设计（EDA）领域渗透的代表性工作。模拟/RF 设计是公认的"设计自动化孤岛"，长期依赖资深工程师的经验与直觉。RFChipAgent 用"知识检索 + 多智能体协作 + 闭环仿真优化 + 经验数据库"的组合，把这类高度非结构化的设计任务结构化，是 agentic AI 进入芯片设计前端的标志性尝试。

**局限与风险**：
- 验证范围聚焦单一工艺（GF22FDSOI）与单一电路类型（60 GHz LNA），泛化性待验证
- 摘要未披露与人类专家设计质量的量化对比，仅强调"减少工作量"
- 多智能体编排的可靠性、可复现性（能否稳定产出可 signoff 的版图）是关键工程挑战

## 相关论文对比
- 与 [[NavGPT-3_Harnessing_Context_in_a_Hierarchical_Navigation_Runtime|NavGPT-3]] 同为"LLM 智能体 + 物理/工程实体"的结合，但 RFChipAgent 面向 EDA 设计流，NavGPT-3 面向具身导航
- 与 [[On_the_Clock_Towards_Punctual_and_Productive_Time-Budgeted_AI_Agents|On the Clock]] 同属 agentic 工作流研究，但侧重点不同（设计自动化 vs 时间预算约束）
