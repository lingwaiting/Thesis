---
date: "2026-11-12"
paper_id: "2611.14247"
title: "Personalized Affective Perception via Continual Learning in Human-Agent Negotiation"
authors: "Mehmet Onur Keskin, Berk Buzcu, Nikhil Churamani, Reyhan Aydogan, Hatice Gunes, Catholijn Jonker"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Affective-Computing
  - Continual-Learning
  - Human-Agent-Negotiation
  - Facial-Expression-Recognition
quality_score: "8.0/10"
related_papers: []
created: "2026-11-12"
updated: "2026-11-12"
status: analyzed
---

# Personalized Affective Perception via Continual Learning in Human-Agent Negotiation

## 核心信息
- **论文ID**：2611.14247
- **作者**：Mehmet Onur Keskin, Berk Buzcu, Nikhil Churamani, Reyhan Aydogan, Hatice Gunes, Catholijn Jonker
- **机构**：--（涉及剑桥大学 Hatice Gunes 情感计算组、代尔夫特理工 Catholijn Jonker 谈判智能体组）
- **发布时间**：2026-11-10
- **会议/期刊**：arXiv 预印本（cs.AI / cs.HC / cs.LG / cs.MA / cs.RO）
- **链接**：[arXiv](https://arxiv.org/abs/2611.14247) | [PDF](https://arxiv.org/pdf/2611.14247)
- **引用**：--

## 摘要翻译

### 英文摘要
Self-interested intelligent agents that negotiate with humans should interpret nonverbal expressions and adapt their behavior accordingly. However, generic facial-affect models degrade across individuals and contexts, creating a generalization gap. We introduce an affect-adaptive negotiation strategy that incorporates Continual Learning with Imagination for Facial Expression Recognition (CLIFER) to personalize valence-arousal perception in real time. Differential feedback converts changes in valence-arousal direction and intensity into adjustments of the agent's target utility. An asynchronous architecture updates CLIFER in the background during live interaction. In a counterbalanced within-subject study in which 66 participants negotiated with a humanoid robot, the CL-enabled agent achieved significantly higher individual utility and elicited higher participant concession rates than the FaceChannel baseline. The CL condition yielded significantly higher mean arousal estimates, alongside a broader output range. Participant utility and Nash distance did not change significantly between conditions.

### 中文翻译
与人谈判的自利型智能体应当解读对方的非语言表达并据此调整自身行为。然而，通用的面部情感模型在不同个体和情境之间会退化，形成泛化差距。本文提出一种情感自适应谈判策略，引入"带想象力的持续学习面部表情识别"（CLIFER），实时个性化地感知效价-唤醒度（valence-arousal）。差分反馈将效价-唤醒度方向与强度的变化转换为对智能体目标效用的调整；一个异步架构在实时交互过程中于后台更新 CLIFER。在一项 66 名参与者与人形机器人谈判的平衡组内实验中，启用持续学习（CL）的智能体相比 FaceChannel 基线取得了显著更高的个体效用，并诱导出更高的参与者让步率。CL 条件也产生了显著更高的平均唤醒度估计值，且输出范围更宽；而参与者效用与纳什距离在两个条件之间无显著变化。

### 核心要点提炼
- **研究背景**：情感计算与自主谈判智能体的交叉，通用情感模型缺乏个体化适应能力。
- **研究动机**：通用面部情感模型在"个体间/情境间"退化，导致谈判智能体无法准确解读对手情绪。
- **核心方法**：CLIFER 持续学习 + 差分反馈驱动效用调整 + 异步后台更新。
- **主要结果**：CL 智能体显著提升个体效用与对手让步率，唤醒度估计更高。
- **研究意义**：证明"持续情感个性化 + 差分反馈"在实时谈判中的实用价值。

## 研究背景与动机

### 领域现状
自动谈判（Automated Negotiation）与情感计算（Affective Computing）长期并行发展。近年来，将情感感知引入谈判智能体的工作开始出现：智能体通过识别对手的微表情、语音、姿态等非语言信号，动态调整出价策略，以争取更有利的结果。情感感知的主流技术路线是通用面部表情识别模型，例如基于视频的 valence-arousal 连续维度回归模型。

### 现有方法的局限性
通用面部情感模型存在"泛化差距"（generalization gap）：它们在大规模数据上训练，但一旦面对特定个体（如表情习惯差异）或特定情境（如谈判压力下的克制表情），预测精度会明显下降。谈判场景尤其棘手——对手往往是"有意克制"的，其真实情绪被掩藏在谈判策略之下。离线一次性训练的模型无法在线适应这种个体差异。

### 研究动机
作者的核心判断是：谈判智能体需要"个体化"的情感感知，而非"一刀切"的通用模型。持续学习（Continual Learning, CL）天然适合这一目标——在交互中不断用新样本更新模型，使其逐步贴近当前对手。本文首次将 CLIFER 这一"带想象力的持续学习"方法引入谈判场景，并用差分反馈把情感信号真正转化为策略调整。

## 研究问题

### 核心研究问题
1. 能否在实时人机谈判中，用持续学习实现**个体化的效价-唤醒度感知**？
2. 如何将情感感知结果**结构化地反馈**到谈判策略（目标效用）中？
3. 异步在线更新是否会带来可观测的**谈判结果改善**（个体效用、对手让步率、唤醒度估计）？

## 方法概述

### 核心思想
把"情感感知"从静态的一次性预测，改造成一个闭环：CLIFER 在线估计对手的效价-唤醒度 → 差分反馈计算其方向与强度变化 → 智能体据此上调/下调自己的目标效用 → 后台异步用新样本持续更新 CLIFER，使其越来越贴合当前对手。

### 方法框架

#### 整体架构

![[HAIFramework.png|800]]

> 图1：HAI 框架总览。上半部分为情感感知模块（CLIFER 持续学习面部表情识别），下半部分为谈判决策模块；二者通过"差分反馈"连接——情感变化被转换为目标效用的调整量，形成感知-决策闭环。

#### 各模块详细说明

**模块1：CLIFER 情感感知**
- **功能**：实时回归对手面部的效价（valence）与唤醒度（arousal）两个连续维度。
- **输入**：谈判过程中对手的面部视频帧序列。
- **输出**：valence-arousal 二维坐标（方向 + 强度）。
- **处理流程**：
  1. 提取面部特征。
  2. 用持续学习机制在线更新回归模型，个性化适配当前个体。
  3. 通过"想象"（imagination）机制生成伪样本缓解灾难性遗忘。
- **关键技术**：Continual Learning with Imagination（CLIFER），在持续学习中用想象生成的样本巩固旧知识。

**模块2：差分反馈**
- **功能**：把情感估计的**变化**（而非绝对值）转化为策略信号。
- **输入**：相邻时刻的 valence-arousal 估计值。
- **输出**：目标效用的调整方向与幅度。
- **处理流程**：
  1. 计算 valence-arousal 方向与强度的变化量。
  2. 对手情绪变得更积极 → 智能体可维持/提升目标效用；情绪变消极 → 降低目标效用以维持合作。
  3. 将变化量映射为效用调整步长。
- **关键技术**：差分（differential）信号，强调"趋势"而非"瞬时值"。

**模块3：谈判决策与异步更新**
- **功能**：执行谈判出价策略，并在后台更新 CLIFER。
- **输入**：当前目标效用、对手出价、情感反馈。
- **输出**：下一轮出价。
- **处理流程**：
  1. 根据调整后的目标效用生成出价。
  2. 交互数据异步进入 CLIFER 更新队列（不阻塞谈判主循环）。
- **关键技术**：异步（asynchronous）架构，将"学习"与"交互"解耦，保证实时性。

### 关键创新
1. **首次将 CLIFER 引入实时人机谈判** - 把持续学习从离线 benchmark 搬到真实交互闭环。
2. **差分反馈机制** - 用情感变化的"方向与强度"而非绝对值来调整效用，更鲁棒。
3. **异步更新架构** - 学习与交互解耦，保证谈判不因在线学习而卡顿。

## 实验结果

### 数据集
- 66 名参与者，与人形机器人（NAO）进行平衡组内（counterbalanced within-subject）谈判实验。
- 条件：CL 条件（启用持续学习）vs FaceChannel 基线条件。

### 实验设置
- **基线方法**：FaceChannel（静态通用面部表情识别模型）。
- **评估指标**：个体效用（individual utility）、参与者让步率（concession rate）、参与者效用、纳什距离（Nash distance）、唤醒度/效价估计值。
- **实验环境**：人形机器人实机交互。

### 主要结果
| 指标 | CL 条件 | FaceChannel 基线 | 显著性 |
|------|---------|------------------|--------|
| 智能体个体效用 | 更高 | 基线 | 显著 |
| 参与者让步率 | 更高 | 基线 | 显著 |
| 平均唤醒度估计 | 更高 | 基线 | 显著 |
| 参与者效用 | 无显著差异 | — | 不显著 |
| 纳什距离 | 无显著差异 | — | 不显著 |

> 注：CL 条件在"智能体自身收益"和"对手让步"上取得显著优势，且唤醒度估计更活跃；但并未损害对手的客观效用与整体协议质量（纳什距离不变），说明提升并非以牺牲对手为代价。

## 深度分析

### 研究价值
- **理论贡献**：把持续学习、情感计算与自动谈判三者打通，提出"情感自适应谈判"的统一框架。
- **实际应用**：面向服务机器人、智能客服、虚拟谈判助理等需要"察言观色"的交互系统。
- **领域影响**：为"情感智能体"从离线评测走向在线闭环提供了可复用的技术范式。

### 优势
- 闭环设计完整：感知 → 反馈 → 决策 → 在线学习形成完整回路。
- 差分反馈对个体差异更鲁棒，避免了绝对阈值在不同人身上的失灵。
- 异步架构兼顾了模型更新与实时性。

### 局限性
- 样本量（66 人）与单一谈判任务，泛化到更复杂谈判协议有待验证。
- 差分反馈的映射函数（情感变化 → 效用步长）需要更多消融。
- 未深入分析 CLIFER 的灾难性遗忘在长时交互中的累积效应。

### 适用场景
- 情感感知需求强、且需要个体化适配的实时人机交互。
- 服务机器人、教育陪伴、辅助谈判等场景。

## 与相关论文对比

### FaceChannel（基线）- 对比关系
- **差异**：FaceChannel 为静态通用模型，本文在其基础上叠加持续学习与差分反馈。
- **改进**：个体化适配能力、情感→策略的显式反馈闭环。
- **性能对比**：CL 条件在个体效用、对手让步率、唤醒度估计上均显著优于 FaceChannel。

## 技术路线定位

本文属于"情感智能体 / 情感自适应交互"技术路线，核心特点是：感知-决策-学习三者闭环。发展脉络大致为：通用情感识别 → 情境化情感识别 → 持续学习情感个性化 → 情感驱动策略调整。本文的关键节点在于**首次把持续学习与差分反馈同时引入实时谈判闭环**。

## 未来工作建议

1. **更大规模、多轮次谈判协议验证** - 提升外部效度。
2. **差分反馈映射的消融研究** - 定量分析情感变化到效用调整的最优映射。
3. **长时交互下的遗忘控制** - 结合 memory replay 进一步稳定 CLIFER。

## 我的综合评价

### 价值评分

**8.0/10** - 思路清晰、闭环完整的交叉领域工作，实测验证扎实，但规模与协议单一。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 持续学习 + 差分反馈 + 谈判的组合有新意 |
| 技术质量 | 8/10 | 异步架构与对照实验设计严谨 |
| 实验充分性 | 7/10 | 66 人实机实验可靠，但协议与规模单一 |
| 写作质量 | 8/10 | 结构清晰，量化结果完整 |
| 实用性 | 8/10 | 服务机器人等场景有直接应用价值 |

> [!tip] 关键启示
> 情感感知的价值不在"识别得准"，而在"能否把识别结果转化为策略动作"——本文的差分反馈闭环正是这一思路的落地。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读：情感智能体与自动谈判交叉领域的一次务实探索，闭环设计值得借鉴。
