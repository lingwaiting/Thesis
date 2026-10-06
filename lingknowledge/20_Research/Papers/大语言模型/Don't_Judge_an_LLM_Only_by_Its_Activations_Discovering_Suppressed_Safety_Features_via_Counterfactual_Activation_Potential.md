---
date: "2026-10-06"
paper_id: "arXiv:2610.05541"
title: "Don't Judge an LLM Only by Its Activations: Discovering Suppressed Safety Features via Counterfactual Activation Potential"
authors: "Swadesh Swain, Sanghamitra Dutta"
domain: "大语言模型"
tags:
  - 论文笔记
  - Mechanistic-Interpretability
  - AI-Safety
  - Jailbreak
  - Sparse-Autoencoder
  - Alignment
quality_score: "8.5/10"
created: "2026-10-06"
updated: "2026-10-06"
status: analyzed
---

# Don't Judge an LLM Only by Its Activations: Discovering Suppressed Safety Features via Counterfactual Activation Potential

## 核心信息
- **论文ID**：arXiv:2610.05541
- **作者**：Swadesh Swain, Sanghamitra Dutta
- **机构**：--
- **发布时间**：2026-10-04
- **会议/期刊**：arXiv（cs.LG / cs.AI / cs.CL / cs.CR）
- **链接**：[arXiv](http://arxiv.org/abs/2610.05541) | [PDF](https://arxiv.org/pdf/2610.05541)
- **引用**：--

## 摘要翻译

### 英文摘要
Mechanistic interpretability has emerged as the primary means to understand safety behavior of LLMs. However, existing tools primarily focus on the activating neurons or features of a model. The role of the remaining large set of inactive components is invisible to such methods. This work demonstrates that the inactive set contains safety-critical features that are causally relevant for refusal of harmful prompts. Suppressing such features could turn refusals into compliance, while passing undetected by prevalent interpretability tools. We introduce the Counterfactual Activation Potential (CAP), a metric that quantifies a suppressed feature's latent activation tendency as the product of its encoder alignment (how strongly the input drives it), suppression strength (how strongly active features inhibit it), and safety criticality (how much refusal depends on it). To find suppressed safety features at scale, we propose CAP-guided Safety Feature Discovery (CSFD), a two-stage filtering algorithm that identifies candidate safety features from hundreds of thousands of transcoder features without exhaustive ablation. A significant fraction of trials turn compliant with harmful prompts when a candidate feature is ablated. Under natural jailbreaks, the suppression acting on the highest-CAP features rises 2-4x, and their activation correspondingly falls by up to 80%. Amplifying a feature's suppressors pushes its activation down and raises harmful compliance with prompts related to the suppressed feature, with no such effect for random features. Our experiments span five Gemma, Qwen, and Llama models across various parameter sizes. Our findings indicate that jailbreaks could operate in part by suppressing safety-critical features rather than solely activating harmful ones, and that suppressed features are a necessary complement to activation-focused interpretability of safety behavior.

### 中文翻译
机制可解释性已成为理解大语言模型（LLM）安全行为的主要手段，但现有工具主要聚焦于模型中**被激活**的神经元或特征，而**大量处于"非激活"状态的组件的作用对这些方法而言是不可见的**。本文证明：这一非激活集合中包含着对拒绝有害提示具有因果作用的安全关键特征；抑制这些特征即可把"拒绝"转变为"服从"，且能避开主流可解释性工具的检测。作者提出**反事实激活势（CAP）**——一个量化"被抑制特征"潜在激活倾向的指标，它由三个因子相乘构成：编码器对齐度（输入对它的驱动强度）、抑制强度（激活特征对它的抑制强度）与安全关键度（拒绝行为对它的依赖程度）。为规模化定位被抑制的安全特征，作者提出 **CAP 引导的安全特征发现（CSFD）**：一个两阶段过滤算法，无需穷举消融即可从数十万个 transcoder 特征中筛出候选安全特征。结果显示：消融候选特征时，相当比例（"significant fraction"）的测试会从拒绝转为对有害提示服从；在自然越狱下，作用于最高 CAP 特征的抑制上升 2–4 倍，其激活相应下降最多 80%；放大某个特征的抑制子会推低其激活并提高与相关有害提示的服从率，而对随机特征无此效应。实验覆盖 5 个 Gemma、Qwen、Llama 系列、不同参数规模的模型。作者指出：越狱可能部分是**通过抑制安全关键特征**而非单纯激活有害特征来运作的；被抑制特征是对"以激活为中心"的安全可解释性的必要补充。

### 核心要点提炼
- **研究背景**：机制可解释性偏重"激活"特征，忽视了大量"非激活/被抑制"组件。
- **研究动机**：安全关键特征可能以"被抑制"状态存在，现有工具检测不到。
- **核心方法**：提出 CAP 指标（编码器对齐 × 抑制强度 × 安全关键度）与 CSFD 两阶段发现算法。
- **主要结果**：消融被抑制安全特征可使拒绝转为服从；越狱会显著强化抑制、削弱激活。
- **研究意义**：揭示"抑制型"安全机制与越狱的新机制解释，补充激活中心范式。

## 研究背景与动机

### 领域现状
稀疏自编码器（SAE）/ transcoder 等工具让研究者能定位 LLM 中被激活的可解释特征，用于理解拒绝行为、分析越狱等。但这些工具天然偏向"被激活"的部分，隐含假设"重要特征必然活跃"。

### 现有方法的局限性
- 激活中心视角忽略了"非激活"特征可能承载的关键因果作用。
- 越狱研究多聚焦"激活有害特征"，可能遗漏"抑制安全特征"这一互补机制。

### 研究动机
需要一种能发现"被抑制但安全关键"特征的方法，补齐激活中心可解释性的盲区，进而更完整地理解 LLM 的安全与越狱行为。

## 研究问题

### 核心研究问题
LLM 的非激活特征集合中是否存在安全关键特征？如何量化并规模化发现它们？抑制这些特征是否因果性地导致拒绝→服从的转变？

## 方法概述

### 核心思想
用"反事实"思路看待被抑制特征：即使某特征当前未被激活，也可以量化它"本应被激活"的潜在倾向。CAP 将该倾向分解为**输入驱动（编码器对齐）**、**被抑制程度（抑制强度）**与**安全相关性（关键度）**三者的乘积；CSFD 则据此在数十万特征中高效筛选候选，无需穷举消融。

### 方法框架

#### 整体架构
① 用 transcoder 提取特征；② 计算每个特征的 CAP（三因子乘积）；③ CSFD 两阶段过滤，筛出高 CAP 候选安全特征；④ 消融/放大抑制子验证因果性；⑤ 在自然越狱下观测抑制的动态变化。

![[figure1_compact_page1.png|600]]

> 图1：CAP 与 CSFD 方法总览（来源：pdf-figure）。

#### 关键设计
- **CAP 三因子分解**：
  $$\text{CAP}(f) = \text{encoder alignment} \times \text{suppression strength} \times \text{safety criticality}$$
- **CSFD 两阶段过滤**：粗筛（依据 CAP 排序）+ 精筛（候选验证），避免对数十万特征穷举消融。
- **因果验证**：消融特征看"拒绝→服从"转变，放大抑制子看激活下降与服从率上升。

## 实验结果

### 实验目标
验证被抑制安全特征的存在性、CAP/CSFD 的发现能力，以及抑制机制与越狱的因果关联。

### 主要结果
- **存在性与因果性**：消融候选特征时，相当比例测试从拒绝转为服从。
- **越狱机制**：自然越狱下，高 CAP 特征的抑制上升 2–4 倍，激活下降最多 80%。
- **操纵效应**：放大抑制子可推低激活、提高有害服从率；对随机特征无此效应。
- **跨模型稳健**：覆盖 5 个 Gemma/Qwen/Llama 模型。

### 实验结果图

![[cap_corr.png|600]]

> 图2：CAP 与安全关键度的相关性分析（来源：arxiv-source）。

![[figure_methods_cap_pairs_page1.png|600]]

> 图3：CAP 配对与抑制/激活动态（来源：pdf-figure）。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出"被抑制特征"这一被忽视的安全机制维度，补充激活中心范式的盲区。
- CAP 指标为"潜在激活倾向"提供可计算的形式化刻画。
- 为越狱提供新机制解释：越狱可部分通过"抑制安全特征"运作。

#### 实际应用价值
- 为红队与安全审计提供新的检测维度（被抑制特征可作为后门/越狱的隐蔽信号）。
- CSFD 的高效筛选可扩展到大规模模型的安全特征发现。

### 局限性分析
- CAP 三因子的乘积形式与权重分配需进一步理论论证。
- 实验以消融/放大为核心，缺少端到端防御方案的验证。
- "significant fraction"的量化口径需更精确的报告。

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** - 视角新颖、机制发现重要，对安全可解释性与越狱研究有明确推进。

#### 分项评分
| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | "被抑制特征"视角 + CAP 指标，新颖且有洞察 |
| 技术质量 | 8/10 | CAP 分解合理，CSFD 高效，因果验证规范 |
| 实验充分性 | 8/10 | 5 模型跨规模，消融/放大/越狱多维度 |
| 写作质量 | 8/10 | 逻辑清晰，图示充分 |
| 实用性 | 8/10 | 对安全审计、红队、越狱研究有直接价值 |

> [!tip] 关键启示
> 安全机制并非只存在于"被激活"的特征里——"被抑制"的特征同样关键，越狱可能正是通过抑制它们来绕过安全防线。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读——安全可解释性与越狱机制研究的重要补充。
