---
date: "2026-07-28"
paper_id: "2607.23602"
title: "Action from Adjacent Set in Physical Space Outperforms the Best Prediction in World Models"
authors: "Liangyu Li, Qingwen Liu, Mingqing Liu"
domain: "强化学习与智能体"
tags: [论文笔记, World-Models, Planning, Robotics, Action-Selection, Model-Based-RL]
quality_score: "9.7/10"
related_papers: []
created: "2026-07-28"
updated: "2026-07-28"
status: analyzed
---

# ASAR: Action from Adjacent Set Outperforms Best Prediction in World Models

## 核心信息
- **论文ID**：2607.23602
- **作者**：Liangyu Li, Qingwen Liu, Mingqing Liu
- **机构**：--
- **发布时间**：2026-07-26
- **链接**：[arXiv](https://arxiv.org/abs/2607.23602) | [PDF](https://arxiv.org/pdf/2607.23602)

## 摘要翻译

基于采样和潜在世界模型的控制器为每个候选动作序列分配预测终端成本，选择最小的并执行其第一个动作块，然后重规划。即使终端成本完美反映物理世界的真实任务目标，这一规则也可能失败——残差预测误差可能给不可行序列异常低的成本，而更大的候选池给这种错误更多机会超越可行方案。我们称之为"条件失败提案过生成"。在 Cube 候选执行审计中，将提案预算从 72 增至 288，各指标下最低潜在成本选择的可行性从 0.375 降至 0.062。我们提出相邻集动作重构（ASAR）：在低成本候选中测量标准化早期动作前缀的密度，并用最小成本序列的轻量锚从相邻集重构完整序列。在 Carry and Release 75 个查询上，Kernel ASAR 相比最优选择提升 18.7-28 个百分点成功率。

## 研究问题
当候选池越大越好是直觉时，为什么更多候选反而导致世界模型中更差的选择？如何修复？

![[esp_construction_overview_page1.png|800]]

> 图1：ASAR 方法概览，展示从候选池到相邻集重构的流程。

## 方法概述

### 核心发现：提案过生成悖论
- 更大的候选池包含更多可行方案，但残差预测误差产生的假低分不可行方案也随之增多
- 假低分方案"胜出"的概率随池大小增加——悖论性地降低选择质量

### ASAR 方法
1. 在低成本候选人中测量早期动作前缀密度（高密度区域=可行）
2. 使用最小成本序列作为轻量锚
3. 从相邻集中重构完整动作序列

![[trm_proposal_scaling_page1.png|800]]

> 图2：提案规模缩放实验，展示随候选池增大可行选择率反而下降。

### 关键创新
- 首次明确定义"提案过生成"悖论及其条件
- ASAR 通过密度估计绕过单点成本评估的噪声

## 实验结果
- **Cube 环境**：提案 72→288，最低成本选择可行性从 0.375→0.062
- **Carry and Release (75 查询)**：Kernel ASAR 提升 18.7-28 个百分点
- **多种成本函数**：潜在成本和轨迹可达性成本下均有效

> [!tip] 关键启示
> 在噪声评估下，更多选择可能导致更差决策——需要在密度空间而非单点成本空间中进行选择。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 揭示了基于模型规划中重要的反直觉现象，方法论严谨。
