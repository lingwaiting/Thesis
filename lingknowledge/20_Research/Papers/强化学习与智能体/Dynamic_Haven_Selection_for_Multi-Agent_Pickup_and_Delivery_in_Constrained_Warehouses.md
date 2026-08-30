---
date: "2026-08-30"
paper_id: "arXiv:2608.26939"
title: "Dynamic Haven Selection for Multi-Agent Pickup and Delivery in Constrained Warehouses"
authors: "Taisei Hirayama, Kohei Yoshida, Hiroki Sakaji, Itsuki Noda"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 多智能体
  - 路径规划
  - MAPD
  - 仓储机器人
  - 避让规划
quality_score: "8.0/10"
related_papers: []
created: "2026-08-30"
updated: "2026-08-30"
status: analyzed
---

# Dynamic Haven Selection for Multi-Agent Pickup and Delivery in Constrained Warehouses

## 核心信息
- **论文ID**：arXiv:2608.26939
- **作者**：Taisei Hirayama, Kohei Yoshida, Hiroki Sakaji, Itsuki Noda
- **机构**：北海道大学（Hokkaido University）、丰田自动织机株式会社（Toyota Industries Corporation）
- **发布时间**：2026-08-27
- **会议/期刊**：cs.MA、cs.RO（arXiv 预印本）
- **链接**：[arXiv](https://arxiv.org/abs/2608.26939) | [PDF](https://arxiv.org/pdf/2608.26939)
- **引用**：--

## 摘要翻译

### 英文摘要
Space-efficient warehouse layouts often contain single-agent-width aisles and dead-end workstations where robots have few places to wait without blocking others. In Multi-Agent Pickup and Delivery (MAPD) on such constrained layouts, robots must accept online pickup-delivery tasks while preserving protected waiting locations called Havens. The Safe HAven Retreat Planner (SHARP) introduced a mechanism that extends each committed task path with a validated retreat to the agent's dedicated initial Haven, but fixed-Haven commitments can send agents toward distant Havens after deliveries. We present A-sharp (Adaptive SHARP), which changes an agent's retreat target at task assignment time. A naive switch can cause two agents to rely on the same waiting location or let another committed path pass through a location that is still occupied or reserved. A-sharp prevents these failures with an availability test for candidate Havens and a pending-release rule that keeps the previous Haven protected until the agent departs. Under explicit Haven-structure and Safe Interval Path Planning (SIPP) assumptions, we prove invariant preservation and finite-release completeness: every task in any finite release sequence is delivered in finite time. Across 72,000 runs on 14,400 paired map-agent-count-rate-seed cases over four maps, both SHARP and A-sharp complete their respective 14,400 runs. For makespan (final delivery time), a prespecified paired comparison with Holm correction over all 138 configurations with more Havens than agents finds A-sharp significantly better in 107 configurations and never significantly worse than SHARP; on the tested tree map, the median reduction is 16.7%.

### 中文翻译
空间高效的仓储布局常包含单智能体宽度的过道与死胡同工作站，机器人几乎没有不阻塞他人的等待空间。在受约束布局上的多智能体取送货（MAPD）任务中，机器人必须在线接单，同时保留被称为 **Haven（避风港）** 的受保护等待位置。Safe HAven Retreat Planner（SHARP）提出了一种机制：把每个已承诺任务路径扩展为"退避到该智能体专属初始 Haven 的、经验证的后撤路径"，但**固定 Haven 承诺**会在送货后让智能体奔向远处的 Haven。我们提出 **A-sharp（Adaptive SHARP）**，在任务分配时即改变智能体的后撤目标。朴素的切换可能导致两个智能体依赖同一等待位置，或让另一条已承诺路径穿过仍被占用/预留的位置。A-sharp 通过**候选 Haven 可用性测试** + **pending-release（待释放）规则**（在智能体离开前保持前一 Haven 受保护）来避免这些失败。在明确的 Haven 结构与安全区间路径规划（SIPP）假设下，我们证明了**不变性保持**与**有限释放完备性**：任意有限释放序列中的每个任务都能在有限时间内送达。在 4 张地图、14,400 组配对（地图-智能体数-速率-种子）上的 72,000 次运行中，SHARP 与 A-sharp 都完成了各自的 14,400 次运行。对于 makespan（最终送达时间），在" Haven 数多于智能体数"的全部 138 个配置上做预先指定的配对比较（Holm 校正），A-sharp 在 107 个配置上显著更优、且从不显著更差；在被测树状地图上，中位 makespan 降低 16.7%。

### 核心要点提炼
- **研究背景**：空间高效的仓储布局（单宽过道、死胡同）下，多机器人取送货需在避让与等待位置之间权衡。
- **研究动机**：SHARP 的固定 Haven 承诺会在送货后让智能体奔向远处 Haven，浪费 makespan。
- **核心方法**：A-sharp 在任务分配时动态切换后撤目标，并用"可用性测试 + pending-release 规则"保证正确性。
- **主要结果**：在 107/138 配置上显著优于 SHARP、从不更差；树状地图中位 makespan 降 16.7%。
- **研究意义**：在保持完备性与安全性不变的前提下，用"动态 Haven 选择"显著降低多机器人任务完成时间。

## 研究背景与动机

### 领域现状
- 多智能体取送货（MAPD）是仓储自动化的核心问题。
- 空间高效布局（单宽过道、死胡同工作站）使机器人几乎没有安全等待空间，容易互相阻塞。

### 现有方法的局限性
- **SHARP**：为每个任务路径附加"退避到专属初始 Haven"的后撤段，保证安全与完备性，但**固定 Haven** 导致送货后智能体可能退避到远处 Haven，浪费 makespan。

### 研究动机
若能在任务分配时**动态选择更近的 Haven** 作为后撤目标，就能在不牺牲安全性/完备性的前提下降低 makespan——但朴素切换会引入新的死锁/冲突。

## 研究问题

### 核心研究问题
如何在保证安全性与完备性的前提下，动态选择 Haven 后撤目标，从而降低 MAPD 的 makespan？

## 方法概述

### 核心思想
在 SHARP 的"退避到 Haven"机制之上，允许智能体在任务分配时切换到更优（更近/更空闲）的 Haven，同时用两条规则保证不引入冲突：
1. **候选 Haven 可用性测试**：确保新 Haven 不被其他智能体同时依赖。
2. **pending-release 规则**：智能体离开前，保持前一 Haven 受保护。

### 方法框架

#### 整体架构

![[well-formed.png|800]]

> 图1：良构（well-formed）Haven 结构示意——智能体在受约束布局中的受保护等待位置与退避路径。

![[narrow-bicon.png|800]]

> 图2：窄双连通（narrow-biconnected）地图示意——单宽过道与死胡同工作站构成的受约束布局。

![[narrow-bicon-de.png|800]]

> 图3：含死胡同（dead-end）的窄双连通布局——凸显固定 Haven 的低效与动态选择的价值。

#### 各模块详细说明

**模块1：任务分配 + Haven 切换（Assignment + Adaptive Retreat）**
- **功能**：在任务分配时，为智能体选择（可能更新的）后撤目标 Haven。
- **关键技术**：候选 Haven 可用性测试。

**模块2：pending-release 保护（Pending-Release Rule）**
- **功能**：智能体离开前保持前一 Haven 受保护，防止他人占用。
- **关键点**：保证不变性不因切换而破坏。

**模块3：SIPP 安全规划（Safe Interval Path Planning）**
- **功能**：在安全区间内规划路径，保证无冲突。

### 关键创新

1. **动态 Haven 选择** - 把固定 Haven 承诺改为任务分配时的自适应切换，降低 makespan。
2. **可用性测试 + pending-release** - 两条规则保证动态切换不引入死锁/冲突。
3. **理论保证** - 证明不变性保持与有限释放完备性。

## 实验结果

### 数据集
- 4 张地图（含树状图 tree、窄双连通图等），14,400 组配对（地图-智能体数-速率-种子）。

### 实验设置
- **基线方法**：SHARP。
- **评估指标**：makespan（最终送达时间）、成功率等。
- **规模**：72,000 次运行。

### 主要结果
- 两方法都完成全部 14,400 次运行（100% 完备）。
- makespan：在 138 个"Haven 数 > 智能体数"配置上，A-sharp 在 107 个显著更优、从不显著更差。
- 树状地图中位 makespan 降低 16.7%。

![[success_rate_heatmap.png|800]]

> 图4：成功率热力图——不同配置下 A-sharp 与 SHARP 的成功率对比。

![[tree.png|800]]

> 图5：树状地图示意——A-sharp 在该地图上中位 makespan 降低 16.7%。

![[trend_makespan_grid.png|800]]

> 图6：makespan 趋势网格图——不同地图/智能体数下 makespan 的变化趋势。

![[trend_service_time_grid.png|800]]

> 图7：服务时间趋势网格图——A-sharp 对服务时间的改善。

## 深度分析

### 研究价值
- **理论贡献**：证明动态 Haven 切换在 SIPP 假设下保持完备性与不变性，理论严谨。
- **实际应用**：可直接用于仓储 AGV/AMR 调度，降低任务完成时间。
- **领域影响**：为 MAPD 的"避让规划 + 动态资源分配"提供了工程可用、有理论保证的方案。

### 优势
- 完备性与安全性有严格证明。
- 大规模实验（72,000 次运行）支撑结论。
- 工程实用性强（仓储场景直接相关）。

### 局限性
- 依赖显式 Haven 结构与 SIPP 假设，通用性受限。
- 评测以 makespan/服务时间为主，未覆盖能耗、鲁棒性等维度。
- 动态切换收益在"Haven 数 > 智能体数"时才显著。

### 适用场景
- 仓储 AGV/AMR 多机器人取送货调度。
- 空间高效、单宽过道的受限布局。

## 与相关论文对比
（该工作是对 SHARP 的直接改进；暂无直接历史笔记对比。）

## 技术路线定位
本文属于 **多智能体路径规划（MAPF/MAPD）** 路线，主要关注"受约束仓储中的避让规划与等待位置分配"。承上是 SHARP 的退避规划，启下是动态资源分配与理论保证的结合。

## 未来工作建议
1. 放宽对显式 Haven 结构与 SIPP 假设的依赖，提升通用性。
2. 结合能耗、故障恢复等更多优化目标。
3. 探索学习式/数据驱动的 Haven 选择策略。

## 我的综合评价

### 价值评分
- **总体评分**：8.0/10
- **分项评分**：
  - 创新性：7/10（对 SHARP 的增量但关键的改进）
  - 技术质量：9/10（理论证明严谨）
  - 实验充分性：9/10（72,000 次运行、Holm 校正、统计严格）
  - 写作质量：8/10
  - 实用性：8/10（仓储场景直接价值）

### 突出亮点
- 完备性与不变性的严格证明
- 大规模统计严谨的实验
- 在保持安全前提下降低 makespan

### 重点关注
- 候选 Haven 可用性测试与 pending-release 的具体实现
- 动态切换的理论正确性证明

### 可借鉴点
- "动态分配 + 保护规则"保证正确性的工程范式
- 用 Holm 校正做配对比较的严谨实验设计

### 批判性思考
- 收益依赖"Haven 数 > 智能体数"，在资源紧张场景优势是否仍在
- 固定 Haven 假设在真实仓库中是否成立

## 我的笔记

%% 用户可在此补充阅读笔记 %%

## 相关论文
（待补充：SHARP 及其他 MAPD 相关工作）

## 外部资源
- [arXiv](https://arxiv.org/abs/2608.26939)
- [PDF](https://arxiv.org/pdf/2608.26939)

> [!tip] 关键启示
> 在保证安全与完备性的前提下，"动态选择等待位置"这种看似微小的改动，也能带来显著的 makespan 改善（16.7%）。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 对多智能体路径规划与仓储机器人感兴趣的读者值得精读。
