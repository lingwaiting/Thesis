---
date: "2025-09-25"
paper_id: "arXiv:2509.21240"
title: "Tree Search for LLM Agent Reinforcement Learning"
authors: "Yuxiang Ji, Ziyu Ma, Yong Wang, Guanhua Chen, Xiangxiang Chu, Liaoni Wu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Tree-Search
  - GRPO
  - Agent-RL
  - ICLR-2026
  - Process-Supervision
quality_score: "8.0/10"
created: "2026-07-11"
updated: "2026-07-11"
status: analyzed
---

# Tree-GRPO: Tree Search for LLM Agent Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2509.21240
- **作者**：Yuxiang Ji, Ziyu Ma, Yong Wang, Guanhua Chen, Xiangxiang Chu, Liaoni Wu
- **机构**：阿里高德 (AMAP, Alibaba Group), 厦门大学, 南方科技大学
- **发布时间**：2025-09-25（v3: 2026-03-18）
- **会议/期刊**：**ICLR 2026**
- **链接**：[arXiv](https://arxiv.org/abs/2509.21240) | [PDF](https://arxiv.org/pdf/2509.21240)
- **代码**：[GitHub](https://github.com/AMAP-ML/Tree-GRPO)

## 摘要翻译

### 英文摘要
Recent advances in reinforcement learning (RL) have significantly enhanced the agentic capabilities of large language models (LLMs). In long-term and multi-turn agent tasks, existing approaches driven solely by outcome rewards often suffer from the problem of sparse supervision. To address the challenge, we propose Tree-based Group Relative Policy Optimization (Tree-GRPO), a grouped agent RL method based on tree search, where each tree node represents the complete agent interaction step.

### 中文翻译
近期强化学习的进展显著增强了 LLM 的智能体能力。在长期和多轮智能体任务中，仅由最终奖励驱动的现有方法往往面临监督稀疏的问题。为了解决这一挑战，我们提出基于树的群组相对策略优化（Tree-GRPO），一种基于树搜索的分组智能体 RL 方法，其中每个树节点代表完整的智能体交互步骤。

### 核心要点提炼
- **研究背景**：基于 RL 的 LLM Agent 训练面临长期任务中奖励稀疏的挑战
- **研究动机**：链式 RL 方法需要大量 rollout 预算，且缺乏中间步骤的监督信号
- **核心方法**：Tree-GRPO —— 以树结构组织 Agent 交互步骤，共享前缀降低预算，自动提取过程监督
- **主要结果**：仅用链式 GRPO 的 1/4 预算即可超越其性能；理论证明树内 GRPO 等价于步骤级 DPO
- **研究意义**：为 LLM Agent 的高效 RL 训练提供了新范式，被 ICLR 2026 接收

## 研究背景与动机

### 领域现状
LLM Agent 的 RL 训练主流方法是 **链式（Chain-based）GRPO/PPO**：
- 每次 rollout 生成一条完整的交互链
- 基于最终结果给予奖励信号
- 不同的 rollout 之间完全独立

### 现有方法的局限性
1. **预算浪费**：不同 rollout 的前缀部分（如初始思考步骤）重复生成，浪费大量 token 和工具调用预算
2. **奖励稀疏**：长期任务中只有最终奖励，中间步骤缺乏监督信号
3. **探索效率低**：每条链独立生成，无法共享探索成果

### 研究动机
如果能将多个 rollout 组织为**树结构**，共享公共前缀，则可以在相同预算下探索更多路径，同时利用树结构天然的过程监督信号。

## 研究问题

### 核心研究问题
**如何利用树搜索结构提升 LLM Agent 的 RL 训练效率？如何在无需额外标注的情况下自动获得步骤级过程监督信号？**

## 方法概述

### 核心思想
Tree-GRPO 的核心思想是将 Agent 的**多轮交互步骤组织为搜索树**：
- 每个树节点 = 完整的 Agent 交互步骤（思考 → 行动 → 观察）
- 共享公共前缀节点，减少重复生成
- 利用树结构的分支关系，设计**树内（intra-tree）和树间（inter-tree）两级优势估计**

![[pipeline_page1.png|800]]

> 图1：Tree-GRPO 整体方法 pipeline —— 展示了从树搜索采样到两级优势估计的完整流程

### 方法框架

#### 整体架构

Tree-GRPO 包含以下关键组件：

**1. 树搜索采样（Tree Search Sampling）**
- 从根节点开始，每个节点代表一个 ReAct 步骤（Thought → Action → Observation）
- 从同一步骤节点展开多条分支（不同的 Thought/Action）
- 共享前缀节点，大幅降低 rollout 预算

**2. 两级群组相对优势估计**
- **树内级别（Intra-tree）**：同一树内共享前缀的不同路径之间的相对优势
- **树间级别（Inter-tree）**：不同树（不同初始状态）之间的相对优势
- 综合两级优势得出最终策略梯度

**3. 隐式过程监督**
- 树结构自然提供了过程监督信号
- 若某个节点子树中的所有完整路径都导致低奖励 → 该节点步骤质量差
- 若某个节点子树中存在高奖励路径 → 该节点步骤质量好
- **理论证明**：树内 GRPO 等价于步骤级 DPO（Direct Preference Optimization）

![[teaser_page1.png|800]]

> 图2：Tree-GRPO teaser —— 树搜索结构使得仅凭最终奖励即可回溯出步骤级过程监督信号

#### 关键数学公式

树搜索策略优化的核心公式：

设 $T$ 为树结构，$v$ 为节点，$children(v)$ 为 $v$ 的子节点：

**树内优势**：
$$A^{\text{intra}}(v) = \frac{R(v) - \mu_{\text{sibling}}}{\sigma_{\text{sibling}}}$$

其中 $R(v)$ 为节点 $v$ 所在路径的最终奖励，$\mu_{\text{sibling}}$ 和 $\sigma_{\text{sibling}}$ 为兄弟节点的奖励均值和标准差。

**树间优势**：
$$A^{\text{inter}}(T_i) = \frac{\bar{R}(T_i) - \mu_{\text{forest}}}{\sigma_{\text{forest}}}$$

综合优势：
$$A(v) = \alpha \cdot A^{\text{intra}}(v) + (1-\alpha) \cdot A^{\text{inter}}(T_i)$$

### 预算效率分析

假设链式 GRPO 需要 $N$ 条完整链，每条链 $L$ 步：
- **链式**：总步骤 = $N \times L$
- **Tree-GRPO**（分支因子 $b$）：总步骤 ≈ $\frac{N \times L}{b}$（共享前缀）

实验证明 $b \approx 4$ 时即可达到与链式相同甚至更好的性能。

![[tree_chain_comp_page1.png|800]]

> 图3：Tree-GRPO vs Chain-GRPO 的预算对比

## 实验结果

### 实验设置
- **基座模型**：Llama-3 和 Qwen 系列
- **数据集**：11 个数据集，涵盖 3 类 QA 任务（知识问答、推理、Agent 交互）
- **基线**：Chain-GRPO、Vanilla PPO、SFT

### 主要结果

![[llama_comp_page1.png|800]]

> 图4：基于 Llama 的主要实验结果对比

![[qwen_comp_page1.png|800]]

> 图5：基于 Qwen 的主要实验结果对比

关键发现：
- Tree-GRPO 在 **1/4 预算**条件下超越 Chain-GRPO
- 在 11 个数据集上全面优于链式方法
- 两种基座模型上均验证了一致性

### 消融实验

![[tree_search_comp_page1.png|800]]

> 图6：树搜索组件的消融实验

1. **树搜索 vs 链式搜索**：树搜索在所有预算水平上优于链式
2. **分支因子的影响**：$b=3-5$ 时效果最佳
3. **两级优势 vs 单级优势**：两级优势显著优于仅使用树内或树间优势

## 深度分析

### 研究价值评估

#### 理论贡献
1. **树搜索 RL 范式**：将 Agent RL 训练从链式扩展到树式，提供了更高效的探索框架
2. **隐式过程监督理论**：证明了树内 GRPO 与步骤级 DPO 的等价性，为无需人工标注的过程监督提供了理论支撑
3. **预算效率分析**：提供了相同预算下树搜索优于链式搜索的理论和实验依据

#### 实际应用价值
- **降低成本**：仅需 1/4 预算即可达到更好性能，大幅降低 Agent RL 训练成本
- **易于实现**：在现有 GRPO 框架基础上增加树搜索采样，改动量小
- **通用性**：方法不依赖特定模型或任务类型

### 方法优势详解

1. **前缀共享 → 预算效率**：树结构中共享公共步骤，避免重复生成相同的初始推理
2. **隐式过程监督 → 自动 Credit Assignment**：无需人工标注步骤质量，从树结构自动推导
3. **两级优势 → 降低方差**：树内和树间优势互补，提供更稳定的梯度估计

### 局限性分析

1. **树结构开销**：管理树结构的计算开销，特别是在大规模 rollout 场景
2. **分支因子敏感**：最优分支因子因任务而异，需要调参
3. **步骤粒度**：以 ReAct 步骤为节点，可能不适用于更细粒度或更粗粒度的任务

### 适用场景

- **适用**：长期多轮 Agent 任务、需要探索多样策略的场景
- **不适用**：单步决策任务（树结构优势有限）

## 技术路线定位

本工作是 **Efficient Agent RL Training** 方向的关键节点，连接了：
- 传统链式 GRPO → 树搜索增强 GRPO → 未来可能有图搜索或混合搜索方法

## 未来工作建议

1. **自适应分支因子**：根据任务难度动态调整分支策略
2. **结合蒙特卡洛树搜索 (MCTS)** ：引入更系统的探索-利用平衡
3. **更细粒度的步骤分解**：在子步骤级别应用树搜索

## 我的综合评价

### 总体评分
**8.0/10** - 方法简洁优雅，理论和实验俱佳，被 ICLR 2026 接收证明了其学术价值

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 树搜索 + Agent RL 的组合创新，理论证明树内GRPO等价于步骤级DPO |
| 技术质量 | 8/10 | 方法设计严谨，两级优势估计巧妙，理论分析充分 |
| 实验充分性 | 8/10 | 11数据集、3类任务、2种基座模型，消融实验全面 |
| 写作质量 | 8/10 | 结构清晰，理论与实验并重 |
| 实用性 | 8/10 | 仅需1/4预算即可超越基线，实用价值高 |

### 重点关注

- **树结构共享前缀**是预算效率的核心来源
- **隐式过程监督**是该方法最巧妙的理论贡献
- 被 ICLR 2026 接收，是 Agent RL 领域的重要进展

## 我的笔记

%% 用户可以在这里添加个人阅读笔记 %%

## 相关论文

### 直接相关
- [GRPO (DeepSeek)] - Tree-GRPO 的基础算法
- [MCTS-based LLM Reasoning] - 蒙特卡洛树搜索在 LLM 中的应用
- [Process Reward Model] - 过程奖励模型相关工作

## 外部资源
- 📄 [arXiv](https://arxiv.org/abs/2509.21240)
- 💻 [GitHub](https://github.com/AMAP-ML/Tree-GRPO)
- 📄 [OpenReview (ICLR 2026)](https://openreview.net/forum?id=BtU29vylui)

> [!tip] 关键启示
> 树结构是连接"探索效率"和"过程监督"的桥梁 —— 共享前缀降低预算，分支结构提供隐式监督

> [!warning] 注意事项
> - 树搜索的开销随分支因子线性增长
> - 步骤级别定义影响树结构的效果
> - 极长任务可能需要分层树结构

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！这是 ICLR 2026 接收的高质量工作，理论解释清晰、实验扎实。对从事 LLM Agent RL 训练的研究者和工程师都有重要参考价值。
