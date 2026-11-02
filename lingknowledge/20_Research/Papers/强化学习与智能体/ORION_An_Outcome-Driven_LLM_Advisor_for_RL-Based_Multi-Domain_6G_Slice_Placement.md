---
date: "2026-11-02"
paper_id: "arXiv:2610.41563"
title: "ORION: An Outcome-Driven LLM Advisor for RL-Based Multi-Domain 6G Slice Placement"
authors: "Mahmoud S. Mayaleh, Fetia Bannour, Nour-El-Houda Yellas"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - 多智能体
  - LLM-Agent
  - 网络切片
  - 6G
quality_score: "8.0/10"
created: "2026-11-02"
updated: "2026-11-02"
status: analyzed
---

# ORION: An Outcome-Driven LLM Advisor for RL-Based Multi-Domain 6G Slice Placement

## 核心信息
- **论文ID**：arXiv:2610.41563
- **作者**：Mahmoud S. Mayaleh, Fetia Bannour, Nour-El-Houda Yellas
- **机构**：--
- **发布时间**：2026-10-30
- **类别**：cs.LG, cs.AI, cs.MA, cs.NI
- **链接**：[arXiv](https://arxiv.org/abs/2610.41563) | [PDF](https://arxiv.org/pdf/2610.41563)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Deploying 6G network slices across administrative domains requires an online decision for each arriving request: which domain hosts each virtual network function, how traffic crosses domain boundaries, and whether the slice is admitted. This joint decision problem is NP-hard under continuous arrivals. Experiments on a parametrized family of multi-domain topologies show that no fixed planning strategy dominates across all regimes: the performance ranking reverses between topology classes, and feasible placements exist for most rejected requests but remain undiscovered. These findings identify the plan layer as the decisive performance lever. We introduce ORION, a hierarchical orchestrator in which two LLM agents translate service intents into structured requests and generate abstract placement plans. A cooperative multi-agent RL coordinator selects the cross-domain partition, treating the plan as a persistent soft prior, while per-domain agents place the functions. ORION main contribution lies at the plan layer: an outcome-driven episodic memory that records, for each completed attempt, the network-condition profile, the proposed plan, and its verified feasibility outcome. The memory feeds verified successes and labelled failures back to the planner, thereby closing the adaptation loop using deployment feasibility rather than a scalar reward assigned to the planner's own proposals.

### 中文翻译
跨行政域部署 6G 网络切片，需要对每个到达请求做在线决策：哪个域承载每个虚拟网络功能、流量如何跨越域边界、以及切片是否被接纳。这个联合决策问题在连续到达下是 NP-hard 的。在一族参数化的多域拓扑上的实验表明，不存在一种在所有场景下都占优的固定规划策略：性能排名在不同拓扑类之间会反转，而且大多数被拒绝的请求其实存在可行放置，只是未被发现。这些发现指出"规划层"才是决定性的性能杠杆。作者提出 ORION，一个分层编排器，其中两个 LLM 智能体把服务意图转化为结构化请求并生成抽象的放置规划；一个协作式多智能体 RL 协调器选择跨域划分，把该规划当作持久软先验，而各域内智能体负责放置功能。ORION 的核心贡献在规划层：一个结果驱动的情景记忆，为每次完成的尝试记录"网络状态画像、所提规划、以及经过验证的可行性结果"。该记忆把验证过的成功与标注过的失败反馈给规划器，从而用部署可行性（而非分配给规划器自身提议的标量奖励）闭合适应回路。

### 核心要点提炼
1. **问题**：跨域 6G 切片放置是连续到达下的 NP-hard 联合决策，固定规划策略无法在所有拓扑场景下占优。
2. **洞察**：规划层是决定性性能杠杆——多数被拒请求其实存在可行放置，只是未被发现。
3. **方法**：ORION 用两个 LLM 智能体生成抽象放置规划，协作式多智能体 RL 协调器做跨域划分，域内智能体执行放置。
4. **核心创新**：结果驱动的情景记忆，用"部署可行性"而非标量奖励来闭环规划器的自适应。

## 方法概述

### 方法架构
![[2610.41563_fig1.png|700]]

分层编排的核心逻辑：
- **意图→规划层**：两个 LLM 智能体把服务意图转化为结构化请求，并生成抽象放置规划（plan layer）。
- **跨域协调层**：协作式多智能体 RL 协调器选择跨域划分，把规划作为持久软先验（soft prior）。
- **域内执行层**：各域智能体负责实际放置虚拟网络功能。
- **结果驱动情景记忆**：为每次尝试记录「网络状态画像 + 所提规划 + 已验证可行性」，把成功/失败标注反馈给规划器，用部署可行性闭合适应回路。

### 关键创新
1. **结果驱动记忆（outcome-driven episodic memory）**：以"部署可行性"作为反馈信号，而非规划器自身提议得到的标量奖励，避免了奖励自评的偏差。
2. **规划层解耦评估**：在固定下游选择/执行机制的前提下，只在 held-out 拓扑族上评估规划器，干净地隔离出"规划质量"的贡献。
3. **重要性保留策略**：相比 FIFO 记忆，importance-based retention 贡献了大部分改进。

## 实验结果

- **设置**：参数化的多域拓扑族，在 held-out 拓扑族上评估规划器（保持下游选择与执行机制不变）。
- **关键结果**：所提记忆使接纳率（admission）较"无记忆的同一规划器"提升 4.6–11.5 个百分点；其中重要性保留策略贡献了相较 FIFO 存储的大部分增益。
- **结论**：规划层是决定性杠杆，结果驱动记忆能稳定提升跨域切片接纳率。

## 深度分析

### 优势
- 把"规划层"识别为决定性杠杆，并针对性地在规划层引入结果驱动记忆，思路清晰。
- 用"部署可行性"这一真实信号替代标量奖励，缓解了 LLM 规划器自评奖励的偏差问题。
- 通过 held-out 拓扑族的受控评估，贡献归因干净。

### 局限与开放问题
- 面向 6G 网络切片这一较垂直领域，通用性有待在更一般的 agent 规划任务上验证。
- 仅 1 张提取图片，方法图较为简略；多智能体 RL 协调器的具体训练细节在摘要中未充分展开。
- 记忆的规模与检索效率、长期在线部署下的遗忘/漂移问题未讨论。

### 与相关工作的关系
- 相比纯 RL 切片放置：引入 LLM 规划 + 结果驱动记忆，把抽象规划与具体放置解耦。
- 相比"标量奖励自评"的 LLM planner：用部署可行性闭环，属于"结果监督/过程监督"思路在网络编排领域的落地。
