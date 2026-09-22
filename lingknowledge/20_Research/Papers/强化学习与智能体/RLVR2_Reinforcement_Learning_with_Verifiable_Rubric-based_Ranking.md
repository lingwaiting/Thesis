---
date: "2026-09-22"
paper_id: "arXiv:2609.23457"
title: "RLVR^2: Reinforcement Learning with Verifiable Rubric-based Ranking"
authors: "Hao Li, Zhengkun Zhang, Gangqiang Hu, Zhen Zhang, Yude Gao, Dai Dai, Jing Liu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - RLVR
  - Rubric
  - Ranking
quality_score: "9.07/10"
created: "2026-09-22"
updated: "2026-09-22"
status: analyzed
---

# RLVR^2: Reinforcement Learning with Verifiable Rubric-based Ranking

## 核心信息
- **论文ID**：arXiv:2609.23457
- **作者**：Hao Li, Zhengkun Zhang, Gangqiang Hu, Zhen Zhang, Yude Gao, Dai Dai, Jing Liu
- **机构**：--
- **发布时间**：2026-09-20
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI）
- **链接**：[arXiv](https://arxiv.org/abs/2609.23457) | [PDF](https://arxiv.org/pdf/2609.23457)
- **引用**：--

## 摘要翻译

### 英文摘要
RLVR is expanding from tasks with well-defined correctness signals (math, code) toward multifaceted quality requirements specified by multi-dimensional rubrics. Since policy optimization consumes one scalar per rollout, rubric-based pipelines must map multiple criterion scores into a scalar reward. The prevailing practice—normalizing each criterion and taking a linear combination—assumes cardinal score differences are comparable across criteria and that gains on one criterion compensate for failures on another; both assumptions are unreliable when criteria are semantically heterogeneous. RLVR² converts rubric scores into criterion-specific within-group ordinal outcomes, recovers a latent utility from the comparison matrix, and merges these utilities into one training signal.

### 中文翻译
带可验证奖励的强化学习（RLVR）正从数学、代码等具有明确定义正确性信号的任务，扩展到由多维评分标准（rubric）刻画的多元质量需求。由于策略优化每次 rollout 只消费一个标量，基于 rubric 的流程必须把多个标准的分数映射为单一标量奖励。主流做法——对每个标准归一化后取线性组合——假设各标准间的基数分差可比、且某一标准的增益能补偿另一标准的失败；当标准在语义上异构时，这两个假设都不可靠。RLVR² 将 rubric 分数转换为每个标准组内的序数结果，从比较矩阵中恢复隐效用，再把这些效用合并为单一训练信号。

### 核心要点提炼
- **研究背景**：RLVR 从可验证正确性（数学/代码）扩展到多维 rubric 质量
- **研究动机**：多维分数如何聚合为标量奖励被当成"缩放"，实则隐式决定了质量维度的权衡
- **核心方法**：组内序数化 → 隐效用恢复 → 多效用合并
- **主要结果**：3 个模型规模、16 个基准上一致超越代表性 rubric 基线
- **研究意义**：为 rubric-based RLVR 的奖励聚合提供原则性范式

## 研究问题

### 核心研究问题
当多个语义异构的评分标准都需要通过一个标量奖励来优化时，如何聚合这些标准分数，才能既**避免校准异构量表**、又**不丢失质量目标**？

## 方法概述

### 核心思想
RLVR² 的关键洞见是：**只保留组内排序信息，丢弃原始分数幅值**。线性组合之所以不可靠，是因为它把"可比基数的分数"和"可跨标准补偿"两个强假设强加于语义异构的标准之上。RLVR² 把每个标准的分数转换为"该标准组内的序数胜负关系"，从由此得到的比较矩阵中恢复隐效用，再做合并，从根本上回避了跨标准量表校准问题。

### 方法框架

![[2609.23457_fig1.png|800]]

> 图1：RLVR² 方法框架——(1) 每个标准内将 rubric 分数转为组内序数结果；(2) 从比较矩阵恢复隐效用；(3) 将多标准效用合并为单一训练信号。

#### 各模块详细说明

**模块1：组内序数化（Within-Group Ordinal Outcome）**
- **功能**：把每个标准的原始 rubric 分数转换为该标准组内的相对排序结果
- **输入**：各 rollout 在某标准上的分数
- **输出**：组内序数胜负关系（谁优于谁）
- **关键技术**：丢弃绝对分数幅值，只保留相对序

**模块2：隐效用恢复（Latent Utility Recovery）**
- **功能**：从序数比较矩阵中反推每个 rollout 在该标准下的隐效用
- **输入**：组内比较矩阵
- **输出**：标准专属的隐效用估计
- **关键技术**：基于排序的效用估计（如 Bradley-Terry 类模型）

**模块3：效用合并（Utility Merging）**
- **功能**：把各标准的隐效用合并为单一训练信号
- **输入**：多标准隐效用
- **输出**：标量奖励
- **关键技术**：客观保持的属性调整——与观测排序相关但非训练目标的辅助属性可在不扩充 rubric 的情况下进入估计

### 关键创新
1. **弃幅值、保序数**——回避异构 rubric 量表的校准难题
2. **客观保持的属性调整**——辅助属性可进入估计却不直接作为奖励
3. **原则性聚合范式**——把"缩放"提升为"排序 + 效用恢复"的正式框架

## 实验结果

### 数据集 / 规模
- 3 个模型规模 × 16 个基准

### 主要结果
- RLVR² 在**绝大多数**基准、**每个规模**上一致超越代表性 rubric-based 基线
- 分析显示其能控制与"推理效率""响应格式"相关的系统效应，同时**保留质量目标**

## 深度分析

### 研究价值
- **理论贡献**：揭示奖励聚合的序数本质，为 RLVR 的多维奖励设计提供理论依据
- **实际应用**：可用于所有需要多维质量评估（如长文生成、对话、Agent 任务）的 RL 后训练流程
- **领域影响**：推动 RLVR 从"单点正确性"向"多维质量"的规范化演进

### 优势
1. 从根因（量表异构）出发，而非经验调权
2. 客观保持的属性调整机制设计精巧
3. 跨规模、跨基准的一致性验证充分

### 局限性
1. 摘要未披露隐效用恢复的具体模型选择细节
2. 组内序数化可能损失跨标准的整体信息
3. 与超大规模模型的验证尚未在摘要中体现

## 我的综合评价

### 价值评分
- **总体评分**：**9.07/10** —— 问题定位精准、方法原则性强
- **分项评分**：
  - 创新性：8/10（排序视角重塑奖励聚合）
  - 技术质量：9/10（形式化清晰）
  - 实验充分性：8/10（3 规模 16 基准）
  - 写作质量：8/10
  - 实用性：8/10（可直接用于 RL 后训练）

### 突出亮点
- "保留序数、丢弃幅值"的核心思想
- 客观保持的属性调整机制

> [!success] 推荐指数
> ⭐⭐⭐⭐ 值得精读——RLVR 多维奖励聚合的原则性方案
