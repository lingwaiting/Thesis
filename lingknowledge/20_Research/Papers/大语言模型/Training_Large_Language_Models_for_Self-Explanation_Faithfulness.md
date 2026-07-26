---
date: "2026-07-26"
paper_id: "arXiv:2607.21090"
title: "Training Large Language Models for Self-Explanation Faithfulness"
authors: "Yeoktatt Cheah, María Pérez-Ortiz, Noah Y. Siegel, Oana-Maria Camburu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Alignment
  - Self-Explanation
  - Faithfulness
  - Reinforcement-Learning
  - Interpretability
quality_score: "8.5/10"
created: "2026-07-26"
updated: "2026-07-26"
status: analyzed
---

# Training Large Language Models for Self-Explanation Faithfulness

## 核心信息
- **论文ID**：arXiv:2607.21090
- **作者**：Yeoktatt Cheah, María Pérez-Ortiz, Noah Y. Siegel, Oana-Maria Camburu
- **机构**：--
- **发布时间**：2026-07-23
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2607.21090v1) | [PDF](https://arxiv.org/pdf/2607.21090v1)
- **引用**：--

## 摘要翻译

### 英文摘要
We propose a Reinforcement Learning (RL) method to directly optimize the faithfulness of self-explanations - the extent to which a model's generated reasoning accurately reflects its internal decision-making process. While existing work focuses on evaluating faithfulness or using inference-time prompting frameworks to improve an LLM's self-explanation's tractability, these approaches do not provide a mechanism to directly optimize a model's parameters to generate faithful self-explanations. We bridge this gap by modifying existing faithfulness metrics into an RL training objective.

We investigate (1) if models can be trained to accurately detect factors that affect their decisions, and (2) whether RL can directly optimize for the disclosure of these factors thereby improving LLM self-explanations' faithfulness. We experiment with two intervention types: random-word insertions and user-bias insertions, using a per-sample reward derived from the Phi-CCT correlation metric. RL fine-tuned Llama3.1-8B and Qwen3-8B show substantial improvements on the Phi-CCT faithfulness metric, with in-distribution scores rising from near-zero to as high as 0.664, and out-of-distribution scores reaching up to 0.691 on held-out tasks such as StrategyQA.

Cross-intervention generalization is weaker but more interesting: a priori we would not expect a model trained only on random word insertions to generalize to user-bias phrases, yet Llama3.1-8B shows non-zero transfer in this direction. The reverse direction and Qwen3-8B do not replicate this, indicating model-dependent and setup-dependent effects we cannot yet explain. Lastly we analyze model behavior to rule out reward gaming behaviors that often plague RL training. Ultimately, we show that models can be trained to implicitly identify influential factors and disclose them, offering a scalable path toward reducing unfaithful reasoning in LLMs.

### 中文翻译
我们提出一种强化学习（RL）方法，直接优化自我解释的忠实度——即模型生成的推理在多大程度上准确反映其内部决策过程。现有工作专注于评估忠实度或使用推理时提示框架来改善LLM自我解释的可追溯性，但这些方法不提供直接优化模型参数以生成忠实自我解释的机制。我们通过将现有忠实度度量改造为RL训练目标来弥补这一空白。

我们研究：(1) 模型是否可以被训练为准确检测影响其决策的因素，以及(2) RL是否可以直接优化这些因素的披露，从而改善LLM自我解释的忠实度。我们实验了两种干预类型：随机词插入和用户偏见插入，使用基于Phi-CCT相关度量的逐样本奖励。RL微调后的Llama3.1-8B和Qwen3-8B在Phi-CCT忠实度度量上显示出大幅改进，分布内分数从接近零提升至最高0.664，分布外分数在StrategyQA等保留任务上达到0.691。

跨干预泛化较弱但更有趣：先验上我们不期望仅在随机词插入上训练的模型能泛化到用户偏见短语，然而Llama3.1-8B在此方向上显示非零迁移。反向和Qwen3-8B未能复现，表明存在我们尚无法解释的模型依赖和设置依赖效应。最后我们分析模型行为以排除常困扰RL训练的奖励欺骗行为。最终，我们证明模型可以被训练为隐式识别影响因素并披露它们，为减少LLM中不忠实推理提供了可扩展的路径。

### 核心要点提炼
- **研究背景**：LLM的自我解释（CoT推理）越来越普遍，但这些解释是否忠实于模型的真实决策过程是一个关键但未解决的问题
- **研究动机**：现有方法仅评估忠实度或通过推理时提示改善，缺乏直接优化模型参数生成忠实解释的机制
- **核心方法**：将Phi-CCT忠实度度量改造为RL奖励，通过PPO直接优化模型参数使其生成忠实自我解释
- **主要结果**：分布内Phi-CCT从接近零提升至0.664，分布外达0.691；发现不对称跨干预泛化
- **研究意义**：首次证明可以通过RL直接训练LLM提高自我解释忠实度，为LLM可解释性和对齐开辟新路径

## 研究背景与动机

### 领域现状
随着Chain-of-Thought推理在LLM中的普及，模型不仅输出答案，还输出"推理过程"。但一个关键问题浮现：**模型说出的推理是否真的反映了其内部决策过程？** 即自我解释的忠实度（faithfulness）问题。

现有方法分为两类：
1. **评估方法**：设计度量（如Phi-CCT）来量化解释的忠实度，但不改变模型行为
2. **推理时方法**：使用提示工程引导模型生成更可追溯的解释，但不改变模型参数

### 现有方法的局限性
- **评估不改变行为**：知道解释不忠实并不能使其变得忠实
- **推理时提示不可靠**：提示工程效果不稳定，且不改变模型的固有倾向
- **缺乏训练时优化**：没有一个机制在训练时直接以"忠实度"为目标优化模型参数

### 研究动机
论文的核心动机是：**如果我们可以将忠实度度量转化为可优化的训练目标，那么我们就可以用RL直接训练模型生成忠实的自我解释**。这是一个从"评估"到"优化"的范式转变，类似于RLHF将人类偏好转化为优化目标。

## 研究问题

### 核心研究问题
1. LLM能否被训练为**准确检测**影响其决策的因素（如文本中的随机词或偏见短语）？
2. RL能否**直接优化**模型对这些影响因素的**披露**，从而提高自我解释的忠实度？
3. 对一种干预类型训练的忠实度能否**泛化**到其他类型的干预？

## 方法概述

### 核心思想
将忠实度评估度量（Phi-CCT）转化为RL奖励信号。核心逻辑：向输入文本注入已知的干扰因素（随机词或用户偏见），然后检查模型的自我解释是否提及这些因素。如果解释准确反映了干扰的影响，就给予正向奖励。通过PPO最大化这个奖励，模型的参数被直接优化以生成更忠实的自我解释。

### 方法框架

#### 整体架构

训练流程包含以下关键组件：

```
输入文本 → [注入干扰因素] → LLM生成回答+自我解释 → [Phi-CCT计算忠实度] → RL奖励 → PPO更新模型
```

**两阶段训练**：
1. **阶段1：干预生成** — 向输入文本注入已知干扰
   - **随机词插入**：在文本中随机位置插入无关词语
   - **用户偏见插入**：在文本中添加暗示特定答案的偏见短语
2. **阶段2：RL优化** — 使用PPO最大化Phi-CCT奖励

#### 各模块详细说明

**模块1：干预注入**
- **功能**：创建"已知答案"的影响因素，使忠实度可量化
- **随机词插入**：
  - 在输入文本中随机插入与任务无关的词语
  - 忠实的解释应该提及"某些无关词语影响了判断"
  - 不忠实的解释会忽略这些插入但被影响决策
- **用户偏见插入**：
  - 插入暗示特定答案的短语（如"我认为答案是X"）
  - 检验模型是否能识别并披露偏见的影响
- **设计原则**：干预是可验证的ground truth——我们知道模型"应该"提及什么

**模块2：Phi-CCT奖励计算**
- **功能**：量化自我解释与真实决策因素的一致性
- **计算方式**：
  - 对每个样本，评估解释是否提及了已知的干预因素
  - Phi-CCT（Correlation-based Faithfulness metric）度量解释中因素提及与因素实际影响之间的相关性
  - 高Phi-CCT意味着解释准确反映了哪些因素影响了决策
- **奖励范围**：标准化为可用于PPO的连续奖励信号

**模块3：PPO训练**
- **功能**：使用Phi-CCT奖励优化LLM参数
- **基础模型**：Llama3.1-8B 和 Qwen3-8B
- **训练细节**：
  - 每样本Phi-CCT作为奖励
  - KL散度约束防止模型偏离太远
  - 同时监控下游任务准确率防止性能退化

### 关键设计决策

**为什么使用已知干预而非开放域忠实度？**
- 开放域的忠实度难以量化——我们不知道模型的"真实"推理过程
- 通过注入已知干预，创建了可验证的ground truth
- 类似"受控实验"的思路：控制变量，测量忠实度

## 实验结果

### 实验目标
验证RL训练能否提高自我解释忠实度，检验跨干预泛化能力，排除奖励欺骗。

### 数据集
- **分布内**：训练时使用的任务类型
- **分布外**：StrategyQA等保留任务，测试泛化

### 主要结果

#### 主实验结果

![[confusion_matrices_Llama-3.1-8B_page1.png|800]]

> 图1：Llama3.1-8B的混淆矩阵，展示了RL训练前后模型在检测和披露影响因素方面的变化。

![[confusion_matrices_Qwen3-8B_page1.png|800]]

> 图2：Qwen3-8B的混淆矩阵结果。

#### 结果分析
- **Phi-CCT大幅提升**：从接近零（随机水平）提升至0.664（分布内）和0.691（分布外）
- **两个模型均显著改善**：Llama3.1-8B和Qwen3-8B都展现了实质性的忠实度提升
- **分布外泛化**：在未见的StrategyQA任务上也达到0.691，表明学习到的忠实度能力具有泛化性

#### 跨干预泛化（关键发现）
- **Llama3.1-8B**：在随机词插入上训练→对用户偏见插入有非零泛化（不对称）
- **Qwen3-8B**：不展现此类泛化
- **反向泛化**（偏见→随机词）：两个模型均不展现
- **解释**：存在模型依赖和方向依赖的泛化特性，可能与模型架构和预训练数据有关

#### 奖励欺骗排除
- 通过行为分析确认PPO训练没有导致模型学会"欺骗"奖励
- 模型确实学会了识别和披露影响因素，而非找到绕开度量的捷径

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：首次将忠实度优化形式化为RL问题**
  - 创新点：从"评估忠实度"到"优化忠实度"的范式转变
  - 学术价值：为LLM可解释性研究提供了训练时优化的新工具
  - 影响范围：可推广到其他形式的模型解释（如特征归因、反事实解释）

- **贡献2：受控干预实验设计**
  - 创新点：通过注入已知影响因素创建可验证的忠实度ground truth
  - 学术价值：提供了一种系统评估和优化忠实度的实验范式
  - 影响范围：方法论贡献，可用于其他可解释性研究

- **贡献3：跨干预泛化的不对称性发现**
  - 创新点：首次报告忠实度训练的跨干预泛化现象及其模型依赖性和方向性
  - 学术价值：揭示了忠实度学习的深层特性，提示需要进一步的理论解释

#### 实际应用价值
- **应用场景1：高风险AI系统的可审计性**
  - 适用性：高——需要模型能够准确解释其决策
  - 优势：直接优化忠实度而非依赖不稳定的提示工程
  - 潜在影响：医疗诊断、法律判断等场景中提高AI决策的透明度

- **应用场景2：AI对齐**
  - 适用性：高——忠实解释是检测和纠正不对齐行为的前提
  - 优势：模型"说出真相"的能力是对齐的基础
  - 潜在影响：减少reward hacking和deceptive alignment的风险

### 方法优势详解

#### 优势1：直接优化而非推理时修补
- **描述**：通过RL改变模型参数，而非依赖推理时的提示工程
- **技术基础**：PPO算法将忠实度量转化为可优化目标
- **实验验证**：Phi-CCT提升超过0.6，远超推理时方法的典型效果
- **对比分析**：提示工程效果不稳定且不改变模型固有倾向；RL训练产生持久的行为改变

#### 优势2：受控实验设计提供可靠ground truth
- **描述**：已知干预因素的设计使得忠实度评估具有可验证性
- **技术基础**：干预是可控的——明确知道模型"应该"披露什么
- **实验验证**：Phi-CCT度量的有效性建立在已知ground truth的基础上
- **对比分析**：开放域忠实度评估缺乏ground truth，可靠性质疑更多

### 局限性分析

#### 局限1：局限于简单干预类型
- **描述**：仅测试了随机词和偏见短语两种干预
- **原因**：概念验证阶段，需要可控的干预来建立方法有效性
- **影响**：真实世界的影响因素更复杂（隐性偏见、上下文线索等）
- **可能的解决方案**：扩展到更多自然发生的干扰类型

#### 局限2：跨干预泛化的不稳定性
- **描述**：泛化是模型依赖和方向依赖的
- **原因**：不同模型的架构和预训练数据可能影响忠实度学习的表征
- **影响**：不能保证对一种干预的训练能泛化到其他类型

#### 局限3：Phi-CCT作为忠实度度量的局限性
- **描述**：Phi-CCT度量的是"因素提及"而非"真实推理过程"
- **原因**：真实推理过程本质不可观测
- **影响**：高Phi-CCT可能是"学会了提及什么"而非"真正忠实地推理"

## 与技术路线的关联

### 所属技术路线
本文属于**LLM可解释性与对齐**技术路线，特别是**训练时忠实度优化**这一新兴子方向。

### 发展历程
```
可解释性评估 → 推理时提示改善 → 训练时忠实度优化 [本文]
    ↑              ↑              ↑
 Phi-CCT等     CoT提示工程    RL忠实度训练
```

### 关键定位
本文是从"评估和推理时修补"到"训练时优化"的关键转折点。类似于RLHF将人类偏好从评估对象变为优化目标，本文使"忠实度"成为可直接优化的训练信号。

## 未来工作建议

### 基于分析的未来方向
1. **方向1：扩展到更自然的干预类型**
   - 动机：真实场景的干扰更复杂
   - 可能的方法：使用对抗生成或人工标注创建更真实的干预
   
2. **方向2：多维度忠实度**
   - 动机：当前仅考虑"因素提及"，真实忠实度是多维的
   - 可能的方法：结合反事实忠实度、机制可解释性等多个维度

3. **方向3：理解跨干预泛化的机制**
   - 动机：泛化不对称性的理论解释缺失
   - 可能的方法：分析模型内部表征的变化

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** — 从"评估忠实度"到"优化忠实度"的范式转变具有重要的学术和实际价值。方法设计精巧（受控干预+RL），实验结果清晰有力。跨干预泛化的发现虽不够完美但提供了宝贵的研究线索。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 首次将忠实度优化形式化为RL训练问题，范式级别的创新 |
| 技术质量 | 8/10 | 方法设计严谨（受控干预、RL训练、奖励欺骗排除），但干预类型有限 |
| 实验充分性 | 8/10 | 两个模型、两种干预、分布内外测试全面；跨干预泛化分析是加分项 |
| 写作质量 | 9/10 | 逻辑清晰，实验设计动机明确，发现和局限诚实呈现 |
| 实用性 | 8/10 | 方法有直接应用价值，但需要扩展到更自然的干预类型 |

### 重点关注
- Phi-CCT从近零到0.664的飞跃——证明忠实度可训练
- 跨干预泛化的不对称性——这可能是未来研究的金矿
- 奖励欺骗的排除——RL训练常见陷阱的有效应对

> [!tip] 关键启示
> 忠实度可以且应该成为训练目标而非仅评估标准。正如RLHF将人类偏好转化为优化目标，本文开创了将"可解释性度量"转化为"可优化目标"的道路。

> [!warning] 注意事项
> - Phi-CCT高≠真正忠实，可能只是学会了"提及什么"
> - 跨干预泛化不可靠——不要假设对一种干预的忠实度训练能泛化到其他场景
> - 方法目前仅适用于已知干预的受控场景

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！LLM可解释性与对齐领域的重要进展，开创了忠实度训练的新范式。

## 相关论文

### 直接相关
- [[Chain-of-Thought]] - CoT推理是本文研究的自我解释形式的基础

### 背景相关
- [[RLHF]] - 本文方法可视为RLHF在忠实度维度的延伸

## 外部资源
- Phi-CCT metric: 参考原始论文了解度量细节
