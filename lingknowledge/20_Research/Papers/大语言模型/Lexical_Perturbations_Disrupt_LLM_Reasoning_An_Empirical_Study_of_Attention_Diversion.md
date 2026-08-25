---
date: "2026-08-25"
paper_id: "arXiv:2608.22140"
title: "Lexical Perturbations Disrupt LLM Reasoning: An Empirical Study of Attention Diversion"
authors: "Jiaqian Zhu, Yang Zhang, Junhua Ding, Xiaowei Yu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 鲁棒性
  - 注意力机制
  - 推理
  - 对抗扰动
quality_score: "7.8/10"
related_papers: []
created: "2026-08-25"
updated: "2026-08-25"
status: analyzed
---

# Lexical Perturbations Disrupt LLM Reasoning: An Empirical Study of Attention Diversion

## 核心信息
- **论文ID**：arXiv:2608.22140
- **作者**：Jiaqian Zhu, Yang Zhang, Junhua Ding, Xiaowei Yu
- **机构**：Missouri University of Science and Technology (Rolla, MO)；University of North Texas (Denton, TX)
- **发布时间**：2026-08-22
- **会议/期刊**：arXiv（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2608.22140) | [PDF](https://arxiv.org/pdf/2608.22140)
- **代码**：https://github.com/Jiaqian-Janelle/Attention-Diversion

## 摘要翻译

### 英文摘要
Large Language Models (LLMs) achieve strong reasoning performance, but their robustness to realistic lexical corruption remains poorly understood. We evaluate four open-weight instruction-tuned models and frontier models across four reasoning benchmarks under keyboard noise, character swaps, and filler insertion. Character-level perturbations substantially degrade accuracy, especially on multi-step reasoning tasks, while filler insertion has little effect. We trace this asymmetry to Attention Diversion: lexical corruption fragments subword tokenization, and the resulting fragments attract disproportionate attention mass, concentrated in middle and final transformer layers.

### 中文翻译
大语言模型（LLMs）具备强大的推理能力，但其对现实世界中"词法损坏"（lexical corruption）的鲁棒性仍未被充分理解。作者在四个开源指令微调模型和前沿模型上、跨越四个推理基准，评估了键盘噪声、字符交换和填充插入三种扰动。字符级扰动会显著降低准确率（尤其多步推理任务），而填充插入几乎无影响。作者将这种不对称性归因于"注意力转移"（Attention Diversion）：词法损坏会打碎子词 token 化，产生的碎片吸引了不成比例的注意力权重，集中在中层和末层 transformer 层。

### 核心要点提炼
- **研究背景**：LLM 推理能力强，但对输入中微小的字符级噪声是否稳健尚不清楚
- **研究动机**：现实部署中拼写错误、键盘噪声、OCR 误差普遍存在，需理解其对推理的影响机制
- **核心方法**：通过因子化干预（factorial intervention）解耦"token 内容"与"注意力分配"两个通道
- **主要结果**：字符扰动显著降推理性能，根源是 token 碎片化导致的注意力转移，且内容与注意力耦合，单一修复无效
- **研究意义**：解释了为何 CoT、拼写检查、自我修复等推理时策略都难以稳定恢复性能

## 研究背景与动机

### 领域现状
LLM 在多步推理、数学、代码等任务上表现强劲，但鲁棒性研究多集中于语义层面的对抗攻击（如改写、同义替换），对**现实常见的字符级损坏**（拼写错误、键盘邻近键误触、OCR 噪声）关注不足。

### 现有方法的局限性
- 已有鲁棒性研究多针对离散 token 或语义扰动，未系统区分"字符级碎片化"与"长度变化"的影响
- 推理时修复策略（CoT 引导、拼写检查、self-repair、更强的修复模型）在实践中效果不稳定，缺乏统一解释

### 研究动机
理解"为什么字符级扰动破坏推理、而填充插入无害"这一不对称现象，并定位其内在机制，从而为鲁棒性设计提供依据。

## 研究问题

### 核心研究问题
1. 字符级词法扰动如何影响 LLM 的推理性能？
2. 这种性能下降的根本机制是什么？
3. 为什么现有推理时修复策略难以恢复性能？

## 方法概述

### 核心思想
作者提出"注意力转移"（Attention Diversion）机制：字符损坏导致子词 tokenizer 产生大量异常碎片，这些碎片在中层和末层 transformer 中吸引过量注意力，从而挤压了对语义关键 token 的注意力，破坏推理链。

### 方法框架

#### 整体架构
研究方法分为三步：**扰动构造 → 行为测量 → 机制定位（因子化干预）**。

![[emnlp_method_page1.png|800]]

> 图1：方法框架总览。左：三类扰动（键盘噪声、字符交换、填充插入）的构造；中：四个推理基准上的行为测量；右：基于注意力分析与因子化干预的机制定位。

#### 各模块详细说明

**模块1：扰动构造**
- **键盘噪声（keyboard noise）**：将字符替换为键盘邻近键
- **字符交换（character swaps）**：相邻字符交换位置
- **填充插入（filler insertion）**：插入无关字符作为长度匹配控制
- **长度匹配控制（length-matched controls）**：区分"碎片化"与"prompt 长度"的影响

**模块2：注意力分析**
- 追踪各层各 token 的注意力权重分布
- 发现碎片 token 在中层和末层吸引不成比例的注意力质量

**模块3：因子化干预（factorial intervention）**
- 将"token 内容"与"注意力分配"作为两个独立因子
- 分别/同时恢复两个通道，观察性能恢复幅度
- 关键发现：单独恢复注意力（内容仍损坏）反而**有害**；单独恢复内容**不足**；只有两者同时恢复才能恢复大部分性能

### 方法架构图

![[fig_exp2_causal_intervention_page1.png|800]]

> 图2：因果干预实验。揭示"内容"与"注意力"两个通道的耦合关系——这是推理时修复策略失败的根源。

## 实验结果

### 实验设置
- **模型**：四个开源指令微调模型 + 前沿模型
- **基准**：四个推理基准（含多步推理任务）
- **扰动类型**：键盘噪声、字符交换、填充插入

### 主要结果

| 扰动类型 | 准确率影响 | 关键发现 |
|----------|-----------|----------|
| 键盘噪声 / 字符交换 | 显著下降 | 多步推理任务受损最严重 |
| 填充插入 | 几乎无影响 | 证明是"碎片化"而非"长度"导致 |

### 结果分析
- **不对称性**：字符级扰动（碎片化 token）→ 显著降性能；填充插入（长度变化但 token 完整）→ 无影响，说明机制在 token 碎片化而非 prompt 长度
- **注意力转移位置**：集中在 transformer 中层和末层
- **耦合性**：内容损坏与注意力分配耦合，这是修复困难的根本原因

### 实验结果图

![[fig_frontier_comparison_page1.png|800]]

> 图3：前沿模型对比。字符级扰动在各类前沿模型上均导致推理性能显著下降。

![[fig_attention_hijack_ratio_4models_page1.png|800]]

> 图4：注意力劫持比例。碎片 token 在多个模型的中层/末层均吸引过量注意力。

![[fig_tokenization_disruption_combined_page1.png|800]]

> 图5：token 化破坏分析。字符扰动导致子词 tokenizer 产生异常碎片。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：提出"注意力转移"机制**——首次系统地将字符级扰动导致的推理下降归因于 token 碎片化引发的注意力重分配，而非简单的语义丢失
- **贡献2：揭示"内容-注意力"耦合**——通过因子化干预证明两通道耦合，解释了为何单一修复策略无效
- **贡献3：统一解释推理时策略失效**——CoT、拼写检查、self-repair 各自只处理一个通道，故无法稳定恢复

#### 实际应用价值
- **应用场景1：LLM 推理鲁棒性评估**——为评估模型在现实噪声（拼写错误、OCR、键盘误触）下的可靠性提供方法论
- **应用场景2：鲁棒性训练设计**——提示需要同时修复 token 内容和注意力分配，而非单一通道
- **潜在影响**：对工业界部署 LLM 时的输入预处理策略有指导意义

### 方法优势详解
- **因子化干预设计严谨**：清晰分离内容与注意力两个因子，因果推断扎实
- **长度匹配控制**：排除了 prompt 长度的混淆因素，结论可信

### 局限性分析
- **局限1：未提出修复方法**——论文聚焦诊断，未给出有效的鲁棒性增强方案
- **局限2：扰动类型有限**——仅覆盖三类字符扰动，未扩展到语义/结构层面
- **局限3：开源模型为主**——前沿模型的部分结论依赖 API 访问，可复现性受限

## 我的综合评价

### 总体评分
**7.8/10** - 机制诊断清晰、实验严谨，对 LLM 鲁棒性研究有启发价值，但缺少修复方案使其偏分析型

### 分项评分
| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | "注意力转移"机制解释较新颖，但属经验观察 |
| 技术质量 | 8/10 | 因子化干预+长度匹配控制，方法严谨 |
| 实验充分性 | 8/10 | 多模型×多基准×多扰动，覆盖充分 |
| 写作质量 | 8/10 | 逻辑清晰，动机明确 |
| 实用性 | 7/10 | 诊断价值高，但缺修复方案 |

> [!tip] 关键启示
> LLM 对字符级噪声脆弱，根源是 token 碎片化引发"注意力转移"，且内容与注意力耦合——这解释了为何现有推理时修复策略都难以稳定奏效。

> [!warning] 注意事项
> - 论文未提出鲁棒性修复方案，只做诊断
> - 结论主要基于开源模型，前沿模型结论需谨慎外推
> - "注意力转移"是经验性机制解释，尚缺严格理论证明

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读。对关注 LLM 鲁棒性、推理可靠性、注意力机制可解释性的研究者很有启发。
