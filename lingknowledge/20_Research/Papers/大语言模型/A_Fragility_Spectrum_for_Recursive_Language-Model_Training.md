---
date: "2026-09-13"
paper_id: "arXiv:2609.11149"
title: "A Fragility Spectrum for Recursive Language-Model Training"
authors: "Yangze Liu, Zhongyi Han"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 模型崩溃
  - 递归训练
  - 合成数据
  - 多样性
quality_score: "7.0/10"
created: "2026-09-13"
updated: "2026-09-13"
status: analyzed
---

# A Fragility Spectrum for Recursive Language-Model Training

## 核心信息
- **论文ID**：arXiv:2609.11149
- **作者**：Yangze Liu, Zhongyi Han
- **机构**：山东大学
- **发布时间**：2026-09-10
- **类别**：cs.CL, cs.AI, cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2609.11149) | [PDF](https://arxiv.org/pdf/2609.11149)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Model-generated text is finding its way back into training corpora, and there is plenty of evidence that training on such data over and over collapses output diversity. Prior work has studied the phenomenon itself: which protocols and which data mixtures cause collapse. But different models behave very differently under the same process. We fix one recursive contamination protocol and let 13 publicly released checkpoints form an ecosystem that shares a common corpus for five generations. The unique 4-gram outcome after five generations ranges from 0.187 to 0.940 across checkpoints, a roughly five-fold spread: some models are barely touched, others degenerate into repetitive fragments. Changing the composition of the shared pool or mixing in human text keeps the Spearman correlation of the ordering at 0.91–0.97, and changing the random seed keeps it at 0.93–0.98. Whether a model collapses easily under recursive training is, then, a property of the checkpoint itself, and one that has gone largely unexamined. Parameter scale alone does not explain it, since a three-size ladder within one family is not monotonic in size, and none of the static indicators we tested predicts it either. What does work is cheap: let a model iterate on its own output for two or three generations, and its fragility in the larger ecosystem can be inferred from that alone. Collapse speed also responds to intervention. Tightening top-p, which cuts the low-probability tail at generation time, nearly stops collapse within three generations and stabilizes six checkpoints spanning the whole spectrum together, while data-side filtering slows collapse without stopping it.

### 中文翻译
模型生成的文本正回流到训练语料中，大量证据表明反复基于此类数据训练会崩溃输出多样性。先前工作研究的是现象本身：哪些协议、哪些数据配比会导致崩溃。但不同模型在相同过程下表现截然不同。我们固定一个递归污染协议，让 13 个公开发布的 checkpoint 组成一个共享语料的生态系统，迭代五代。五代的唯一 4-gram 结果在 checkpoint 间从 0.187 到 0.940 不等，约五倍的差异：有些模型几乎不受影响，另一些则退化为重复片段。改变共享池的组成或混入人类文本，排序的 Spearman 相关系数保持在 0.91–0.97；改变随机种子则保持在 0.93–0.98。一个模型是否容易在递归训练下崩溃，因此是 checkpoint 本身的属性，而这一点此前在很大程度上未被审视。仅参数规模无法解释它——同族的三档尺寸并非随规模单调变化，我们测试的所有静态指标也都无法预测它。真正有效的办法很廉价：让模型在自己的输出上迭代两三代，就足以推断它在更大生态系统中的脆弱性。崩溃速度也对干预有响应。收紧 top-p（在生成时截断低概率尾部）几乎能在三代内阻止崩溃，并使跨越整个谱系的六个 checkpoint 一起稳定下来；而数据侧过滤只能减缓崩溃，无法阻止它。

### 核心要点提炼
- **研究背景**：合成文本回流训练语料已成常态，递归训练导致输出多样性逐代崩溃已是共识
- **研究动机**：先前工作把"起始模型"当作可互换的背景变量，假设单一 checkpoint 上的崩溃结论可外推，但事实并非如此
- **核心方法**：固定协议下让 13 个 1–4B checkpoint 组成共享语料生态系统迭代五代，测量崩溃谱系并检验其稳定性与可预测性
- **主要结果**：崩溃脆弱性是 checkpoint 的稳定独立属性（五倍差异、排序稳健），可用 2–3 代自循环廉价推断，且可通过 top-p 截断有效干预
- **研究意义**：警示"单一 checkpoint 的崩溃结论不可默认外推"，呼吁崩溃实验披露 checkpoint 身份

## 研究背景与动机

### 领域现状
模型生成文本正成为训练数据的一部分：2022–2025 年间公开网页中 AI 生成/辅助内容占比到 2025 年中已达约 35%。新模型已无法完全避免在旧模型输出上训练。递归训练的后果已确立——分布尾部最先消失，多样性逐代收窄（Shumailov 等、Alemohammad 等）。

### 现有方法的局限性
1. **研究轴单一**：既有工作主要回答"哪些协议/配比会崩溃"，把起始模型当作可互换的背景变量
2. **结论被默认外推**：在单一模型上测得的崩溃结果被假设可迁移到其他模型，缺乏实证检验
3. **缺失去重与披露规范**：崩溃实验通常不披露 checkpoint 身份，也不做第二 checkpoint 的对照

### 研究动机
作者直接检验一个被忽略的问题：**不同 checkpoint 进入递归训练时，是否以相同速度崩溃？** 通过固定协议、只改变起始 checkpoint 的受控实验，作者揭示崩溃脆弱性是一个此前被当作背景变量的稳定属性。

## 研究问题

**核心研究问题**：在固定递归污染协议下，不同 checkpoint 的崩溃速度是否存在系统性差异？这种差异是否稳定、可预测、可干预？

## 方法概述

### 方法架构
1. **生态系统链（Ecosystem chain）**：13 个公开发布的 1–4B 基础 checkpoint（10 个模型家族）组成共享语料生态系统。每代中每个模型独立生成文本，按预设比例混入共享池（主生态系统中 M07/phi-2 贡献 28%，其余十二个各 6%）；每个模型在共享池上做全参数微调，但**从自身干净预训练权重出发**，不跨代继承权重，从而隔离"语料递归反馈"而非"持续自训练"
2. **自循环链（Self-loop chain）**：模型仅在自己输出上训练下一代，用于检验"小规模单模型递归链能否提前预判生态系统的脆弱性排序"
3. **崩溃度量**：主指标为唯一 4-gram 占比（u4，越低越集中），辅以冻结编码器距离、困惑度、词频尾部统计

### 关键创新
1. **研究轴转向**：从"什么协议导致崩溃"转向"哪些 checkpoint 更脆弱"，提出崩溃脆弱性是独立于规模与静态指标的 checkpoint 属性
2. **受控生态设计**：共享池 + 不跨代继承权重，干净地隔离了语料递归反馈这一变量
3. **廉价筛选工具**：证明 2–3 代自循环即可推断生态脆弱性排序，无需真实生态实验

![[fig1_fragility_spectrum_draft_page1.png|600]]

## 实验结果

- **稳定谱系**：13 个 checkpoint 五代后的 u4 从 0.187 到 0.940，约五倍差异
- **排序稳健性**：换主导模型/改混合比例/加人类文本，Spearman 0.91–0.97；换随机种子 0.93–0.98
- **规模失效**：同族三档尺寸（0.6–4B）崩溃速度非单调，中间尺寸最稳健
- **静态指标失效**：校准、初始输出统计、单次微调信号等预递归指标均无法预测脆弱性
- **可干预**：收紧 top-p 几乎在三代内阻止崩溃，并稳定跨越整个谱系的六个 checkpoint；数据侧过滤（去重、困惑度门控）只减速不阻止

## 深度分析

### 研究价值
1. **方法论警示**：核心贡献是"单一 checkpoint 的崩溃结论不可默认外推"，对合成数据与模型崩溃研究的方法论规范有直接意义
2. **低成本筛选工具**：自循环 2–3 代的脆弱性推断，为实践者提供了一个廉价、可操作的模型选择信号
3. **干预机制定位**：把干预点定位到"生成时的低概率尾部"（top-p 截断），比数据侧过滤更有效，具有清晰的因果指向

### 局限性
1. **规模受限**：仅覆盖 1–4B 基础模型，未涉及更大规模或指令微调模型
2. **单一协议**：结论基于固定的递归协议，脆弱性谱系是否在更激进/更温和协议下保持仍有待验证
3. **机制未解**：揭示了"脆弱性是 checkpoint 属性"，但未解释其内在机制（架构/训练历史的哪个成分决定脆弱性）

### 未来工作
- 扩展到更大规模与指令微调模型，检验谱系的普适性
- 溯源脆弱性的内在机制（架构、训练历史、tokenizer 等）
- 建立崩溃实验的披露规范与跨 checkpoint 对照标准

## 相关论文对比

| 研究方向 | 代表工作 | 与本文的关系 |
|---------|---------|-------------|
| 模型崩溃现象 | Shumailov 等、Alemohammad 等 | 建立"递归训练致多样性崩溃"的共识基础 |
| 协议/配比研究 | Gerstgrasser 等、Dohmatob 等 | 回答"何时崩溃"，本文研究"谁更易崩溃" |
| 多模型生态 | Wang 等、Vu 等 | 研究对象是生态系统收敛点，本文借设定研究 checkpoint 差异 |
| 崩溃预警与缓解 | Du 等、Drayson 等 | 本文揭示静态预警指标失效，并对比采样侧与数据侧干预 |

本文的独特贡献在于：把"起始 checkpoint"从背景变量提升为研究对象，揭示并刻画了崩溃脆弱性的稳定谱系。
