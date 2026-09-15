---
date: "2026-09-15"
paper_id: "arXiv:2609.15066"
title: "Salesforce Koa: An Enterprise Language Model for Agentic Tool Use"
authors: "Zixiang Chen, Sufeng Niu, Yingchi Liu, Wenting Zhao, Akshara Prabhakar, Shubham Mehrotra, et al."
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Agent
  - Tool-Use
  - Function-Calling
  - GRPO
  - Reinforcement-Learning
  - Enterprise-AI
quality_score: "8.0/10"
related_papers: []
created: "2026-09-15"
updated: "2026-09-15"
status: analyzed
---

# Salesforce Koa: An Enterprise Language Model for Agentic Tool Use

## 核心信息
- **论文ID**：arXiv:2609.15066
- **作者**：Zixiang Chen, Sufeng Niu, Yingchi Liu 等（Salesforce Agentforce & AI Research）
- **机构**：Salesforce Agentforce & AI Research（与 NVIDIA 合作训练）
- **发布时间**：2026-09-14
- **会议/期刊**：arXiv 预印本（技术报告，cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2609.15066) | [PDF](https://arxiv.org/pdf/2609.15066)
- **引用**：--

## 摘要翻译

### 英文摘要
We present Salesforce Koa, an enterprise language model built by post-training the open-weight Nemotron-3-Super-120B foundation model with reinforcement learning using Group Relative Policy Optimization (GRPO). Trained on public and synthetically generated data (no customer data), its distinctive component is a simulation-to-reward pipeline that expands workflow specifications into persona-conditioned multi-turn tasks with task-resolution rewards grounded in successful tool use. Across public tool-use, agentic-reasoning, and enterprise CRM benchmarks, Salesforce Koa improves over its open-weight base, with the clearest gains on multi-turn tool use, and surpasses a strong proprietary baseline while remaining below the strongest frontier models.

### 中文翻译
作者提出 **Salesforce Koa**，一款通过 GRPO（Group Relative Policy Optimization，组相对策略优化）强化学习对开源权重 Nemotron-3-Super-120B 基础模型进行后训练得到的企业语言模型。其训练仅使用公开与合成数据（无客户数据），特色组件是一条「仿真到奖励」管线：将工作流规范扩展为以 persona 为条件的多轮任务，奖励基于成功工具调用（task-resolution reward）。在公开工具使用、智能体推理与企业 CRM 基准上，Koa 均超越其开源基础模型，其中多轮工具使用的提升最明显，并超越强专有基线（GPT-4.1），但仍低于最强前沿模型。

### 核心要点提炼
- **研究背景**：企业 AI 需要稳健的工具调用与智能体推理，但企业希望更多控制部署/定制/治理/成本。
- **研究动机**：开源权重模型能否被有效专业化到企业智能体任务，同时保留通用能力？
- **核心方法**：规格驱动（spec-driven）RL——用 Agent Script 声明式规范构建仿真环境与接地奖励，驱动 GRPO。
- **主要结果**：Tau2Bench 69.41、BFCL 66.63%、CRM Bench 0.86，均超基线与 GPT-4.1；RL 显著提升多轮工具使用，SFT 增益有限。
- **研究意义**：证明「规格驱动 RL」是专业化开源基础模型到企业智能体任务的实用路径。

## 研究背景与动机

### 领域现状
LLM 日益驱动企业 AI 系统，需要调用外部工具、检索结构化信息、完成多步工作流。开源权重基础模型（Llama、Gemma、Qwen、Mistral、Nemotron）已在公开基准上逼近前沿，且权重可访问使企业能针对自身领域后训练。

### 现有方法的局限性
1. 多数后训练（SFT、RLHF、GRPO）面向通用推理，而企业应用需针对组织特定的 schema、API 与策略操作。
2. SFT（行为克隆）在已重度后训练的基线上，对多轮工具使用增益有限。
3. 缺少将「智能体编写（agent authoring）」与「模型后训练」直接打通的方法。

### 研究动机
核心问题：能否将开源基础模型有效专业化到企业智能体任务，同时保留通用能力？作者提出用声明式 Agent Script 规范同时驱动环境构建与奖励定义，建立「智能体编写 → 模型后训练」的直接桥梁。

## 研究问题

### 核心研究问题
1. 如何构建能产生接地（grounded）任务解决奖励的企业智能体训练环境？
2. 规格驱动 RL 能否在公开工具使用与企业 CRM 基准上超越基线与专有基线？
3. 与 SFT 相比，RL 对多轮工具使用的增益如何？

## 方法概述

### 核心思想
**规格驱动 RL**：企业工作流用 Agent Script（Salesforce 声明式语言）编写为规范，静态提取为「带类型工作流图」实例化可执行环境；仿真管线将规范扩展为 persona 条件多轮任务；NeMo Gym 在线 rollout，用 coverage judge 产生接地奖励，驱动 GRPO。

### 方法框架

#### 整体架构
单个冻结辅助模型在每次 rollout 中扮演三个角色：客户模拟器（从 persona 生成下一用户轮）、工具/函数模拟器（无生产后端时产生工具输出）、coverage 评判器（对解决情况评分形成奖励）。辅助模型是独立推理服务，永不参与策略优化。

![[2609.15066_fig1.png|800]]

> 图1：一次训练 rollout 的端到端流程。实线为训练/rollout 路径，虚线为冻结辅助响应。策略（左）是唯一被训练的组件，奖励由 coverage 评判器在末尾产生。

#### 各模块详细说明

**模块1：规格驱动环境与任务构建**
- **功能**：将 Agent Script 规范编译为可执行环境。
- **输入**：规范（router、子智能体、类型化 action、工具作用域、NL 推理指令）。
- **输出**：带类型工作流图（工具参数 schema、状态效应、路由条件、终止配置）→ 可执行环境。
- **仿真**：每个规范扩展为 scenario- 与 persona- 条件会话，可从前缀或开场请求开始，暴露不同深度的多轮决策。

**模块2：在线 rollout 与接地奖励**
- **功能**：执行 rollout 并产生奖励。
- **奖励**：coverage rate $\text{cov} = \frac{\#\{sub-questions\ resolved\}}{\#\{sub-questions\}}$，标量奖励 $R = 0$（出现连续重复工具调用）否则 $R=\text{cov}$。
- **接地性**：需要客户/系统特定数据的子问题，仅当答案基于成功的相关工具调用才标记 resolved。
- **确定性沙箱**：$R=1$ 当预测动作复现参考终态，否则 0。

**模块3：GRPO 优化**
- **功能**：最大化期望轨迹奖励 $J(\theta)=\mathbb{E}_{x\sim D}\mathbb{E}_{\tau\sim\pi_\theta(\cdot|x,env)}[R(\tau)]$。
- **关键技术**：leave-one-out 组基线（无价值网络）、动态采样（丢弃零方差组）、token 级截断重要性采样权重修正 train/generation log-prob 失配、单次 on-policy 更新。

**模块4：约束决策轮的单步训练（PivotRL 启发）**
- **功能**：针对「必须与用户沟通而非调用工具」的罕见但决定性轮次。
- **奖励**：$R_{pivot} = G(a_k) \times f_{match}$，其中程序门 $G(a_k)$ 在出现状态改变调用时清零，$f_{match}$ 评估恢复轮是否正确发出被隐藏的工具调用。

### 关键创新
1. **规格驱动 RL**：同一 Agent Script 规范既配置智能体，又结构化 rollout 任务与解决标准，打通智能体编写与模型后训练。
2. **接地任务解决奖励**：覆盖评判器强制「数据相关子问题须有成功工具调用支撑」。
3. **冻结辅助模型三合一**：客户模拟/工具模拟/coverage 评判由单一冻结模型承担，降低数据并行协调风险。

## 实验结果

### 数据集/基准
- **Tau2Bench**：多轮客户服务（航空 50、零售 114、电信 114 任务），GPT-4.1 用户模拟器，4 次试验 pass^1。
- **BFCL**：智能体工具使用（多步调用、网页搜索、记忆、有状态工具）。
- **CRM Bench**：单轮 Salesforce/Agentforce 工作流（主题/函数调用/自由文本准确率）。

### 基线方法
Claude Opus 4.8、OpenAI GPT-5.5、GPT-4.1、Nemotron-3-Super-120B（基座）。

### 主要结果
- **Tau2Bench**：加权平均 69.41，超基座（68.64），超 GPT-4.1（54.48）14.9 分。
- **BFCL**：66.63%，超基座（64.73）与 GPT-4.1（53.96），但低于最强专有模型。
- **CRM Bench**：0.86，接近 Opus-4.8（0.87），超 GPT-4.1（0.81）与基座（0.84）；函数调用准确率 0.77 超基座（0.71）。
- **SFT vs RL 消融**：RL 显著提升 BFCL 多轮（54.12→59.50），SFT 反而略降（→53.25）；SFT 增益集中在记忆/相关性检测等单轮类别。

### 结果分析
- 规格驱动企业 RL 在公开与企业基准上均超基座，多轮工具使用增益最清晰。
- 关键限制：基座本身已是 RL 后训练过的，SFT/RL 对比结论可能不适用于 pre-RL 检查点。

## 深度分析

### 研究价值
- **理论贡献**：建立「声明式智能体规范 ↔ 训练任务与奖励」的对应关系。
- **实际应用**：为企业在自有数据/规范上专业化开源模型提供可落地方案。
- **领域影响**：为「spec-driven RL」用于企业智能体提供了首个规模化实证。

### 优势
1. 无客户数据，仅公开+合成数据，隐私合规。
2. 接地奖励设计（duplicate-call gate + 数据相关问题须工具支撑）抑制奖励黑客。
3. 工程细节完整（分布式训练、奖励消融、scale-dependent 稳定性）。

### 局限性
1. 基座已 RL 后训练，SFT/RL 对比受限于此起点。
2. 仍低于最强前沿模型（Opus-4.8、GPT-5.5）。
3. 评测集中于工具调用，未覆盖更广泛的智能体能力。

### 适用场景
- 企业 CRM/客服智能体的模型专业化。
- 需在自有 schema/API 上定制开源模型的组织。

## 与相关论文对比

### [[APIGen|APIGen]] - 延伸关系
- **差异**：APIGen 是函数调用数据生成管线（SFT 导向）。
- **改进**：Koa 将其扩展为在线 RL 的环境与奖励，且强调多轮。

### [[APIGen-MT|APIGen-MT]] - 延伸关系
- **差异**：APIGen-MT 做多轮数据生成。
- **改进**：Koa 用规格驱动 + 接地奖励做 RL，而非仅 SFT。

### [[PivotRL|PivotRL]] - 借鉴关系
- **差异**：PivotRL 定位高准确率智能体后训练的约束决策轮。
- **改进**：Koa 借鉴其单步训练思路，融入约束决策轮的 gate-then-score 奖励。

## 技术路线定位

本文属于「LLM 智能体工具使用后训练」技术路线，子方向为「企业智能体的 RL 专业化」。核心特点：规格驱动环境构建、接地任务解决奖励、GRPO 在线 rollout。

发展历程：`APIGen/APIGen-MT（数据生成）→ PivotRL（约束轮）→ 本文 Salesforce Koa → pre-RL 基座上的联合 SFT+RL`

## 未来工作建议
1. **作者建议**：SFT 与 RL 阶段联合设计、从 pre-RL 基座评估、扩展到更广企业域与更复杂智能体工作流。
2. **延伸建议**：探索自适应 coverage 评判器、跨域规范泛化、更长 horizon 的多智能体协作。

## 我的综合评价

### 价值评分
- **总体评分**：**8.0/10** — 工程扎实、接地奖励设计值得借鉴的企业智能体 RL 后训练报告。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 规格驱动 RL 有新意，但非颠覆性范式 |
| 技术质量 | 8/10 | 奖励设计、分布式训练、消融均详实 |
| 实验充分性 | 7/10 | 三个基准 + SFT/RL 消融，但缺更多专有基线 |
| 写作质量 | 8/10 | 技术报告结构清晰 |
| 实用性 | 9/10 | 直接面向企业落地，无客户数据合规 |

### 突出亮点
- 接地奖励 + duplicate-call gate 抑制奖励黑客。
- 「智能体编写即训练任务定义」的统一范式。
- RL vs SFT 在多轮工具使用上的清晰区分。

### 重点关注
- coverage 评判器如何保证数据相关子问题的接地性。
- 奖励设计消融（Fig 3）与 scale-dependent 稳定性（Fig 4）。

### 可借鉴点
- 冻结辅助模型三合一（模拟/仿真/评判）的架构。
- 接地奖励「数据相关子问题须工具支撑」的设计原则。

### 批判性思考
- 基座已 RL 后训练，削弱了「RL 优于 SFT」结论的普适性。
- 评测仅面向 Salesforce 生态的 CRM，跨域泛化存疑。

## 我的笔记

%% 用户可在此添加阅读笔记 %%

## 相关论文
- [[APIGen|APIGen]] - 延伸关系
- [[APIGen-MT|APIGen-MT]] - 延伸关系
- [[PivotRL|PivotRL]] - 借鉴关系

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.15066)
- [Agent Script 文档](https://developer.salesforce.com/docs/ai/agentforce/guide/agent-script.html)

> [!tip] 关键启示
> 让「智能体编写规范」同时成为「训练任务与奖励的定义」，是专业化开源模型到企业智能体任务的高效路径。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 企业智能体、工具调用后训练研究者值得一读，接地奖励与规格驱动 RL 的工程实践尤其有参考价值。
