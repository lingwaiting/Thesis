---
date: "2026-09-19"
paper_id: "arXiv:2609.20082"
title: "MATCH: Model-Aware Tool Learning with Curriculum Scheduling and Hierarchically Gated Rewards"
authors: "Shihao Liu, Hao Yin, Lijun Liu, Zhengzong Chen, Yuanyuan Zhao, Fei Huang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Tool-Learning
  - Reinforcement-Learning
  - Curriculum-Learning
  - Reward-Shaping
  - LLM-Agent
quality_score: "9.2/10"
created: "2026-09-19"
updated: "2026-09-19"
status: analyzed
---

# MATCH: Model-Aware Tool Learning with Curriculum Scheduling and Hierarchically Gated Rewards

## 核心信息
- **论文ID**：arXiv:2609.20082
- **作者**：Shihao Liu, Hao Yin, Lijun Liu, Zhengzong Chen, Yuanyuan Zhao, Fei Huang
- **机构**：中国科学院大学（UCAS）、荣耀终端有限公司（Honor Device Co., Ltd）
- **发布时间**：2026-09-17
- **会议/期刊**：AAAI 2027（投稿）
- **链接**：[arXiv](https://arxiv.org/abs/2609.20082) | [PDF](https://arxiv.org/pdf/2609.20082)
- **领域**：大语言模型 · 工具学习 · 强化学习

## 摘要翻译

### 英文摘要
Tool learning enables large language models (LLMs) to use external tools for tasks beyond parametric knowledge. Reinforcement learning can optimize tool-call behavior from feedback, but current methods still face two problems: fixed-threshold curricula can become misaligned with the policy's evolving capability boundary, and additive rewards can leak argument-level credit when the predicted tool is wrong. To address these problems, we propose MATCH, a closed-loop framework for model-aware tool learning with curriculum scheduling and hierarchically gated rewards. Model-Aware Curriculum Learning (MACL) maintains reward-derived sample difficulty that co-evolves with the policy, and each epoch selects samples near the current capability boundary together with a top-k pool of harder cases. Hierarchical Tool-call Gated Reward (HTGR) scores tool name, argument key, and argument value as a gated chain, granting credit at each level only when prerequisites hold. The same HTGR rewards drive both GRPO updates and MACL's difficulty refresh, closing the loop between policy optimization and sample scheduling. On API-Bank and BFCL V3, MATCH reaches 72.19% and 62.87% overall accuracy, outperforming the main supervised and RL-based baselines.

### 中文翻译
工具学习使大语言模型（LLM）能够调用外部工具完成超出参数化知识的任务。强化学习可从反馈中优化工具调用行为，但现有方法仍面临两大问题：固定阈值的课程设置可能与策略不断演进的能力边界失配；可加性奖励在预测工具错误时会发生参数级信用泄漏。为此，本文提出 MATCH——一个面向模型感知工具学习的闭环框架，结合课程调度与层级门控奖励。模型感知课程学习（MACL）维护一个与策略共同演化的、由奖励导出的样本难度，每个 epoch 选取靠近当前能力边界的样本，并叠加一个更难样例的 top-k 池。层级工具调用门控奖励（HTGR）将工具名、参数键、参数值作为一个门控链进行评分，只有当所有前置层级正确时才逐级授予信用。同一套 HTGR 奖励同时驱动 GRPO 更新与 MACL 的难度刷新，从而将策略优化与样本调度闭合成环。在 API-Bank 与 BFCL V3 上，MATCH 分别达到 72.19% 与 62.87% 的整体准确率，优于主要的监督式与 RL 式基线。

### 核心要点提炼
- **研究背景**：LLM 工具学习通过 RL 优化工具调用，但样本调度与奖励设计各自存在独立缺陷。
- **研究动机**：固定阈值课程无法追踪策略能力边界；可加性奖励在工具选错时会错误地给参数层打正分。
- **核心方法**：MACL（能力边界感知的课程调度）+ HTGR（工具名/参数键/参数值三层门控奖励），由 GRPO 闭环连接。
- **主要结果**：API-Bank 72.19%、BFCL V3 62.87%，超越监督式与 RL 式基线，且跨 4 个骨干模型一致提升。
- **研究意义**：首次将"样本调度"与"奖励塑形"在工具学习 RL 中联合建模并闭环协同。

## 研究背景与动机

### 领域现状
工具学习让 LLM 能调用搜索、计算器、API 等外部工具，突破纯参数化知识的边界。随着 GRPO 等 RLHF 后训练方法兴起，研究者开始用强化学习直接优化工具调用策略。现有工作通常采用**固定阈值的课程学习**（先易后难）来调度训练样本，并采用**可加性奖励**（格式分 + 工具分）来评分。

### 现有方法的局限性
1. **课程失配**：固定阈值课程假设难度分布稳定，但策略能力边界随训练不断移动，导致阈值要么过早（学不会）要么过晚（无挑战）。
2. **信用泄漏**：可加性奖励把工具名、参数键、参数值各层分数直接相加，当预测的工具名错误时，参数层仍可能拿到分数，造成梯度信号被污染。
3. **调度与奖励解耦**：样本调度器与奖励函数各自独立设计，无法互相利用信息形成闭环。

### 研究动机
作者希望用「策略感知」的课程调度替代固定阈值，用「层级门控」的奖励替代可加性奖励，并让二者共享同一奖励信号形成闭环，从而系统性解决工具学习 RL 中的两个核心缺陷。

## 研究问题

### 核心研究问题
1. 如何让课程调度实时追踪策略演进中的能力边界，避免固定阈值的失配？
2. 如何设计奖励函数，使信用在工具名→参数键→参数值之间逐级门控，消除参数级信用泄漏？
3. 如何让奖励信号同时驱动策略优化与样本调度，形成闭环协同？

## 方法概述

### 核心思想
把工具学习 RL 中的「选什么样本训练」和「怎么给工具调用打分」两个问题统一到同一个奖励信号下闭环求解。MACL 用奖励导出的难度追踪能力边界选样本；HTGR 用门控链逐级授予信用；GRPO 的奖励既更新策略，又回灌给 MACL 刷新难度。

### 方法框架

#### 整体架构
MATCH 由三大模块构成一个闭环：

![[overview_3.png|800]]

> 图1：MATCH 整体框架。左侧 MACL 估计样本难度并在策略能力边界附近选取课程批次、叠加更难样本；中间 GRPO 在选中批次上以 HTGR 奖励更新策略；右侧 HTGR 通过层级门控评估输出格式与工具调用正确性，其奖励同时刷新样本难度，用于下一轮课程选择。

#### 各模块详细说明

**模块1：Model-Aware Curriculum Learning（MACL）**
- **功能**：用固定阈值课程替换为追踪能力边界的动态调度。
- **输入**：离线 rollout 的初始难度 + 在线 GRPO 奖励。
- **输出**：每 epoch 的训练子集 $\mathcal{S}_t=\mathcal{B}_t\cup\mathcal{H}_t$（边界带 ∪ 困难池 top-k）。
- **处理流程**：
  1. 离线初始化：对每个样本跑 $N$ 次随机 rollout，以精确匹配率 $\alpha_i$ 作为初始奖励，难度 $d_i^{(0)}=1-\alpha_i$。
  2. 在线刷新：用 GRPO 每组的 $M$ 个奖励做归一化 $\tilde{R}_i^{(t)}$，再以指数滑动平均 $\hat{R}_i^{(t)}=\eta\hat{R}_i^{(t-1)}+(1-\eta)\tilde{R}_i^{(t)}$ 更新难度。
  3. 批次选择：以全局平均难度 $\mu_t$ 作为能力边界，选 $|d_i-\mu_t|\le\epsilon_{\mathrm{band}}$ 的边界带，外加按 $r_{\mathrm{hard}}$ 比例从更难池中 top-k 采样。
- **关键技术**：指数滑动平均降低 rollout 方差；边界带 + top-k 双通道保证"够得着 + 有挑战"。

**模块2：Hierarchical Tool-call Gated Reward（HTGR）**
- **功能**：把工具调用拆成"工具名→参数键→参数值"三级门控链，逐级授予信用。
- **输入**：模型生成的工具调用序列。
- **输出**：总分 $R=\lambda R_{\mathrm{fmt}}+R_{\mathrm{tool}}$，其中 $R_{\mathrm{tool}}$ 是门控链得分。
- **处理流程**：
  1. 工具名正确才授予该层分数，否则后续参数层全部置零。
  2. 工具名正确时，参数键正确才授予参数键层分数。
  3. 参数键正确时，参数值正确才授予参数值层分数。
- **关键技术**：门控链将信用严格限制在"前置正确"的范围内，从机制上消除参数级信用泄漏。

**模块3：Policy Update（GRPO）**
- **功能**：用 HTGR 奖励对选中批次做策略优化。
- **输入**：选中子集 $\mathcal{S}_t$ 上的 rollout 与 HTGR 奖励。
- **输出**：更新后的策略 + 回灌给 MACL 的难度信号。
- **关键技术**：GRPO 的组内相对优势估计 + 裁剪代理目标，与 HTGR 奖励天然兼容。

### 方法架构图
![[overview_3.png|800]]

> 图1（同）：MATCH 的闭环结构，MACL（左）— GRPO（中）— HTGR（右）三者共享同一奖励信号。

## 实验结果

### 实验目标
验证 MATCH 在工具学习基准上的整体准确率、组件贡献（消融）、超参数敏感性、课程调度策略与骨干泛化能力。

### 数据集与基线
- **数据集**：API-Bank（工具调用对话）、BFCL V3（伯克利函数调用排行榜）。
- **基线**：主流监督式方法（SFT）与 RL 式方法（如 GRPO 变体）。
- **评估指标**：整体准确率（overall accuracy）、按级别细分的准确率。

### 主要结果

| 方法 | API-Bank 准确率 | BFCL V3 准确率 |
|------|----------------|----------------|
| 监督式基线 | 低于 MATCH | 低于 MATCH |
| RL 式基线 | 低于 MATCH | 低于 MATCH |
| **MATCH** | **72.19%** | **62.87%** |

> 注：MATCH 在两个基准上均超越主要监督式与 RL 式基线。

#### 结果分析
- 主实验（RQ1）：MATCH 在 API-Bank 与 BFCL V3 上分别达 72.19% 与 62.87%，证明闭环协同优于解耦设计。
- 消融（RQ2）：MACL 与 HTGR 各自移除后性能均下降，说明两者都必不可少且相互增强。
- 骨干泛化（RQ5）：在四个骨干模型（两个模型家族）上一致提升，说明框架不依赖特定骨干。

### 消融实验
- **MACL 消融**：移除动态调度、退化为固定阈值课程后，能力边界失配导致性能下降。
- **HTGR 消融**：移除门控、退化为可加性奖励后，信用泄漏使参数层信号被污染。
- **闭环消融**：切断"奖励回灌难度"的路径后，调度与优化脱节。

### 实验结果图
![[curriculum_sampling_strategy_detail.png|800]]

> 图2：课程采样策略细节，展示边界带与 top-k 困难池的构成方式。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：策略感知的课程调度**——用奖励导出的难度实时追踪能力边界，把"固定阈值"范式升级为"能力边界"范式。
  - 创新点：难度与策略共同演化，选样始终"够得着 + 有挑战"。
  - 学术价值：为工具学习 RL 提供新的样本调度理论框架。
- **贡献2：层级门控奖励**——工具名/参数键/参数值三级门控链，机制性消除参数级信用泄漏。
  - 创新点：把"可加性"替换为"前置门控"，奖励信号更干净。
  - 学术价值：可推广到其他结构化输出的 RL 场景（如代码、SQL 生成）。
- **贡献3：闭环协同**——同一奖励信号同时驱动优化与调度，形成正反馈。

#### 实际应用价值
- **应用场景1：Agent 工具调用后训练**——用 RL 训练 Agent 精准调用工具。
- **应用场景2：函数调用（function calling）对齐**——BFCL 场景下的 API 调用优化。

#### 领域影响
- **短期**：为工具学习 RL 提供即插即用的调度 + 奖励方案。
- **中期**：推动 Agent 工具调用从 SFT 向 RL 后训练迁移。
- **长期**：层级门控奖励可能成为结构化输出 RL 的通用组件。

### 方法优势详解
1. **闭环性**：调度、奖励、优化共享同一信号，避免各自为政。
2. **鲁棒性**：跨四个骨干、两个模型家族一致提升。
3. **可解释性**：门控链清晰刻画"信用该给到哪一层"。

### 局限性分析
1. **任务覆盖面**：仅在工具调用类任务验证，尚未扩展到一般推理/生成。
2. **超参数敏感**：$\epsilon_{\mathrm{band}}$、$r_{\mathrm{hard}}$、$\eta$ 等需调优。
3. **离线初始化开销**：需对初始策略跑 $N$ 次 rollout 初始化难度。

### 适用性与场景分析
- **适用**：工具/函数调用类 RL 后训练、Agent 动作空间为结构化输出的场景。
- **不适用**：纯自由文本生成、无明确工具/参数结构的任务。

## 我的综合评价

### 价值评分

#### 总体评分
**9.2/10** — 选题精准（工具学习 RL 的两大痛点）、方法闭环优雅、实验扎实（双基准 + 多骨干消融），是工具学习 RL 方向的高质量工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 门控链奖励 + 能力边界调度组合新颖，闭环设计有范式意义 |
| 技术质量 | 9/10 | 数学定义严谨（EMA、Sinkhorn 式门控），实现清晰 |
| 实验充分性 | 9/10 | 双基准 + 消融 + 超参 + 调度策略 + 骨干泛化，五组 RQ 覆盖全面 |
| 写作质量 | 9/10 | 结构清晰，公式规范 |
| 实用性 | 9/10 | 工具学习 RL 可直接落地 |

### 重点关注
- **值得关注的技术点**：HTGR 的门控链如何与 GRPO 组内优势估计兼容；MACL 的 EMA 难度估计如何平衡方差与偏差。
- **需要深入理解的部分**：归一化奖励 $\tilde{R}_i^{(t)}$ 的 clip 边界如何影响难度分布。

## 相关论文

### 直接相关
- 工具学习 RL 的前序工作（GRPO 工具调用）
- 课程学习（curriculum learning）与动态采样

### 后续工作
- 门控奖励在代码/结构化输出 RL 中的推广

> [!tip] 关键启示
> 把"样本调度"与"奖励塑形"统一到同一奖励信号下闭环求解，是工具学习 RL 的关键设计范式。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐——工具学习 RL 的调度与奖励问题被系统性解决，方法论可迁移到更广泛的结构化输出场景。
