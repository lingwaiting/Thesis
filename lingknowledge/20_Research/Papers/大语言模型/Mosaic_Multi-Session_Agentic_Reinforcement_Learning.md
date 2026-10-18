---
date: "2026-10-15"
paper_id: "arXiv:2610.22174"
title: "Mosaic: Multi-Session Agentic Reinforcement Learning"
authors: "Yilong Xu, Minrui Xu, Xinyu Zhou, Xiang Long, Haochen Tan, Zhijiang Guo"
domain: "大语言模型"
tags:
  - 论文笔记
  - agentic-RL
  - multi-session
  - GRPO
  - reinforcement-learning
  - context-compaction
  - subagent
quality_score: "8.5/10"
created: "2026-10-18"
updated: "2026-10-18"
status: analyzed
---

# Mosaic: Multi-Session Agentic Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2610.22174
- **作者**：Yilong Xu, Minrui Xu, Xinyu Zhou, Xiang Long, Haochen Tan, Zhijiang Guo
- **机构**：LARK, HKUST(GZ)、华为 Foundation Model Lab、香港城市大学、HKUST
- **发布时间**：2026-10-15
- **会议/期刊**：Preprint
- **链接**：[arXiv](http://arxiv.org/abs/2610.22174v1) | [PDF](https://arxiv.org/pdf/2610.22174v1) | [项目页](https://lark-ai-lab.github.io/Mosaic/)

## 摘要翻译

### 中文翻译
现有智能体强化学习（agentic RL）方法大多假设单会话轨迹——交互在一个持续累积的上下文中进行。然而，上下文压缩（context compaction）和子智能体分叉（subagent forking）等技术会产生跨多个会话的轨迹，造成训练假设与推理执行之间的不匹配。本文提出 **Multi-Session Agentic RL** 这一更通用的设定，支持一个轨迹内任意数量的会话。在此设定下，广泛采用的"广播"（broadcasting）方法把轨迹级优势复制到所有会话，但无法区分各会话的贡献、也无法指导轨迹内的会话分配。为此，作者提出 **Mosaic**，一种无 critic 的分层优化多会话 agentic RL 方法：在轨迹层，将会话使用信号纳入优势估计；在会话层，按估计的边际贡献塑造每个会话的优势。在 BrowseComp（Qwen3.5-4B）上，Mosaic 在上下文压缩和子智能体分叉两种场景下均超越 broadcasting，且平均使用更少的会话；进一步分析表明其改进了会话效用与分配。

## 研究背景与动机

### 领域现状
Agentic RL 通过在线 rollout 和奖励反馈训练 LLM 智能体做多轮推理、工具使用与环境交互。近期智能体引入了上下文管理（context compaction）、harness 工程（subagent forking）等高级推理技术，使工作流能处理更长程、更复杂的任务。

### 现有方法的局限
- **单会话假设**：大多数 agentic RL 假设单会话轨迹，但 compaction 会压缩历史、重写前缀以释放上下文，从而开启新会话；subagent forking 会委托子任务产生分支会话。这些轨迹不再满足单会话假设，造成训练-推理不一致。
- **Broadcasting 的粗糙监督**：把轨迹级结果奖励/优势复制到所有会话的所有 token，只提供粗粒度监督，无法显式控制跨会话的优化，轨迹跨越多会话时学习信号定位差。

### 研究动机
如何为多会话轨迹设计更精细、分层的优化信号，既保留结果导向的全局信号，又区分每个会话的局部贡献？

## 方法概述

### 核心思想
Mosaic 采用**分层优化**：轨迹层用会话感知的优势估计提供长期、结果导向的全局信号；会话层用边际信息增益塑造优势，捕捉短期的局部边际效应。

### 方法框架

#### 组件1：Session-Aware Advantage Estimation（会话感知的轨迹级优势估计）
- 用会话数 $N_i$ 作为探索程度/结构复杂度的代理信号，但不能直接当奖励。
- 设计三条排序约束（C1-C3）：成功轨迹用更少会话 → 优势更高；失败轨迹用更多会话（探索更充分）→ 优势更高；结果奖励必须保持主导地位。
- 将会话数映射为组内**中心化排名** $u_i = 2f(N_i)-1$，用调制因子 $\alpha_i^{\mathrm{session}} = -\rho u_i$ 只调节优势的**幅度**而不改变符号：
  $$\hat{A}_i^{\mathrm{sat}} = (1 + \alpha_i^{\mathrm{session}})\,\hat{A}_i^{\mathrm{traj}}$$
- 对同质奖励组（全成功/全失败）引入 fallback 信号，提升样本效率。

#### 组件2：Session-Level Advantage Shaping（会话级优势塑造）
- 把每个会话视为状态转移 $s_{i,j} = (v_{i,j-1} \to v_{i,j})$，定义三类会话：normal、compaction、subagent。
- 用 rollout 策略对 ground-truth 答案的**边际信息增益**作为会话级即时奖励：
  $$r^{\Delta}_{i,j} = \mathrm{stopgrad}\big[\Phi_{\bar\theta}(y^*_i \mid x_i, v_{i,j}) - \Phi_{\bar\theta}(y^*_i \mid x_i, v_{i,j-1})\big]$$
- **按会话类型分别归一化**（type-conditioned normalization），避免不同类型增益分布差异导致的系统性偏差。
- 最终：$\hat{A}_{i,j}^{\mathrm{session}} = \hat{A}_i^{\mathrm{traj}} + \lambda_r \hat{r}_{i,j}$

![[figure1-single-session-vs-multi-session-3_page1.png|800]]

> 图1：单会话 vs 多会话轨迹的对比。compaction 和 subagent forking 会打破单会话假设。

## 实验结果

### 实验设置
- **基准**：BrowseComp（1266 个需持续网络导航的难题）
- **基模型**：Qwen3.5-4B，ReAct 风格 agent loop
- **工具**：search（Serper API）+ visit（Jina Reader）
- **训练**：REDSearcher_RL_1k，1 epoch，rollout batch 16，group size 8

### 主要结果（BrowseComp 准确率）

| 方法 | Single-Session | Compact 场景 | Subagent 场景 |
|------|----------------|--------------|---------------|
| Qwen3.5-4B（无 RL） | 23.22 | 24.80 (1.70) | 21.64 (2.98) |
| GRPO（单会话训练） | 31.99 | 34.68 (1.24) | 31.91 (2.09) |
| Broadcasting（多会话） | -- | 35.31 (3.17) | 31.44 (4.43) |
| **Mosaic** | -- | **38.86 (2.35)** | **32.46 (3.77)** |

> 括号内为平均会话数。Mosaic 相对 broadcasting 最高 +10.1% 相对提升，且会话数更少（3.17→2.35）。

### 关键发现
- **RQ2 消融**：轨迹级会话感知优势估计与会话级优势塑造各自都有贡献；compact（线性结构）场景两组件叠加增益明显，subagent（DAG 结构）场景增益较小。
- **RQ3 机制**：会话感知优势产生预期的排序；type-conditioned 归一化消除了对 compaction 会话的系统性偏见。
- **RQ4 洞察**：多会话优化的核心不是"用多少会话"，而是"从执行的会话中榨取多少有用进展"——Mosaic 在会话数量不增的前提下提升会话效用（Positive-IG 37.36%→59.92%）与分配。

![[result_and_ablation_page1.png|800]]

> 图2：主要结果与消融——多会话推理/训练/Mosaic 的增益，以及各组件贡献。

![[training_dynamic_page1.png|800]]

> 图3：训练动态——Mosaic 提升轨迹奖励更快，且会话数增长更早收敛。

![[ordering_and_ig_normalization_page1.png|800]]

> 图4：优势-会话数排序关系（左）与信息增益分布的类型归一化对比（右）。

## 深度分析

### 研究价值评估
- **理论贡献**：形式化"多会话 agentic RL"这一新设定，并给出无 critic 的分层优化方案，统一了 compaction/subagent 等异构推理机制的优化。
- **关键洞察**：会话级"边际信息增益"作为 process supervision 的代理信号，无需 critic 即可捕捉每个会话的局部贡献；按类型归一化是关键工程细节。
- **实际价值**：改进样本效率与会话分配，在更少会话下取得更高准确率，直接降低推理成本。

### 方法优势
- **无 critic 分层**：轨迹级全局信号 + 会话级局部信号，无需训练 critic 模型。
- **会话效用而非数量**：通过中心化排名和类型归一化，抑制无意义的会话堆叠，鼓励"有进展的继续、低效用的停止"。
- **可迁移到异构机制**：compaction 与 subagent 两种场景统一在同一算法框架下。

### 局限性
- **subagent 场景增益有限**：DAG 结构下，会话的即时边际收益与最终贡献可能不一致，shaping 信号有偏差。
- **会话数仅代理信号**：不直接等于探索强度或执行成本，语义上仍是间接的。
- **单一基准**：仅在 BrowseComp 上验证，未覆盖更广泛的 agentic 任务。

### 与相关论文对比
- **GRPO**：单会话假设的经典 agentic RL，本文扩展其优势估计以纳入会话信号。
- **Broadcasting（如 Dressage/Polar）**：把轨迹级优势广播到所有会话，是本文的直接对标基线。
- **IGPO**：在 turn 级应用信息增益，本文在 session 级应用，并证明 wall-time 计算成本更低。

## 我的综合评价

### 总体评分
**8.5/10** — 问题定义新颖、分层优化设计优雅、消融与机制分析扎实；局限在于 subagent 场景增益有限、基准单一。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 提出多会话设定与分层优化，迁移信息增益到会话级 |
| 技术质量 | 8/10 | 排序约束、中心化排名、类型归一化设计严谨 |
| 实验充分性 | 8/10 | RQ1-4 覆盖有效性、消融、机制、行为模式，但仅单基准 |
| 写作质量 | 9/10 | 结构清晰，RQ 驱动，讨论部分有洞见 |
| 实用性 | 8/10 | 更少会话更高准确率，直接降推理成本 |

> [!tip] 关键启示
> 多会话 agentic RL 的关键不是"会话数量"，而是"会话效用与分配"——用边际信息增益做会话级塑造信号，是无需 critic 的优雅方案。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读：对正在用 GRPO 训练长程 agent、且引入 compaction/subagent 的研究者很有参考价值。
