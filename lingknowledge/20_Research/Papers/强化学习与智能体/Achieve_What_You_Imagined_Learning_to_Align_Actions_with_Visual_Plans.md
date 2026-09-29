---
date: "2026-09-29"
paper_id: "arXiv:2609.33832"
title: "Achieve What You Imagined: Learning to Align Actions with Visual Plans"
authors: "Yuheng Qiao, Ziran Wei, Xiaohan Wang, Daqiang Guo, Yichen Luo, Zhibo Pang, Peng Zhou, Sichao Liu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 世界模型
  - 机器人操作
  - 视觉规划
  - Flow-Policy-Optimization
quality_score: "8.5/10"
created: "2026-09-29"
updated: "2026-09-29"
status: analyzed
---

# Achieve What You Imagined: Learning to Align Actions with Visual Plans

## 核心信息
- **论文ID**：arXiv:2609.33832
- **作者**：Yuheng Qiao, Ziran Wei, Xiaohan Wang, Daqiang Guo, Yichen Luo, Zhibo Pang, Peng Zhou, Sichao Liu
- **机构**：KTH、北京航空航天大学、香港科技大学（广州）、北京大学、大湾区大学
- **发布时间**：2026-09-27
- **链接**：[arXiv](https://arxiv.org/abs/2609.33832) | [PDF](https://arxiv.org/pdf/2609.33832) | [项目页](https://imagine-to-achieve.github.io)
- **分类**：cs.RO, cs.AI, cs.LG

## 摘要翻译

### 英文摘要
World-action models can jointly predict future visual observations and robot actions. However, discrepancies may exist between their visual predictions and the consequences implied by generated actions. We observe that WAMs can often generate visually plausible task-completion outcomes before producing action sequences that reliably achieve them. Consequently, we treat the WAM-generated visual prediction as a goal-conditioned visual proposal rather than a directly executable plan. We use a frozen action-conditioned world model to predict action-conditioned consequences and construct feedback based on consistency between the two future predictions and alignment with the terminal goal. Leveraging this feedback, we employ Flow Policy Optimization (FPO) to optimize the action head of the WAM. This framework avoids online robot interaction and additional training of task-specific reward models. Across four real-world UR5 manipulation tasks, our method increases the mean success rate from 43.4% to 75.1%, compared with 61.4% for π0.5. These results show that cross-model prediction discrepancy can provide useful feedback for improving robot policies under the evaluated manipulation tasks.

### 中文翻译
世界-动作模型（WAM）能联合预测未来视觉观测与机器人动作。然而，其视觉预测与"生成动作所隐含的后果"之间可能存在偏差。作者观察到：WAM 常常**先**生成视觉上合理的任务完成结果，**之后**才产生能可靠达成该结果的动作序列。因此，作者把 WAM 生成的视觉预测当作"以目标为条件的视觉提案"，而非可直接执行的计划。他们使用一个冻结的、以动作为条件的世界模型来预测"动作条件后果"，并基于"两个未来预测之间的一致性"以及"与最终目标的对齐程度"来构造反馈。利用该反馈，作者采用 **Flow Policy Optimization（FPO）** 优化 WAM 的动作头。该框架避免了在线机器人交互，也无需额外训练任务特定的奖励模型。在四个真实世界 UR5 操作任务上，该方法把平均成功率从 43.4% 提升到 75.1%，相比之下 π0.5 为 61.4%。这些结果表明，跨模型预测差异可以为改善机器人策略提供有效反馈。

### 核心要点提炼
- **研究背景**：世界-动作模型（WAM）联合预测未来视觉与动作，但其"视觉预测"与"动作后果"常不一致。
- **研究动机**：WAM 的视觉预测"看起来能完成"，但动作序列未必可靠，存在"想象"与"执行"的错位。
- **核心方法**：把视觉预测当"目标条件视觉提案"，用冻结动作条件世界模型做一致性反馈，FPO 优化动作头。
- **主要结果**：4 个真实 UR5 任务平均成功率 43.4%→75.1%，超过 π0.5（61.4%）。
- **研究意义**：证明"跨模型预测差异"可作为无需奖励模型的策略改进信号。

## 研究背景与动机

### 领域现状
世界模型与视频生成模型的兴起，催生了"世界-动作模型"（WAM）——同时预测未来帧与动作的架构，被用于机器人操作规划。但这类模型存在一个根本问题：**预测的视觉结果与动作的真实后果脱节**。

### 现有方法的局限性
现有基于 WAM 的方法常把视觉预测当作可直接执行的计划，但作者观察到：模型"看得见的成功"（视觉上完成了任务）往往先于"动作上可靠的完成"出现。换言之，模型会"画饼"——生成好看的完成画面，却给不出能真正达成它的动作。

### 研究动机
如果视觉预测不可靠，不如把它**降级为"目标提案"**，再单独用动作条件世界模型去核实"这个动作序列到底会导致什么"，用两者的一致性来提供训练反馈。

## 研究问题

如何在**不进行在线机器人交互、不训练任务特定奖励模型**的前提下，利用 WAM 的视觉预测与动作条件世界模型之间的差异，为机器人策略提供有效反馈？

## 方法概述

### 核心思想
"视觉提案 + 动作条件核实"：把 WAM 的视觉预测当作目标条件提案，用冻结的动作条件世界模型核实动作后果，用"一致性 + 目标对齐"构造反馈，FPO 优化动作头。

### 方法框架

#### 整体架构

![[framework_page1.png|600]]

> 图1：方法整体框架，展示"视觉提案 → 动作条件核实 → 一致性反馈 → FPO 优化动作头"的闭环。

**流程**：
1. WAM 生成目标条件视觉提案（未来画面）。
2. 冻结的动作条件世界模型，给定动作序列，预测"动作条件后果"（另一组未来画面）。
3. 构造反馈信号：
   - **一致性**：两个未来预测（视觉提案 vs 动作后果）是否一致；
   - **目标对齐**：动作后果与最终目标的对齐程度。
4. 用该反馈驱动 Flow Policy Optimization（FPO），优化 WAM 的动作头。

#### 关键机制说明

**机制1：视觉预测降级为提案**
- 不再假设视觉预测可直接执行，避免"画饼"带来的错误监督。

**机制2：冻结动作条件世界模型**
- 使用冻结的、以动作为条件的世界模型，独立预测动作导致的未来状态，作为"核实器"。

**机制3：跨模型一致性反馈**
- 视觉提案与动作后果之间的差异，本身构成可用的奖励信号——无需额外训练奖励模型。

### 方法架构图

![[intro_page1.png|600]]

> 图2：引言配图，直观展示"想象（视觉提案）"与"执行（动作后果）"之间的一致性如何被用作反馈。

## 实验结果

### 实验目标
在真实机器人操作任务上，验证"跨模型预测差异"反馈能否提升策略成功率。

### 实验设置
- **任务**：4 个真实世界 UR5 机械臂操作任务。
- **基线**：π0.5（现有 VLA/WAM 基线）。

### 主要结果
| 方法 | 平均成功率 |
|------|-----------|
| 初始 WAM | 43.4% |
| π0.5 | 61.4% |
| **本文方法（FPO 优化后）** | **75.1%** |

**关键结论**：跨模型预测差异为改进机器人策略提供了有效反馈，在无在线交互、无奖励模型的前提下显著超越基线。

### 实验结果图

![[exp_page1.png|600]]

> 图3：四个真实任务的实验表现对比。

![[reward_page1.png|600]]

> 图4：反馈/奖励信号的可视化，展示一致性反馈如何区分好/坏动作。

![[ablation_page1.png|600]]

> 图5：消融实验，验证一致性反馈与目标对齐两项设计的贡献。

## 深度分析

### 研究价值评估

#### 理论贡献
- **"视觉预测即提案"的重定位**：明确指出 WAM 视觉预测与动作后果的错位，并给出务实的降级处理。
- **跨模型差异作为奖励**：为"无奖励模型的机器人策略学习"提供了一种可解释、可复用的反馈来源。
- **避免在线交互**：全程离线，降低真实机器人实验成本。

#### 实际应用价值
- **真实机器人操作**：在 UR5 上直接验证，具备落地潜力。
- **数据/交互成本低**：无需在线收集、无需奖励模型，适合资源受限的机器人研究。

### 方法优势详解
- **零在线交互**：真实机器人实验成本高，本方法全程离线。
- **无奖励模型**：用现成的动作条件世界模型做核实，避免额外训练。
- **显著增益**：相对初始 WAM 提升 31.7 个百分点，超过 π0.5。

### 局限性分析
- **依赖动作条件世界模型质量**：核实器若不准确，一致性反馈也会失真。
- **任务类型有限**：验证于 4 个 UR5 操作任务，更复杂/长程任务的泛化待检验。
- **π0.5 之外的基线**：未与更多 VLA 基线（如 π0、RT-2 等）全面对比。

## 技术路线定位

### 所属技术路线
本文属于 **世界模型 / 世界-动作模型（WAM）** 与 **机器人操作策略学习** 交叉方向，核心特点是"视觉提案 + 动作条件核实"的闭环反馈。

### 本文在技术路线中的位置
- **承上**：继承世界-动作模型联合预测未来视觉与动作的思路。
- **启下**：为"用跨模型预测差异做无奖励反馈"提供了可扩展范式，可与更强的动作条件世界模型结合。

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** — 问题洞察清晰（"画饼"现象），方法务实且真实机器人验证充分，但任务规模有限。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "视觉预测=提案"的重定位新颖 |
| 技术质量 | 8/10 | 冻结核实器 + FPO 设计合理 |
| 实验充分性 | 8/10 | 真实 UR5 四任务，含消融 |
| 写作质量 | 8/10 | 动机清晰，配图直观 |
| 实用性 | 8/10 | 零在线交互、零奖励模型，落地友好 |

### 重点关注
- **一致性反馈的构造细节**：如何度量"两个未来预测"之间的一致性。
- **动作条件世界模型的冻结方式**：为何冻结、如何保证核实器的独立性。

## 相关论文

### 直接相关
- [[20_Research/Papers/强化学习与智能体/Robot-GST_geometry-aware_spatial-temporal_robot_policy_representation_and_evaluation|Robot-GST]] - 同一团队（Sichao Liu）的机器人操作可靠性工作

### 背景相关
- [[20_Research/Papers/大语言模型/Diffusion_Reward_Models|Diffusion Reward Models]] - 奖励建模的替代范式

## 外部资源
- 项目页：https://imagine-to-achieve.github.io

> [!tip] 关键启示
> 视觉生成模型"看起来能完成"≠"动作能达成"；把预测结果降级为提案、再用独立世界模型核实，就能把这种错位变成免费的训练信号。

> [!success] 推荐指数
> ⭐⭐⭐⭐☆ 推荐：世界模型/机器人操作方向读者值得一读，"跨模型一致性反馈"的范式可迁移。
