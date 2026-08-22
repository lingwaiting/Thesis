---
date: "2026-08-22"
paper_id: "arXiv:2608.19803"
title: "MileGPO: Milestone Inference with Local Evidence for Graph-Based Policy Optimization of Long-Horizon LLM Agents"
authors: "Bo Qian, Yuting Wu, Shuang Zeng, Huaiyu Wan, Dalin Zhang, Jiqiang Liu"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - LLM-Agent
  - Credit-Assignment
  - Policy-Optimization
  - Graph-Based
  - Long-Horizon
quality_score: "9.8/10"
created: "2026-08-22"
updated: "2026-08-22"
status: analyzed
---

# MileGPO: Milestone Inference with Local Evidence for Graph-Based Policy Optimization of Long-Horizon LLM Agents

## 核心信息
- **论文ID**：arXiv:2608.19803
- **作者**：Bo Qian, Yuting Wu, Shuang Zeng, Huaiyu Wan, Dalin Zhang, Jiqiang Liu
- **机构**：--
- **发布时间**：2026-08-20
- **分类**：cs.LG, cs.AI, cs.CL
- **链接**：[arXiv](https://arxiv.org/abs/2608.19803) | [PDF](https://arxiv.org/pdf/2608.19803)
- **研究领域**：强化学习与智能体 · 信用分配

## 摘要翻译

### 英文摘要
Credit assignment is challenging in long-horizon agentic reinforcement learning, where supervision often comes only from final rewards. Existing methods refine trajectory-level signals into step-level credits through step grouping or graph-based advantage estimation, but can overlook meaningful intermediate milestones. We propose MileGPO (Milestone Inference with Local Evidence for Graph-Based Policy Optimization), which derives process-level credit from grouped on-policy rollouts through three designs. Milestone Discovery identifies candidate milestones on successful rollouts and recurring traps on failed ones. Reliability-Calibrated Shaping (RCS) weights these candidates by outcome-based confidence, strengthening reliable milestones and traps while down-weighting uncertain ones. Progress-Contrastive Calibration (PCC) further tests whether a candidate reflects local progress and whether its incoming transition outperforms observed alternatives from the same state. MileGPO requires neither auxiliary models nor additional environment interaction. Experiments on ALFWorld and WebShop show state-of-the-art performance and a small in-distribution to out-of-distribution gap on ALFWorld. Ablations and credit diagnostics indicate that reliability weighting, local progress, and same-state branch evidence complement milestone discovery and resolve ambiguous intermediate credit.

### 中文翻译
在长程（long-horizon）智能体强化学习中，监督往往只来自最终奖励，信用分配（credit assignment）因此极具挑战。现有方法通过步骤分组或基于图的优势估计，将轨迹级信号细化为步骤级信用，但可能忽略有意义的中间里程碑。作者提出 **MileGPO**（基于局部证据的里程碑推断，用于基于图的策略优化），通过三项设计从分组 on-policy rollout 中推导过程级信用：**里程碑发现（Milestone Discovery）** 在成功 rollout 上识别候选里程碑、在失败 rollout 上识别反复出现的陷阱；**可靠性校准塑形（RCS）** 依据结果置信度对这些候选加权，强化可靠里程碑/陷阱、降权不确定者；**进度对比校准（PCC）** 进一步检验候选是否反映局部进展，以及其入边转移是否优于来自同一状态的已观测替代方案。MileGPO 既不需要辅助模型，也不需要额外的环境交互。在 ALFWorld 与 WebShop 上的实验取得 SOTA 性能，且在 ALFWorld 上分布内到分布外的差距较小。消融与信用诊断表明，可靠性加权、局部进展与同状态分支证据共同补充了里程碑发现，并解决了模糊的中间信用。

### 核心要点提炼
- **研究背景**：长程 agent RL 的信用分配受"仅最终奖励"监督所限，现有过程级信用方法忽略中间里程碑。
- **研究动机**：从轨迹中自动推断"有意义的中间里程碑"，为策略优化提供更可靠的步骤级信号。
- **核心方法**：里程碑发现 + 可靠性校准塑形（RCS）+ 进度对比校准（PCC），三者组合无需辅助模型与额外交互。
- **主要结果**：ALFWorld 与 WebShop 上 SOTA；ALFWorld 上 ID→OOD 差距小。
- **研究意义**：为长程 LLM agent 的过程级信用分配提供了更精细、更廉价（无额外交互）的解决方案。

## 研究背景与动机

### 领域现状
LLM agent 的强化学习通常只有最终奖励作为监督信号，长程任务中该信号稀疏、延迟，导致信用分配困难。现有工作通过步骤分组、基于图的优势估计（如 GAE、过程奖励模型）将轨迹级奖励细化到步骤级。

### 现有方法的局限性
- 步骤分组/图优势估计可能**忽略有意义的中间里程碑**，把注意力放在错误的步骤上。
- 部分方法依赖辅助模型（如过程奖励模型）或额外环境交互，成本高。

### 研究动机
能否在**不引入辅助模型、不增加环境交互**的前提下，自动推断出轨迹中的中间里程碑，并将其转化为可靠的步骤级信用？

## 研究问题

### 核心研究问题
1. 如何从 on-policy rollout 中自动发现"有意义的中间里程碑"？
2. 如何校准这些候选里程碑的可靠性，避免噪声干扰？
3. 如何利用局部进度与同状态分支证据，解决模糊的中间信用？

## 方法概述

### 核心思想
将过程级信用分配建模为"**从分组 rollout 中推断里程碑**"：成功轨迹中的关键进展点 = 里程碑，失败轨迹中的反复卡点 = 陷阱；再通过"结果置信度加权"与"局部进度/分支对比"双重校准，得到可靠、可判别的步骤级信用。

### 方法框架

#### 整体架构
MileGPO 由三项设计构成一个流水线：

![[method-latest_page1.png|800]]

> 图1：MileGPO 方法框架，展示里程碑发现 → 可靠性校准塑形 → 进度对比校准的完整流程。

#### 各模块详细说明

**模块1：里程碑发现（Milestone Discovery）**
- **功能**：在成功 rollout 上识别候选里程碑，在失败 rollout 上识别反复出现的陷阱。
- **输入**：分组的 on-policy rollout 轨迹。
- **输出**：候选里程碑/陷阱集合。

**模块2：可靠性校准塑形（Reliability-Calibrated Shaping, RCS）**
- **功能**：依据结果置信度（outcome-based confidence）对候选里程碑/陷阱加权。
- **机理**：可靠的里程碑/陷阱被强化，不确定者被降权，抑制噪声。

**模块3：进度对比校准（Progress-Contrastive Calibration, PCC）**
- **功能**：检验候选是否反映局部进展，及其入边转移是否优于同状态已观测替代方案。
- **机理**：用"局部进度"与"同状态分支证据"消解模糊的中间信用。

### 方法架构图
![[credit_assignment_page1.png|800]]

> 图2：信用分配对比/诊断，展示 MileGPO 如何推断与校准过程级信用。

## 实验结果

### 实验设置
- **环境/数据集**：ALFWorld、WebShop
- **基线**：现有步骤分组与图优势估计方法
- **评估**：任务成功率、ID→OOD 泛化差距

### 主要结果
- 在 ALFWorld 与 WebShop 上取得 **SOTA** 性能。
- 在 ALFWorld 上分布内到分布外（ID→OOD）的差距较小，泛化性更好。
- 消融与信用诊断表明：可靠性加权、局部进展、同状态分支证据三者**互补**，共同解决模糊中间信用。

### 结果分析
MileGPO 的价值在于**廉价而精细**：无需辅助模型与额外交互即可得到优于现有方法的步骤级信用，且在小 OOD 差距上体现稳健性。

## 深度分析

### 研究价值评估
- **理论贡献**：将"里程碑推断"引入长程 agent 信用分配，补充了纯步骤分组/图优势估计所忽视的中间语义信号。
- **实际应用**：适合预算受限、无额外环境交互成本的 agent 训练场景。
- **领域影响**：为"过程级监督的自动获取"提供了新范式，可与过程奖励模型方向互补。

### 方法优势
- 无需辅助模型、无额外环境交互，训练成本低。
- RCS + PCC 双重校准提升了步骤级信用的可靠性，直接缓解"模糊中间信用"这一长程痛点。

### 局限性
- 摘要披露的评测环境（ALFWorld、WebShop）相对传统，在更开放/长程的 agent 环境上的可扩展性待验证。
- 里程碑发现对分组 rollout 的质量可能敏感。

## 技术路线定位
本文属于 **LLM agent 强化学习 · 信用分配** 技术路线，与过程奖励模型（PRM）、基于图的优势估计等方法并列，是"无奖励模型、无额外交互"这一子方向的代表工作。

## 未来工作建议
1. **与过程奖励模型融合**：用 PRM 提供的外部信号补充里程碑发现，进一步提升信用质量。
2. **扩展到更开放环境**：在 WebArena、OSWorld 等长程环境上验证可扩展性。
3. **里程碑的可解释性**：将推断出的里程碑显式暴露给策略，可能提升策略的可解释性与可控性。

## 我的综合评价

### 价值评分
**9.8/10** — 针对长程 agent 信用分配的核心痛点，提出无额外成本且互补于现有方法的精细方案，实验扎实。

### 重点关注
- RCS 与 PCC 的校准思想，可迁移到其他需要"从稀疏信号中提炼中间监督"的场景。
- "里程碑发现"的自动推断机制，值得深入理解其与人工设计过程监督的差异。

> [!tip] 关键启示
> 长程 agent 的信用分配关键在于"推断有意义的中间里程碑"，而非简单地把轨迹级奖励细分到每一步。

> [!warning] 注意事项
> - 结论基于 ALFWorld/WebShop 等结构化环境，迁移到开放环境需谨慎。
> - "无额外交互"的优势依赖 on-policy rollout 已可获得。
