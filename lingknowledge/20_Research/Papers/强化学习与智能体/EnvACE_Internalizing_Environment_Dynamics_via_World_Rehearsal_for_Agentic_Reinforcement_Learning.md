---
date: "2026-08-08"
paper_id: "arXiv:2608.06197"
title: "EnvACE: Internalizing Environment Dynamics via World Rehearsal for Agentic Reinforcement Learning"
authors: "Zishan Xu, Zhiyuan Yao, Yuxin Chen, Yifu Guo, Zhengxi Lu, Yuquan Lu, Jinyang Huang, Yan Xu, Yasheng Wang, Weinan Zhang, Xingshan Zeng, Weiwen Liu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - Agentic-RL
  - LLM-Agent
  - World-Model
  - GRPO
  - Tool-Use
  - Multi-Turn
quality_score: "8.5/10"
created: "2026-08-08"
updated: "2026-08-08"
status: analyzed
---

# EnvACE: Internalizing Environment Dynamics via World Rehearsal for Agentic Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2608.06197
- **作者**：Zishan Xu, Zhiyuan Yao, Yuxin Chen, Yifu Guo, Zhengxi Lu, Yuquan Lu, Jinyang Huang, Yan Xu, Yasheng Wang, Weinan Zhang, Xingshan Zeng, Weiwen Liu
- **机构**：--
- **发布时间**：2026-08-06
- **链接**：[arXiv](http://arxiv.org/abs/2608.06197v1) | [PDF](https://arxiv.org/pdf/2608.06197v1)
- **代码**：https://github.com/Within-yao/EnvACE

## 摘要翻译

### 英文摘要
Training large language model agents for long-horizon tool use typically relies on interactions with real or synthesized executable environments, whose construction and verification are costly, or on external simulators that are difficult to ground. We introduce EnvACE, an agentic reinforcement learning method that replaces external environment interaction during training with **world rehearsal**. The policy alternates between acting and rehearsal: it first generates a tool call, then plays the role of the environment to produce the response induced by that action, and conditions subsequent decisions on the rehearsed response. Both roles are jointly optimized end-to-end using task-success rewards. Through world rehearsal, the policy internalizes the relationship between actions and their environment responses in its parameters, yielding an agent world model that directly supports decision making. Across BFCL-v4, τ²-Bench, VitaBench, and FinMCP-Bench, EnvACE achieves strong and transferable performance, outperforming environment-scaling baselines in the overall evaluation. Controlled studies further show that world rehearsal consistently improves policy learning across model scales. At test time, the internalized world model enables private rehearsal before committed execution, yielding further gains under a moderate rehearsal budget without additional external interaction.

### 中文翻译
训练用于长周期工具使用的大语言模型智能体通常依赖与真实或合成的可执行环境的交互，这些环境的构建和验证成本高昂，要么依赖难以校准的外部模拟器。我们提出 EnvACE，一种将训练时的外部环境交互替换为**世界排练**的智能体强化学习方法。策略在行动和排练之间交替：它首先生成一个工具调用，然后扮演环境的角色产生该动作引发的响应，并基于排练后的响应条件化后续决策。两个角色通过任务成功奖励端到端联合优化。通过世界排练，策略将动作与其环境响应之间的关系内化到参数中，产生直接支持决策的智能体世界模型。在 BFCL-v4、τ²-Bench、VitaBench 和 FinMCP-Bench 上，EnvACE 取得了强且可迁移的性能，在整体评估中优于环境缩放基线。对照研究进一步表明世界排练在各种模型规模上持续改善策略学习。在测试时，内化的世界模型允许在提交执行前进行私有排练，在适度排练预算下无需额外外部交互即可获得进一步提升。

### 核心要点提炼
- **研究背景**：LLM Agent 的 RL 训练通常需要与真实或合成环境交互，环境构建和验证成本高昂
- **研究动机**：消除训练时对外部环境的依赖，让 Agent 通过自排练学习动作-环境关系
- **核心方法**：世界排练（World Rehearsal）—— Actor 和 Environment 两角色交替的端到端 RL 训练
- **主要结果**：在多个 benchmark 上优于环境缩放基线，世界排练在所有模型规模上持续带来收益
- **研究意义**：为 LLM Agent 训练开辟了一条超越外部环境约束的新路径

## 研究背景与动机

### 领域现状
LLM Agent 的训练目前主要依赖两种范式：
1. **环境交互式 RL**（如 GRPO 在可验证任务上）：需要真实的工具执行环境（API 调用、数据库查询等）或合成的模拟环境。BFCL-v4、τ²-Bench 等 benchmark 提供了标准化的评估环境，但训练时的环境成本随任务复杂度呈指数增长。
2. **监督微调（SFT）**：基于高质量轨迹数据进行模仿学习，但受限于数据覆盖率和分布。

### 现有方法的局限性
- **环境构建成本高**：真实执行环境（如 API 沙盒、代码解释器）维护成本高，合成环境的保真度难以保证
- **外部模拟器难以校准**：模拟器与真实环境之间的分布偏移会降低训练效果
- **扩展性受限**：随着工具种类和任务复杂度的增加，环境维护成为瓶颈

### 研究动机
核心动机是**消除 LLM Agent RL 训练对外部环境的依赖**。作者观察到：经过足够预训练的 LLM 已经具备模拟环境响应的基本能力。若能通过"自排练"机制将这种能力系统化地整合到 RL 训练中，就可以突破环境瓶颈。

## 研究问题

### 核心研究问题
**能否通过在 RL 训练中让 LLM 同时扮演 Actor 和 Environment 两个角色，来替代外部环境交互，从而实现可扩展的 Agent 训练？**

具体包括：
1. 世界排练（World Rehearsal）能否有效替代真实环境交互？
2. 双角色联合优化能否产生可迁移的环境理解？
3. 内化的世界模型能否在推理时提供额外收益？

## 方法概述

### 核心思想
EnvACE 的核心创新在于**将环境交互内化为模型的自我排练过程**：同一模型交替扮演"策略"和"环境"两个角色。策略角色生成工具调用（action），环境角色模拟该动作的返回结果，然后基于排练结果继续下一步决策。两个角色通过任务成功奖励端到端联合优化，无需任何外部环境信号。

![[envace_page1.png|800]]

> 图1：EnvACE 整体架构。展示 Actor-Environment 双角色交替训练流程。

### 方法框架

#### 整体架构

EnvACE 的训练流程分为两个交替阶段：

**阶段1：Actor 阶段（行动）**
- 输入：用户查询 + 对话历史
- 策略角色生成工具调用（function call）
- 输出：action（如 API 调用、Python 代码等）

**阶段2：Rehearsal 阶段（环境排练）**
- 输入：用户查询 + 对话历史 + 生成的 action
- 环境角色模拟该 action 的执行结果
- 输出：rehearsed response（模拟的环境返回）

两个阶段的输出被拼接到多轮对话中，最终通过任务成功奖励（verifiable reward）进行端到端优化。

![[method2_page1.png|800]]

> 图2：EnvACE 训练方法的详细流程。

#### 各模块详细说明

**Actor 模块**
- **功能**：基于当前对话状态生成下一步的工具调用
- **输入**：用户查询 + 前序对话历史（含此前排练的环境响应）
- **输出**：function call（工具名 + 参数）
- **关键设计**：Actor 接收排练环境响应作为上下文，逐步构建对工具行为的理解

**Rehearsal（环境排练）模块**
- **功能**：模拟给定工具调用的真实执行结果
- **输入**：用户查询 + 对话历史 + 当前 action
- **输出**：模拟的环境响应
- **关键设计**：与 Actor 共享同一个 LLM 主干，通过奖励信号学习准确模拟环境行为

**联合优化**
- 使用 GRPO（Group Relative Policy Optimization）或其变体作为优化器
- 奖励信号：最终任务是否成功（verifiable reward，如答案正确/API 调用格式正确等）
- 双角色的所有决策通过同一条轨迹共享奖励，实现端到端信用分配

### 世界排练的核心机制

训练时，EnvACE 将每轮交互建模为：

1. 状态 $s_t$ = {查询，历史对话}
2. Actor 采样 action $a_t \sim \pi_\theta(a|s_t)$
3. Environment 采样响应 $r_t \sim \pi_\theta(r|s_t, a_t)$
4. 新状态 $s_{t+1} = s_t \cup \{a_t, r_t\}$
5. 轨迹奖励 $R$ 在交互结束后赋予

通过这种方式，模型参数 $\theta$ 同时编码了"如何行动"和"行动后环境如何响应"的知识，形成了一个**内化的智能体世界模型**。

## 实验结果

### 实验目标
验证 EnvACE 在以下方面的有效性：
1. 对比环境交互式训练基线的性能
2. 世界排练在不同模型规模上的收益
3. 推理时的私有排练效果
4. 跨 benchmark 的迁移能力

### 数据集

| Benchmark | 任务类型 | 评估维度 |
|-----------|----------|----------|
| BFCL-v4 | 函数调用 | API 调用准确性 |
| τ²-Bench | 多步工具使用 | 任务完成率 |
| VitaBench | 生活场景工具使用 | 实用性 |
| FinMCP-Bench | 金融场景工具使用 | 领域迁移 |

### 主要结果

![[share8b_tau2_avg_styled_page1.png|800]]

> 图3：主要实验结果对比。

#### 核心发现
1. **整体性能优势**：EnvACE 在整体评估中优于环境缩放基线
2. **跨模型规模一致**：世界排练在 1.7B 到 8B 参数规模上均持续改善策略学习
3. **推理时增益**：测试时的私有排练（无需外部环境）在适度排练预算下进一步提升性能
4. **迁移性**：EnvACE 训练的策略在不同类型的 benchmark 上展现良好的迁移能力

### 消融实验

![[ablation_tau2_absolute_1p7b_8b_brokeny_v4_page1.png|800]]

> 图4：消融实验结果。

消融研究验证了世界排练的各个组件，包括排练轮次、模型规模的影响等。

### 案例研究

![[Casestudy_page1.png|800]]
![[CasestudyB_page1.png|800]]

> 图5-6：案例研究展示 EnvACE 如何通过世界排练正确模拟环境响应并完成多步工具使用任务。

![[envace_model_size_dumbbell_page1.png|800]]

> 图7：不同模型规模下世界排练的效果对比。

## 深度分析

### 研究价值评估

#### 理论贡献
- **世界排练概念**：将环境交互重新定义为模型的自我排练，这是一个新颖的范式转变
  - 创新点：首次系统化地将 LLM 的生成能力用于替代 RL 训练中的环境交互
  - 学术价值：为 Agent RL 训练的可扩展性提供了理论基础
  - 影响范围：LLM Agent 训练、RL、世界模型、多轮工具使用

- **双角色联合优化框架**：Actor 和 Environment 共享参数端到端训练
  - 创新点：无需额外的 Critic 网络或环境模拟器
  - 简化了 Agent RL 的训练管线

#### 实际应用价值
- **降低 Agent 开发成本**：消除环境构建和维护的工程开销
- **提高训练可扩展性**：训练不再受限于环境并发数
- **推理时自主优化**：测试时可进行私有排练进一步提升性能

### 方法优势详解

1. **环境无关训练**：完全消除对外部环境的依赖
2. **端到端可微分**：双角色联合优化，简化训练管线
3. **推理时增益机制**：内化的世界模型支持私有排练
4. **跨任务迁移**：策略学到的是通用的"行动-环境"关系理解

### 局限性分析

- **环境排练的准确性**：模型模拟的环境响应可能与真实环境存在偏差，在 safety-critical 场景下可能引入风险
- **长周期任务的挑战**：随着任务轮次增加，排练误差可能累积
- **对基础模型能力的依赖**：世界排练的效果在很大程度上依赖于基础 LLM 对工具行为的先验知识
- **评估环境有限**：当前仅在函数调用类 benchmark 上验证，尚未在更广泛的任务类型（如 embodied agent、游戏 agent）上测试

### 适用性与场景分析

**适用场景**：
- 工具使用 API 调用类任务（当前主要验证场景）
- 代码生成与执行类任务（工具行为相对可预测）
- 训练资源受限的场景（无需构建复杂环境）

**不适用场景**：
- 需要精确物理仿真的任务
- 安全性要求极高的领域（医疗、自动驾驶）
- 环境行为高度随机或不可预测的场景

## 与相关论文对比

### 与 GRPO（DeepSeek-R1）的关系
- **继承**：EnvACE 使用 GRPO 风格的群组相对策略优化作为底层 RL 算法
- **扩展**：将单角色 RL 扩展为双角色交替训练，引入了环境排练维度
- **关键差异**：GRPO 需要外部环境提供奖励信号和下一状态，EnvACE 自我生成环境响应

### 与 AgentOPSD (arXiv:2608.05987) 的关系
- **共同目标**：都致力于改进 Agentic RL 的信用分配
- **不同路径**：AgentOPSD 通过递归自蒸馏进行轮次级信用分配；EnvACE 通过双角色排练实现端到端优化
- **互补性**：两者可以结合 —— 在 EnvACE 的排练框架中引入 AgentOPSD 的逐轮细化可能进一步提升性能

### 与世界模型（World Models）的关系
- **概念关联**：EnvACE 内化的环境理解本质上是一个隐式的世界模型
- **差异**：经典世界模型（如 Dreamer）需要显式建模状态转移和奖励函数；EnvACE 通过语言生成隐式编码世界知识

## 技术路线定位

本文属于 **Agentic RL with Internal World Models** 技术路线：

```
GRPO/PPO → Agent RL 训练 → 世界排练（EnvACE）→ 完全自主的 Agent 训练
(外部环境) → (合成环境) → (自我排练) → (零外部依赖)
```

EnvACE 代表了一个关键的范式节点：**从依赖外部信号转向内部自我模拟**。这为后续完全自主的 Agent 训练系统奠定了基础。

## 未来工作建议

1. **扩展到更多工具类型**：代码执行、数据库查询、Web 浏览等
2. **与检索增强结合**：在排练时参考真实工具文档或历史交互记录
3. **排练质量评估**：开发排练响应质量的自动评估指标
4. **多智能体扩展**：多个 Agent 通过交叉排练学习协作

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** — 概念新颖，方法简洁，实验充分，为 Agent RL 训练提供了重要的新方向

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | "世界排练"概念新颖，将 RL 训练中的环境交互问题重新定义为模型自我模拟，具有范式创新意义 |
| 技术质量 | 8/10 | 方法设计简洁优雅，双角色联合优化框架清晰；但排练准确性的理论保证有待加强 |
| 实验充分性 | 8/10 | 在 4 个 benchmark 上验证，含消融和案例分析；但缺少与更强基线（如 RLHF）的对比 |
| 写作质量 | 8/10 | 概念表述清晰，方法描述完整；代码已开源 |
| 实用性 | 8/10 | 对 Agent 开发有直接实用价值；环境排练的可靠性在部分场景下仍需验证 |

### 重点关注
- 世界排练的概念本身——如何将环境交互内化为自我排练
- 双角色联合优化的信用分配机制
- 推理时私有排练的实现和效果

> [!tip] 关键启示
> 世界排练证明：LLM 不仅可以作为策略，还可以作为其自身训练所需的环境。这一洞见可能会改变 Agent RL 训练的范式。

> [!warning] 注意事项
> - 排练的准确性依赖于基础模型的先验知识
> - 长周期任务中排练误差可能累积
> - 当前评估范围有限（主要是函数调用场景）

> [!success] 推荐指数
> ⭐⭐⭐⭐½ 强烈推荐！EnvACE 是 Agentic RL 训练领域的重要进展，概念简洁但影响深远。

## 相关论文

### 直接相关
- [[AgentOPSD_Recursive_Self-Distillation_for_Agentic_Reinforcement_Learning|AgentOPSD]] - 同方向的逐轮信用分配方法
- [[From_Economic_Agents_to_Agentic_Economies_A_Systems_Blueprint_for_Economic_World_Models|Economic World Models]] - 经济世界模型的系统蓝图

### 背景相关
- GRPO（DeepSeek-R1）- 群组相对策略优化
- [[20_Research/Papers/强化学习与智能体/MAGRPO_LLM_Collaboration_With_Multi-Agent_Reinforcement_Learning|MAGRPO]] - 多智能体 GRPO

## 外部资源
- 代码：https://github.com/Within-yao/EnvACE
- arXiv：http://arxiv.org/abs/2608.06197v1
