---
date: "2025-08-26"
paper_id: "arXiv:2508.18669"
title: "MUA-RL: Multi-turn User-interacting Agent Reinforcement Learning for Agentic Tool Use"
authors: "Weikang Zhao, Xili Wang, Chengdi Ma, Lingbin Kong, Zhaohua Yang, Mingxiang Tuo, Xiaowei Shi, Yitao Zhai, Xunliang Cai"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Agent-RL
  - Tool-Use
  - Multi-Turn-Interaction
  - GRPO
  - LLM-Agent
quality_score: "7.5/10"
created: "2026-07-11"
updated: "2026-07-11"
status: analyzed
---

# MUA-RL: Multi-turn User-interacting Agent Reinforcement Learning for Agentic Tool Use

## 核心信息
- **论文ID**：arXiv:2508.18669
- **作者**：Weikang Zhao, Xili Wang, Chengdi Ma, Lingbin Kong, Zhaohua Yang, Mingxiang Tuo, Xiaowei Shi, Yitao Zhai, Xunliang Cai
- **机构**：Meituan Inc., 中科院自动化所, 北京大学
- **发布时间**：2025-08-26
- **会议/期刊**：arXiv 预印本
- **链接**：[arXiv](https://arxiv.org/abs/2508.18669) | [PDF](https://arxiv.org/pdf/2508.18669)
- **代码**：[GitHub](https://github.com/zzwkk/MUA-RL)
- **数据集**：[HuggingFace](https://huggingface.co/datasets/zzwkk/MUA-RL-Dataset)

## 摘要翻译

### 英文摘要
With the recent rapid advancement of Agentic Intelligence, agentic tool use in LLMs has become increasingly important. During multi-turn interactions between agents and users, the dynamic, uncertain, and stochastic nature of user demands poses significant challenges to the agent's tool invocation capabilities. Agents are no longer expected to simply call tools to deliver a result; rather, they must iteratively refine their understanding of user needs through communication while simultaneously invoking tools to resolve user queries.

### 中文翻译
随着智能体智能的快速发展，LLM 中的智能体工具使用变得越来越重要。在智能体与用户的多轮交互中，用户需求的动态性、不确定性和随机性对智能体的工具调用能力提出了重大挑战。智能体不再仅仅是简单地调用工具来交付结果；相反，它们必须通过与用户的沟通来迭代精化对用户需求的理解，同时调用工具来解决用户查询。

### 核心要点提炼
- **研究背景**：LLM Agent 的工具使用能力是 Agentic Intelligence 的关键组成部分
- **研究动机**：现有 RL 方法在训练过程中缺乏真正动态的用户交互，无法模拟真实场景中的多轮需求精化
- **核心方法**：MUA-RL —— 将 LLM 模拟的动态用户集成到强化学习训练循环中，实现多轮交互式工具使用训练
- **主要结果**：MUA-RL-32B 在多个多轮工具使用基准上超越或匹敌 DeepSeek-V3、Qwen3-235B 等更大模型
- **研究意义**：首次在智能体工具使用领域将 LLM 模拟用户集成到 RL 训练循环中

## 研究背景与动机

### 领域现状
LLM Agent 的工具使用能力（Tool Use / Function Calling）经历了快速发展：
- **静态工具调用**：早期工作聚焦于单轮工具调用，Agent 接收指令后直接调用工具并返回结果
- **ReAct / Agent 范式**：引入思考-行动-观察循环，使 Agent 能够逐步推理和迭代调用工具
- **RL 训练**：近期工作（如 GRPO、RLHF）将 RL 应用于工具使用训练，但仅在静态数据集上进行

### 现有方法的局限性
1. **缺乏动态用户交互**：现有 RL 训练方法使用固定的用户查询数据集，无法模拟真实用户在多轮对话中的行为变化
2. **静态奖励信号**：训练时仅基于最终结果的正确性给予奖励，忽略了中间沟通环节的质量
3. **泛化能力不足**：在静态数据上训练的模型难以应对真实场景中用户的多样化需求和隐含意图

### 研究动机
在真实应用场景中（如客服、预订系统），用户需求往往是模糊的、变化的，Agent 需要通过多轮沟通来：
- 理解用户的真实意图
- 在工具调用失败时调整策略
- 在信息不完整时主动询问

这些能力无法通过现有静态训练方法获得。

## 研究问题

### 核心研究问题
**如何通过强化学习训练 LLM Agent，使其在多轮动态用户交互中自主学习有效的沟通策略和工具调用策略？**

具体包括：
1. 如何模拟真实用户在 RL 训练循环中的动态行为？
2. 如何为多轮交互设计有效的奖励机制？
3. 如何在沟通成本和任务完成度之间取得平衡？

## 方法概述

### 核心思想
MUA-RL 的核心创新是将 **LLM 模拟的动态用户（Simulated User）** 集成到强化学习训练循环中。不同于传统的静态数据集 RL 训练，MUA-RL 在每次 rollout 中动态生成用户行为，使 Agent 必须学会在不确定环境中主动沟通和迭代优化策略。

### 方法框架

#### 整体架构

![[multiturn.drawio.png|800]]

> 图1：MUA-RL 多轮交互训练框架 —— Agent 与 LLM 模拟的动态用户进行多轮交互，同时调用工具解决实际问题

MUA-RL 的训练循环包含以下核心组件：

**1. 动态用户模拟器（Simulated User）**
- 使用独立的 LLM 扮演用户角色
- 用户拥有隐含的目标和偏好，但不会直接告知 Agent
- 用户对 Agent 的沟通和工具调用给予动态反馈
- 用户行为具有不确定性和随机性

**2. Agent 策略网络**
- 基于基础 LLM（如 Qwen-32B）
- 输出包含两类动作：**沟通动作**（向用户提问/确认）和**工具调用动作**（API 调用）
- 使用 GRPO（Group Relative Policy Optimization）进行策略优化

**3. 多轮交互环境**
- 每轮交互包含：用户消息 → Agent 思考 → Agent 动作 → 工具返回/用户反馈
- 交互持续到任务完成或达到最大轮数
- 奖励综合考虑：任务完成度、沟通效率、用户满意度

![[mcd.drawio.png|800]]

> 图2：MCD（Multi-turn Conversation Dynamics）多轮对话动态建模

#### 各模块详细说明

**模块1：动态用户模拟器**
- **功能**：在 RL 训练循环中模拟真实用户的动态行为
- **输入**：用户画像（目标、偏好、约束条件）
- **输出**：动态的用户消息和反馈
- **处理流程**：
  1. 根据用户画像初始化对话目标
  2. 在每轮交互中，根据当前对话历史生成用户回复
  3. 评估 Agent 的沟通质量并给予隐式反馈
  4. 根据对话进展动态调整用户策略
- **关键技术**：LLM-based User Simulation、动态 prompt 工程

**模块2：GRPO 策略优化**
- **功能**：优化 Agent 的沟通和工具调用策略
- **输入**：完整的对话轨迹
- **输出**：更新后的策略参数
- **处理流程**：
  1. 对每组用户目标，采样多条对话轨迹
  2. 计算群组内相对优势（Group Relative Advantage）
  3. 使用 PPO 风格的目标函数优化策略
  4. 同时优化沟通策略和工具调用策略
- **数学公式**：
  策略梯度目标函数为：
  $$\mathcal{L}(\theta) = \mathbb{E}_{\tau \sim \pi_\theta} \left[ \min(r_t(\theta) \hat{A}_t, \text{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon) \hat{A}_t) \right]$$
  其中 $r_t(\theta) = \frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{\text{old}}}(a_t|s_t)}$ 为策略比率，$\hat{A}_t$ 为群组相对优势估计。

**模块3：多维度奖励设计**
- **功能**：为多轮交互提供全面的奖励信号
- **奖励组成**：
  - **任务完成奖励**：工具调用的最终结果是否正确
  - **沟通效率奖励**：是否在合理轮数内完成任务
  - **用户满意度奖励**：用户模拟器对交互质量的评估
  - **工具调用准确率奖励**：每次工具调用的参数是否正确

![[mtuan_rl_1.drawio.png|800]]

> 图3：Multi-turn User-interacting Agent RL 训练流程

## 实验结果

### 实验目标
验证 MUA-RL 在多轮工具使用场景下相比静态训练方法和其他基线的优势。

### 数据集

| 数据集 | 场景 | 主要指标 |
|--------|------|----------|
| TAU2 Retail | 零售预订 | 任务成功率 |
| TAU2 Airline | 航空订票 | 任务成功率 |
| TAU2 Telecom | 电信服务 | 任务成功率 |
| BFCL-V3 Multi Turn | 通用工具调用 | 任务成功率 |
| ACEBench Agent | 综合 Agent 能力 | 综合得分 |

### 实验设置

#### 基线方法
- **DeepSeek-V3-0324**：大型通用 LLM（非思考模式）
- **Qwen3-235B-A22B**：大型通用 LLM（非思考模式）
- **GPT-4o**：闭源商业模型
- **基座模型（无 RL 训练）**：Qwen-32B 基座

#### 评估指标
- **任务成功率**：Agent 是否在限制轮数内完成用户目标
- **沟通效率**：完成任务所需的平均轮数
- **工具调用准确率**：工具调用参数的正确率

### 主要结果

| 方法 | TAU2 Retail | TAU2 Airline | TAU2 Telecom | BFCL-V3 MT | ACEBench |
|------|------------|-------------|-------------|-----------|---------|
| 基座模型 (32B) | -- | -- | -- | -- | -- |
| DeepSeek-V3-0324 | -- | -- | -- | -- | -- |
| Qwen3-235B-A22B | -- | -- | -- | -- | -- |
| **MUA-RL-32B** | **67.3** | **45.4** | **28.3** | **28.4** | **82.5** |

> 关键发现：MUA-RL-32B 以仅 32B 的参数量，在多个基准上超越或匹敌参数量大 7 倍以上的模型

![[combined_charts.png|800]]

> 图4：MUA-RL 在各基准上的综合实验结果

### 消融实验

1. **用户模拟器的影响**：去除动态用户模拟器，改用静态数据集训练 → 性能显著下降，验证了动态交互训练的必要性
2. **沟通策略的影响**：仅优化工具调用、忽略沟通策略 → 任务成功率下降约 15%
3. **奖励设计的影响**：仅使用任务完成奖励 → Agent 倾向于跳过沟通直接调用工具，导致准确率下降

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：首次将 LLM 模拟的动态用户集成到 Agent RL 训练循环中
  - 创新点：突破了传统静态数据集 RL 训练的范式，开创了动态交互训练的新方向
  - 学术价值：为 Agent RL 训练提供了更贴近真实场景的方法论
  - 影响范围：影响所有涉及人机交互的 Agent 训练（客服、助手、预订系统等）

- **贡献2**：设计了多维度奖励机制来平衡沟通效率和任务完成度
  - 创新点：将沟通策略作为可优化的维度集成到 RL 目标中
  - 学术价值：解决了"Agent 应该何时沟通、何时行动"这一核心问题

#### 实际应用价值
- **应用场景**：智能客服、预订系统、技术支持等需要多轮交互的 Agent 系统
- **优势**：显著提升 Agent 在真实多轮交互场景中的表现
- **潜在影响**：32B 模型即可匹敌更大模型，降低了部署成本

### 方法优势详解

1. **动态训练 vs 静态训练**：通过动态用户模拟器，Agent 学会了在不确定性中主动沟通和调整策略
2. **参数量效率**：32B 模型超越更大模型，证明了训练方法的有效性而非模型规模的堆砌
3. **通用性**：方法不局限于特定领域，可应用于各类工具使用场景

### 局限性分析

1. **用户模拟器的真实性**：LLM 模拟的用户行为可能与真实用户存在偏差
2. **训练成本**：动态模拟用户在 RL 训练循环中增加了计算开销
3. **评估局限**：缺乏与真实用户交互的大规模评估

### 适用性分析

**适用场景**：需要多轮交互的 Agent 系统（客服、预订、咨询等）
**不适用场景**：简单的单轮工具调用场景（额外开销不必要）

## 技术路线定位

### 所属技术路线
本文属于 **Agent RL Training with Dynamic Interaction** 技术路线，核心特点是：
- 将用户交互纳入 RL 训练循环
- 使用 LLM 模拟动态环境
- 多维度奖励优化

### 技术路线发展历程
```
静态 SFT → 静态 RL (GRPO/PPO) → 动态用户 RL (MUA-RL) → 真实用户 RL
   ↑              ↑                      ↑                    ↑
 ToolLLM      ToolRL/GRPO              MUA-RL              未来方向
```

## 未来工作建议

1. **真实用户评估**：在真实用户场景中进行大规模 A/B 测试
2. **用户模拟器改进**：使用更复杂的用户行为建模，缩小模拟与真实的差距
3. **跨领域迁移**：验证方法在不同领域（医疗、法律、金融）的适用性

## 我的综合评价

### 价值评分

#### 总体评分
**7.5/10** - 首次将动态用户交互引入 Agent RL 训练，方法创新且实用价值高，但用户模拟器的真实性仍是开放问题

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次将 LLM 模拟用户集成到 Agent RL 训练循环，范式创新显著 |
| 技术质量 | 7/10 | 方法设计合理，但用户模拟器的实现细节不够深入 |
| 实验充分性 | 7/10 | 覆盖多个基准，但缺乏真实用户评估和更多消融实验 |
| 写作质量 | 7/10 | 结构清晰，问题定义明确 |
| 实用性 | 8/10 | 32B 模型即可实用部署，应用前景广阔 |

### 重点关注

- MUA-RL 的**动态用户模拟**训练范式是核心亮点
- 多轮沟通策略的 RL 优化值得深入理解
- 32B 超越更大模型的结果验证了训练方法的有效性

## 我的笔记

%% 用户可以在这里添加个人阅读笔记 %%

## 相关论文

### 直接相关
- [GRPO (DeepSeek)] - MUA-RL 使用的核心策略优化算法
- [ToolRL] - 工具使用的 RL 训练相关工作
- [TAU-Bench] - 多轮工具使用基准

### 后续工作
- 真实用户交互的 Agent RL 训练
- 跨领域多轮交互 Agent

## 外部资源
- 📄 [arXiv](https://arxiv.org/abs/2508.18669)
- 💻 [GitHub](https://github.com/zzwkk/MUA-RL)
- 📊 [HuggingFace Dataset](https://huggingface.co/datasets/zzwkk/MUA-RL-Dataset)

> [!tip] 关键启示
> 将环境（用户）本身纳入 RL 训练循环是提升 Agent 交互能力的关键 —— 动态训练优于静态训练

> [!warning] 注意事项
> - 用户模拟器的质量直接影响训练效果
> - 动态训练的计算开销显著高于静态训练
> - 评估结果基于模拟用户，真实场景表现可能有所不同

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读！这是 Agent RL 训练从"静态"走向"动态"的代表性工作，对从事 Agent 工具使用和对话系统研究的同学有重要参考价值。
