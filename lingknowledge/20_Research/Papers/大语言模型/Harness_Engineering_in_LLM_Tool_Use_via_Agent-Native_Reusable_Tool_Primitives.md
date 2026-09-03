---
date: "2026-09-03"
paper_id: "arXiv:2609.01736"
title: "Harness Engineering in LLM Tool Use via Agent-Native Reusable Tool Primitives"
authors: "Haibo Jin, Suijin Wang, Xucheng Yu, Haojing Luo, Haohan Wang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Agent
  - 工具调用
  - Tool-Use
  - Agent-Native
quality_score: "8.5/10"
created: "2026-09-03"
updated: "2026-09-03"
status: analyzed
---

# Harness Engineering in LLM Tool Use via Agent-Native Reusable Tool Primitives

## 核心信息
- **论文ID**：arXiv:2609.01736
- **作者**：Haibo Jin, Suijin Wang, Xucheng Yu, Haojing Luo, Haohan Wang
- **机构**：--
- **发布时间**：2026-09-01
- **会议/期刊**：--（arXiv 预印本，cs.SE / cs.AI / cs.CL / cs.LG / cs.MA）
- **链接**：[arXiv](http://arxiv.org/abs/2609.01736v1) | [PDF](https://arxiv.org/pdf/2609.01736v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language models (LLMs) augmented with external tools have demonstrated remarkable capability in solving complex real-world tasks. However, existing approaches suffer from two key challenges: brittle multi-step and multi-turn reasoning caused by incompatible tool output types and API schemas, and performance degradation under large tool catalogues. To address these, we introduce **Tool Primitives**, a design that replaces rigid API schema-based invocation with natural language as the interface for tool calling, where each tool is wrapped with an LLM interface that handles schema resolution and execution internally, enabling natural inter-tool communication for nested and multi-turn tool calling. Building on Tool Primitives, we host **ToolFace**, a centralized repository of 25,519 functions from which LLMs dynamically retrieve only the relevant tools at inference time, eliminating the need to enumerate raw API schemas in context. To orchestrate Tool Primitives and ToolFace reliably in complex settings, we further propose **HEART**, a **H**arness **E**ngineering framework via **A**gent-native, **R**eusable **T**ool Primitives, comprising a Planner, Router, and Verifier that jointly support dynamic tool invocation planning, multi-step execution, and feedback-driven recovery.

Experiments on five benchmarks demonstrate that HEART outperforms SFT-based models by 10% on average and surpasses GPT-5.4, Claude-4.6-Sonnet, and Gemini-3.1-Pro by 6% on average while reducing API cost by up to 85%. On 50 real-world tasks, HEART achieves 84% task completion, 3.8× the average of three frontier commercial models (22%).

### 中文翻译
借助外部工具增强的大语言模型（LLM）在解决复杂现实任务方面展现了卓越的能力。然而，现有方法面临两大挑战：一是由于工具输出类型与 API schema 不兼容导致的脆弱多步、多轮推理；二是在大型工具目录下的性能退化。为解决这些问题，作者提出 **Tool Primitives（工具原语）**——一种用自然语言取代刚性 API schema 调用接口的设计，每个工具都包裹在一个负责 schema 解析与执行的 LLM 接口内，从而支持嵌套与多轮工具调用之间的自然工具间通信。在 Tool Primitives 之上，作者构建了 **ToolFace**，一个包含 25,519 个函数的集中式仓库，推理时 LLM 仅动态检索相关工具，避免在上下文中枚举全部原始 API schema。为进一步在复杂场景中可靠地编排 Tool Primitives 与 ToolFace，作者提出 **HEART**（通过 Agent 原生、可复用工具原语进行的 Harness Engineering 框架），由 Planner、Router 和 Verifier 三个组件构成，协同支持动态工具调用规划、多步执行与反馈驱动的恢复。

在五个基准上的实验表明，HEART 平均比 SFT 基模型高出 10%，比 GPT-5.4、Claude-4.6-Sonnet 和 Gemini-3.1-Pro 平均高出 6%，同时将 API 成本降低多达 85%。在 50 个现实世界任务上，HEART 达到 84% 的任务完成率，是三家前沿商业模型平均水平（22%）的 3.8 倍。

### 核心要点提炼
- **研究背景**：LLM 工具调用面临 schema 不兼容导致的多步推理脆弱，以及工具目录过大导致性能退化。
- **研究动机**：用自然语言作为工具调用接口，消除 schema 刚性约束，并实现按需检索工具。
- **核心方法**：Tool Primitives（LLM 包裹的工具原语）+ ToolFace（集中式工具仓库）+ HEART（Planner/Router/Verifier 编排框架）。
- **主要结果**：五个基准上超 SFT 模型 10%、超前沿商业模型 6%，API 成本降低 85%，现实任务完成率 84%。
- **研究意义**：为 agent-native 工具使用提出一套可复用的"harness engineering"范式。

## 研究背景与动机

### 领域现状
LLM + 工具（function calling / tool use）已成为解决复杂现实任务的标配路径，常见范式包括 OpenAI function calling、ReAct、以及各类 agent 框架。这些方法普遍要求以严格的 JSON schema 描述工具，并把工具列表塞进上下文。

### 现有方法的局限性
1. **Schema 刚性**：工具输出类型与 API schema 不兼容时，多步、多轮推理变得脆弱，容易中途失败。
2. **上下文膨胀**：大型工具目录（成千上万个函数）需要全部枚举进上下文，既贵又伤性能。
3. **工具间通信困难**：嵌套调用、多轮调用之间缺乏自然的接口衔接。

### 研究动机
作者希望把工具调用从"刚性 API schema"中解放出来，用自然语言作为统一接口，并让 LLM 在推理时按需检索工具，从而在复杂任务中可靠、低成本地编排工具。

## 研究问题

### 核心研究问题
如何在大型工具目录下，实现可靠、低成本、可复用的 LLM 工具调用，从而支持复杂多步、多轮任务？

## 方法概述

### 核心思想
把"工具"从"需要 LLM 理解原始 API schema 的被动资源"转变为"自带 LLM 接口、能用自然语言自描述、可互相通信的主动原语"，再通过 Planner/Router/Verifier 三组件可靠编排。

### 方法框架

#### 整体架构
![[toolprimitives0506_2_page1.png|800]]

> 图1：HEART 框架整体架构，展示 Tool Primitives、ToolFace 与 Planner/Router/Verifier 三组件之间的关系。

#### 各模块详细说明

**模块1：Tool Primitives（工具原语）**
- **功能**：用自然语言作为工具调用接口，每个工具包裹在 LLM 接口内。
- **处理流程**：LLM 接口负责 schema 解析与执行，屏蔽底层 API 差异。
- **关键技术**：agent-native 封装，支持嵌套与多轮调用的工具间通信。

**模块2：ToolFace（集中式工具仓库）**
- **功能**：托管 25,519 个函数，推理时按需检索相关工具。
- **输入**：自然语言查询；**输出**：仅相关工具子集。
- **关键技术**：动态工具检索，避免在上下文中枚举全部 schema。

**模块3：HEART 编排框架**
- **Planner**：动态工具调用规划。
- **Router**：路由到合适的工具/执行路径。
- **Verifier**：反馈驱动的执行验证与恢复。

## 实验结果

### 数据集 / 基准
五个 benchmark（涉及工具调用与多步推理），另有 50 个现实世界任务。

### 主要结果
| 对比项 | 结果 |
|--------|------|
| 超 SFT 基模型 | 平均 +10% |
| 超 GPT-5.4 / Claude-4.6-Sonnet / Gemini-3.1-Pro | 平均 +6% |
| API 成本 | 降低最多 85% |
| 现实世界任务完成率 | 84%（前沿商业模型平均 22%，约 3.8×） |

## 深度分析

### 研究价值评估
- **理论贡献**：提出"Tool Primitives"与"harness engineering"概念，把工具调用从 schema 层抽象到自然语言层。
- **实际应用**：对大型工具目录、多步 agent 任务、成本敏感场景有直接价值。

### 方法优势
1. **去 schema 刚性**：自然语言接口天然兼容，消除 schema 不匹配导致的脆弱性。
2. **按需检索**：ToolFace 显著降低上下文开销与 API 成本。
3. **可靠编排**：Planner/Router/Verifier 提供反馈驱动的失败恢复。

### 局限性
- 缺乏会议/期刊背书，目前为 arXiv 预印本，需关注后续评审。
- 25,519 个函数的 ToolFace 仓库的覆盖范围与维护成本未充分讨论。

## 技术路线定位
本文属于 **LLM agent / tool use** 路线，从"function calling schema"走向"agent-native 可复用工具原语"，与 ReAct、Toolformer、function calling 等工作一脉相承，但在抽象层次（自然语言接口）与规模化（集中式仓库 + 动态检索）上有明显推进。

## 未来工作建议
1. 将 ToolFace 扩展至更广泛、跨领域的工具生态。
2. 探索 Tool Primitives 在多 agent 协作与自我改进（self-improvement）场景中的应用。
3. 补充开源代码与更大规模现实任务的验证。

## 我的综合评价

### 价值评分
**8.5/10** — 概念清晰、工程价值突出、实验对比前沿模型，但作为预印本仍需后续评审验证。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "Tool Primitives + harness engineering"概念有辨识度 |
| 技术质量 | 8/10 | 三组件编排框架完整 |
| 实验充分性 | 8/10 | 五个基准 + 50 现实任务，对比前沿商业模型 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 9/10 | 成本降低 85%、完成率 3.8×，落地价值高 |

> [!tip] 关键启示
> 把工具抽象为"自带 LLM 接口、自然语言自描述的原语"，再以 Planner/Router/Verifier 可靠编排，是缓解 schema 刚性与上下文膨胀两条腿走路的有效范式。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐关注——面向 LLM 工具调用/agent 方向，工程与概念价值兼具。
