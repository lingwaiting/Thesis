---
date: "2026-10-12"
paper_id: "arXiv:2610.13504"
title: "Beyond Temporal Proximity: Explanatory World Models for Credit Assignment in Reinforcement Learning"
authors: "Oriol Corcoll, Qiyao Wei, Ciarán Mark Gilligan-Lee"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Credit-Assignment
  - Reinforcement-Learning
  - World-Model
  - Generative-Recommender
quality_score: "8.2/10"
created: "2026-10-12"
updated: "2026-10-12"
status: analyzed
---

# Beyond Temporal Proximity: Explanatory World Models for Credit Assignment in Reinforcement Learning

## 核心信息
- **论文ID**：arXiv:2610.13504
- **作者**：Oriol Corcoll, Qiyao Wei, Ciarán Mark Gilligan-Lee
- **机构**：Spotify + University of Cambridge
- **发布时间**：2026-10-09
- **会议/期刊**：arXiv 预印本（cs.LG / cs.AI / cs.IR）
- **链接**：[arXiv](https://arxiv.org/abs/2610.13504) | [PDF](https://arxiv.org/pdf/2610.13504)
- **引用**：--

## 摘要翻译

### 英文摘要
Generative Recommenders increasingly use Reinforcement Learning (RL) to optimize outcomes that may appear hundreds or thousands of interactions after a decision. Standard RL propagates reward according to temporal proximity rather than explicit relevance, attenuating the signal assigned to distant but important actions and creating a source of sample inefficiency. We introduce Explanatory World Models (EWM), an attribution framework that uses explanations to identify the earlier events that plausibly contributed to a realized outcome. We instantiate EWM with a frontier language model and use its explanations as learning signals using a conservative hierarchical operator that recursively redistributes reward while preserving the undiscounted episode return. To investigate what constitutes a useful explanation, we operationalize three complementary variants inspired by Halpern's actual causation, Lombrozo's explanatory simplicity, and Deutsch's hard-to-vary explanations. We evaluate EWM on two controlled long-horizon RL benchmarks, a public sequential-recommendation benchmark, and an industry-scale generative recommender. On Crafter and WordCraft, EWM substantially outperforms PPO and LaRe, with its largest gains when rewards are most delayed. On KuaiRand, EWM outperforms SFT and LaRe offline and A2C online. Finally, in an offline evaluation of an industry generative recommender, EWM considerably improves Recall and increases behavioral proxies.

### 中文翻译
生成式推荐系统越来越多地使用强化学习（RL）来优化那些可能在一个决策之后数百乃至上千次交互才会显现的结果。标准 RL 按照**时间邻近性而非显式相关性**来传播奖励，削弱了分配给"遥远但重要"的动作的信号，从而造成样本低效。我们提出 **解释性世界模型（Explanatory World Models，EWM）**，一个利用解释来识别"可能促成最终结果出现的早期事件"的归因框架。我们用前沿语言模型实例化 EWM，把它的解释作为学习信号，并通过一个**保守的层次算子**递归地重分配奖励，同时保持无折扣的 episode 回报不变。为探究"什么构成有用的解释"，我们基于 Halpern 的实际因果（actual causation）、Lombrozo 的解释简洁性（explanatory simplicity）与 Deutsch 的"难以变动的解释"（hard-to-vary explanations）三种思想，具体化了三个互补变体。我们在两个受控长时程 RL 基准、一个公开序列推荐基准，以及一个工业级生成式推荐系统上评估 EWM。在 Crafter 与 WordCraft 上，EWM 大幅超越 PPO 和 LaRe，且在奖励最延迟时增益最大；在 KuaiRand 上，EWM 离线超越 SFT 与 LaRe、在线超越 A2C；最后，在工业生成式推荐的离线评估中，EWM 显著提升 Recall 并改善行为代理指标。

### 核心要点提炼
- **研究背景**：生成式推荐用 RL 优化"延迟很久才显现"的长期结果，标准 RL 按时间邻近性分配奖励，导致长时程信用分配失效。
- **研究动机**：需要一种能识别"哪些早期事件真正促成了最终结果"的归因机制，把奖励按相关性而非时间传播。
- **核心方法**：用前沿 LLM 的解释作为归因信号，配合保守层次算子递归重分配奖励，同时保持无折扣回报不变。
- **主要结果**：Crafter/WordCraft 大幅超越 PPO/LaRe（延迟越大增益越大）；KuaiRand 超 SFT/LaRe（离线）与 A2C（在线）；工业推荐 Recall 显著提升。
- **研究意义**：为"用语言模型解释实现长时程信用分配"提供了可复用的结构化归因框架，直接落地于工业推荐。

## 研究背景与动机

### 领域现状
生成式推荐（Generative Recommender）逐步采用 RL 来优化点击、播放时长等长期用户行为。但这些结果往往在决策后数百上千次交互后才显现，形成极长的信用分配链条。标准 RL（如 PPO）以时间折扣或邻近性传播奖励，本质上假设"离结果越近的动作越重要"，在长时程场景下这一假设失效。

### 现有方法的局限性
- **时间邻近性传播**：把奖励按时间衰减传播，分配给"遥远但关键"的动作的信号被严重稀释，样本效率低下。
- **无显式相关性建模**：标准 RL 不区分"恰好发生在结果前"与"真正促成结果"的动作。
- **长时程稀疏奖励**：延迟奖励导致梯度信号稀疏，训练不稳定。

### 研究动机
借鉴因果归因思想，用 LLM 的解释能力去识别"哪些早期事件合理地促成了最终结果"，把这些解释转化为重分配奖励的学习信号，从而把信用从"时间邻近"改为"因果相关"。

## 研究问题

### 核心研究问题
在奖励延迟出现的长时程 RL 场景中，如何利用解释（explanation）识别对结果真正有贡献的早期事件，并按相关性（而非时间邻近性）重分配信用，从而提升样本效率与最终性能？

## 方法概述

### 核心思想
EWM 把"信用分配"视为"归因"问题：让前沿 LLM 读一段交互历史与最终结果，解释"哪些早期事件促成了该结果"；再用一个保守层次算子把这些解释转化为奖励的重分配——保留无折扣 episode 总回报不变，只把奖励从"时间邻近"重新分布到"因果相关"的早期事件上。

### 方法框架

#### 整体架构
EWM 由两部分构成：① 解释生成器（前沿 LLM）——输入历史与结果，输出对早期事件的归因解释；② 保守层次算子——递归地把奖励按解释给出的相关性重新分配，并保持 episode 无折扣回报守恒。三组解释变体分别编码不同的因果/简洁性准则。

![[2610.13504_fig1.png|600]]

> 图1：EWM 概览——LLM 解释生成归因信号，层次算子按相关性重分配奖励。

#### 关键组件说明

**组件1：解释生成器（前沿 LLM）**
- 输入：一段交互轨迹与最终结果（如长期留存）。
- 输出：对"哪些早期事件合理促成了结果"的自然语言解释，作为奖励重分配的归因依据。

**组件2：保守层次算子**
- 递归地把 episode 奖励重新分配到被解释标记为相关的早期事件上。
- 保守性约束：重分配过程保持无折扣 episode 回报不变，只改变奖励的"落点"。

**组件3：三种解释变体**
- **Halpern 实际因果**：强调"若非该事件，结果是否会不同"的反事实归因。
- **Lombrozo 解释简洁性**：偏好更简单、更少的因果要素。
- **Deutsch 难变解释**：偏好难以被轻易改动而不失效的解释。

## 实验结果

### 实验目标
验证 EWM 在长时程 RL 中的信用分配效果，并探究"何种解释最有用"。

### 数据集与基线
- **受控长时程基准**：Crafter、WordCraft。
- **公开序列推荐**：KuaiRand。
- **工业级生成式推荐**：离线评估。
- **基线**：PPO、LaRe、SFT、A2C。

### 主要结果
- **Crafter / WordCraft**：EWM 大幅超越 PPO 与 LaRe，且在奖励最延迟时增益最大。
- **KuaiRand**：离线超越 SFT 与 LaRe，在线超越 A2C。
- **工业生成式推荐**：Recall 显著提升，行为代理指标改善。
- **解释变体**：三种变体互补，编码不同的因果/简洁性准则，均可作为有效归因信号。

### 结果分析
EWM 的核心价值在于"用解释把时间邻近性替换为因果相关性"。延迟越大、时间邻近性假设越失效，EWM 相对标准 RL 的优势越明显——这与"EWM 在奖励最延迟时增益最大"的实验结论一致。层次算子的"回报守恒"设计保证了重分配不改变优化目标的尺度，使其可直接嵌入现有 RL 流程。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：提出"解释性世界模型"概念，把长时程信用分配与语言模型解释/因果归因联系起来。
  - 创新点：用自然语言解释作为奖励重分配的归因信号，开辟了"LLM 辅助 RL"的新接口。
- **贡献2**：设计保守层次算子，在重分配奖励的同时保持无折扣回报守恒。
  - 创新点：保证重分配不破坏优化目标，工程上可直接嵌入现有 RL 训练。

#### 实际应用价值
- **应用场景1**：工业级生成式推荐——直接优化长期留存/满意度等延迟结果，是论文明确验证的落地场景。
- **应用场景2**：任何长时程、稀疏奖励的 RL 场景（对话系统、agent 决策等）。

### 局限性
- 解释质量依赖前沿 LLM 能力，引入额外推理成本；LLM 归因本身的可靠性未被充分审计。
- 主要验证于推荐与受控基准，更复杂的真实交互环境下的鲁棒性待验证。
- "何种解释最有用"的结论依赖三个特定变体，解释空间远未被穷尽。

### 未来工作
- 探索更丰富的解释类型与更轻量的解释生成方式，降低 LLM 调用成本。
- 在更多长时程 RL 任务（agent、对话）上验证通用性。
- 研究解释可信度与奖励重分配稳健性之间的关系。

### 与相关工作对比
- **vs PPO / A2C 等标准 RL**：这些按时间邻近性传播奖励；EWM 用解释按相关性重分配，长时程下更高效。
- **vs LaRe / 奖励塑形（reward shaping）**：LaRe 等仍基于时间/局部信息；EWM 引入语义层面的因果归因。
- **研究路线归属**：属于"LLM 辅助强化学习"与"因果信用分配"的交叉，重点解决长时程稀疏奖励问题。
