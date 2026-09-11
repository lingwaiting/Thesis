---
date: "2026-09-11"
paper_id: "arXiv:2609.10142"
title: "Active Adaptation, Not Static Defense: Temporal Dynamics of Preventative Steering in Adversarial Fine-Tuning"
authors: "Jing Guan, Yachao Yang, Zhaoliang Liu, Yuyao Zhang, Fanyu Meng, Junlan Feng"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Safety
  - Alignment
  - Adversarial-Fine-Tuning
  - Model-Steering
quality_score: "7.5/10"
related_papers: []
created: "2026-09-11"
updated: "2026-09-11"
status: analyzed
---

# Active Adaptation, Not Static Defense: Temporal Dynamics of Preventative Steering in Adversarial Fine-Tuning

## 核心信息
- **论文ID**：arXiv:2609.10142
- **作者**：Jing Guan, Yachao Yang, Zhaoliang Liu, Yuyao Zhang, Fanyu Meng, Junlan Feng
- **机构**：--
- **发布时间**：2026-09-09
- **会议/期刊**：--
- **链接**：[arXiv](https://arxiv.org/abs/2609.10142) | [PDF](https://arxiv.org/pdf/2609.10142)
- **代码**：--
- **分类**：cs.CL, cs.AI, cs.LG

## 摘要翻译

### 英文摘要
Large language models remain fragile against malicious fine-tuning, motivating training-time defenses against harmful persona drift. Preventative Steering injects undesirable-trait persona vectors during fine-tuning and removes them at evaluation time, yet the mechanism behind its lasting protection remains unclear. Analyzing its temporal optimization dynamics, we find that the defense emerges from an early compensatory adaptation phase followed by a steady-state phase where the corrective signal decays; in parameter space, attention output projections emerge as the dominant residual-write route for defensive updates. Through Intervention Delta Preservation (IDP) and IDP Continuation experiments, we further show that preserving or reinjecting the weight offset fails to maintain protection, indicating that preventative steering relies on active adaptation rather than a static defense. Motivated by this finding, we propose Progressive Intensity Scheduling (PIS), which starts with a moderate injection strength and increases it after static-strength alignment begins to decay. Across the evaluated Qwen2.5 and Gemma-3 models, PIS improves safety robustness over static-strength steering while reducing harmful trait expression.

### 中文翻译
大语言模型在面对恶意微调时仍然脆弱，这催生了针对「有害人格漂移」的训练期防御方法。预防性引导（Preventative Steering）在微调阶段注入「不良特质人格向量」，并在评估时移除它们，但其持久保护背后的机制一直不清楚。作者通过分析其时间优化动态发现：防御由一个早期的「补偿适应阶段」与随后的「稳态阶段」构成（后者的纠正信号会衰减）；在参数空间中，**注意力输出投影（attention output projections）**成为防御更新的主导残差写入路径。通过干预增量保持（IDP）与 IDP 延续实验，作者进一步表明，保留或重新注入权重偏移都无法维持保护，说明预防性引导依赖的是**主动适应**而非静态防御。基于此，作者提出**渐进强度调度（PIS）**：以中等注入强度起步，并在静态强度对齐开始衰减后逐步加大强度。在 Qwen2.5 与 Gemma-3 上的评估表明，PIS 相比静态强度引导提升了安全鲁棒性，同时降低了有害特质表达。

### 核心要点提炼
- **研究背景**：LLM 对恶意微调脆弱，训练期防御（如 Preventative Steering）日益重要。
- **研究动机**：Preventative Steering 的「持久保护」机制不明确——是静态防御还是动态过程？
- **核心方法**：分析时间优化动态 + IDP/IDP Continuation 实验，提出 PIS 渐进强度调度。
- **主要结果**：防御来自早期补偿适应 + 稳态衰减；注意力输出投影是主导写入路径；PIS 提升安全鲁棒性。

## 研究背景与动机

### 领域现状
LLM 的安全对齐在部署后仍可能被「恶意微调」（adversarial fine-tuning）轻易破坏——只需少量有害数据微调，模型就可能产生「有害人格漂移」（harmful persona drift）。这推动了一系列训练期防御方法，其中 Preventative Steering 是一类代表性思路：在微调时主动注入「不良特质人格向量」，评估时再移除，从而「对冲」攻击者意图。

### 现有方法的局限性
尽管 Preventative Steering 被证明能带来持久保护，但它的内在机制长期处于「知其然不知其所以然」的状态：保护究竟源于一个静态的权重偏移，还是一个动态的优化过程？这直接影响防御的可解释性、可靠性与可改进空间。

### 研究动机
作者希望打开 Preventative Steering 的「黑箱」，从时间优化动态的视角回答：防御是如何在微调过程中涌现、稳定与衰减的，并据此设计更优的注入策略。

## 研究问题

### 核心研究问题
1. Preventative Steering 的持久保护源于「静态防御」还是「主动适应」？
2. 防御更新在参数空间中经由哪些关键路径（哪些层/模块）完成？
3. 能否基于对动态机制的理解，设计出超越静态强度引导的更优策略？

## 方法概述

### 核心思想
把 Preventative Steering 视作一个随时间演化的**动态过程**而非一次性静态操作，通过剖析其优化轨迹与参数写入路径，揭示防御的本质，并据此提出「渐进强度调度」。

### 方法框架

#### 整体架构
![[v7.drawio.png|800]]

> 图1：Preventative Steering 的时间动态与防御机制示意图——防御由早期的补偿适应阶段与随后的稳态阶段构成，注意力输出投影是主导的防御更新写入路径。

#### 各模块详细说明

**模块1：时间优化动态分析**
- **功能**：追踪微调过程中注入向量与纠正信号的演化。
- **发现**：早期出现「补偿适应阶段」，随后进入「稳态阶段」，纠正信号随时间衰减。

**模块2：参数空间写入路径分析**
- **功能**：定位防御更新主要落在哪些参数上。
- **发现**：注意力输出投影（attention output projections）是主导的残差写入（residual-write）路径。

**模块3：IDP / IDP Continuation 实验**
- **功能**：通过「保留/重新注入权重偏移」来检验防御是否可静态复现。
- **发现**：静态保留或重注入均无法维持保护 → 证明是主动适应而非静态防御。

**模块4：Progressive Intensity Scheduling（PIS）**
- **功能**：以中等注入强度起步，在静态强度对齐开始衰减后加大注入强度。
- **目标**：在 Qwen2.5、Gemma-3 上提升安全鲁棒性、降低有害特质表达。

### 关键创新
1. **首次从时间优化动态视角**揭示了 Preventative Steering 的「主动适应」本质。
2. **定位关键写入路径**——注意力输出投影，为可解释防御提供抓手。
3. **提出 PIS 渐进强度调度**，用更聪明的强度策略超越静态引导。

## 实验结果

### 实验设置
- **模型**：Qwen2.5、Gemma-3
- **防御基线**：静态强度 Preventative Steering
- **评估维度**：安全鲁棒性、有害特质表达

### 主要结果
1. 防御由「早期补偿适应 + 稳态衰减」两阶段构成，纠正信号在稳态阶段衰减。
2. IDP 实验表明：保留或重注入权重偏移**无法**维持保护。
3. 注意力输出投影是防御更新的主导残差写入路径。
4. PIS 相比静态强度引导，在安全鲁棒性上更优，同时降低有害特质表达。

![[combined_alpha_cos.png|800]]

> 图2：不同强度调度下的安全鲁棒性对比——PIS（渐进强度）相对静态强度引导更稳定地维持防护。

## 深度分析

### 研究价值
- **理论贡献**：将「安全引导」从静态视角升级为动态过程视角，澄清了 Preventative Steering 的生效机制。
- **实际应用**：PIS 可直接用于提升 LLM 安全微调防御的鲁棒性。
- **领域影响**：为训练期安全防御的「机制理解 + 策略优化」提供了一个可复用的分析范式。

### 优势
- 问题切入精准，直击「持久保护机制不明」这一真实痛点
- 实验设计（IDP 延续）有力地排除了「静态防御」假说
- 从机制发现自然导向策略改进（PIS），逻辑闭环完整

### 局限性
- 仅在 Qwen2.5、Gemma-3 两个模型上验证，跨模型/跨规模的普适性待进一步检验
- 对「注意力输出投影为何是主导写入路径」的深层原因解释有限
- 未在更强攻击（如多轮恶意微调、混合攻击）下评估 PIS 的鲁棒性

### 适用场景
- LLM 安全对齐与恶意微调防御
- 训练期防御机制的可解释性研究

## 我的综合评价

### 价值评分
- **总体评分**：**7.5/10** — 机制分析扎实、策略改进自然的 LLM 安全防御研究

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 「主动适应 vs 静态防御」的视角有启发性，PIS 是渐进改进 |
| 技术质量 | 8/10 | IDP 延续实验设计严谨，有效排除竞争假说 |
| 实验充分性 | 7/10 | 两模型验证，但攻击场景覆盖有限 |
| 写作质量 | 8/10 | 论证清晰，逻辑层层递进 |
| 实用性 | 8/10 | 直接服务 LLM 安全微调防御 |

### 突出亮点
- 用「主动适应 vs 静态防御」这对概念，把模糊的防御机制问题转化为可检验的假说
- IDP Continuation 实验是一处漂亮的反证设计，令人信服地否定了静态防御解释

## 相关论文
- 待补充：与其他恶意微调防御（如 Representation Rerouting、Task Vector 去除等）的对比

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.10142)
- [PDF](https://arxiv.org/pdf/2609.10142)
