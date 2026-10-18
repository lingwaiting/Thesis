---
date: "2026-10-15"
paper_id: "arXiv:2610.22292"
title: "CAT-alyzing Honesty: Continuous Adversarial Training for Transferable LLM Honesty"
authors: "Yuan-Chen Chang, Niklas Herbster, Gauthier Gidel, Tommaso Tosato"
domain: "大语言模型"
tags:
  - 论文笔记
  - honesty
  - alignment
  - adversarial-training
  - LLM
quality_score: "8.5/10"
created: "2026-10-18"
updated: "2026-10-18"
status: analyzed
---

# CAT-alyzing Honesty: Continuous Adversarial Training for Transferable LLM Honesty

## 核心信息
- **论文ID**：arXiv:2610.22292
- **作者**：Yuan-Chen Chang, Niklas Herbster, Gauthier Gidel, Tommaso Tosato
- **机构**：Tara Research、Mila -- Quebec AI Institute、慕尼黑工业大学（TUM）
- **发布时间**：2026-10-15
- **会议/期刊**：ICLR 2027 投稿（Preprint）
- **链接**：[arXiv](http://arxiv.org/abs/2610.22292v1) | [PDF](https://arxiv.org/pdf/2610.22292v1)

## 摘要翻译

### 中文翻译
大语言模型正越来越多地出现在"诚实"至关重要的场景中。然而，尽管经过了对齐训练，这些模型仍然会表现出不诚实的行为，例如输出与某些知识相矛盾、或在上下文中缺乏依据的声明。本文提出 **CATCH**（Continuous Adversarial Training for Consistent Honesty，一致性诚实的连续对抗训练），其灵感源于一种最初为稳健拒绝有害查询而开发的方法。CATCH 将模型推向诚实答案、同时远离不诚实答案，并保持模型能力。作者评估了两种架构（Llama-3.3-70B-Instruct 和 Qwen3-3.6-27B），证明仅用数百个由"自报告场景"构建的目标训练，CATCH 就能学到一种稳健泛化、跨基准迁移的诚实行为：在 MASK 上持续降低压力下的不诚实，在 Among Us 多智能体游戏中提高认罪率，在 AuditBench 上提高模型隐藏行为的发现率。CATCH 在提升诚实方面优于 prompting 和 SFT，与 SOTA 诚实激活转向（activation steering）持平；但得益于一种新颖的效用数据选择，CATCH 在保持效用方面超越了诚实转向。

## 研究背景与动机

### 领域现状
LLM 的诚实（honesty）对齐已成为对齐研究的核心议题。与安全对齐（拒绝有害输出）不同，诚实对齐更难：安全对齐中一个通用的"Sorry, I can't do that"拒绝即可作为所有有害 prompt 的 toward-target，但诚实要求 truth 和 lie 都依赖具体上下文，因此两个目标都必须逐例构建。

### 现有方法的局限
- **Prompting / SFT**：Wang et al. (2025) 认为 prompting 和 SFT 是最有效的诚实缓解手段，但本文发现它们在跨基准（如 Among Us）上泛化差。
- **Activation steering（如 StTP）**：诚实最有效的转向方法，但在其有效强度下会严重损害通用能力（Llama 的 AlpacaEval 从 51% 掉到 8.6%）。
- **SFT 需要海量数据**：Wang et al. 用了约 50 倍的数据去 SFT 一个更大的闭源模型。

### 研究动机
能否在小数据（几百个目标）条件下，用对抗训练学到一种**可迁移、且不损害能力**的诚实行为？

## 方法概述

### 核心思想
把安全对齐中的**连续对抗训练（Continuous Adversarial Training）**迁移到诚实对齐：对每个 prompt 构造"诚实答案 $y^+$"与"不诚实答案 $y^-$"两个目标，在 embedding 空间中做对抗扰动，训练模型在扰动后的输入上提高 $y^+$、压低 $y^-$ 的概率，同时用一个**效用数据集**锚定模型能力。

### 方法框架

#### 整体架构
1. **设计对抗目标**：对每个含"说谎压力"的 prompt $x$，关联两个续写——$y^+$（与模型可用知识一致的诚实答案）和 $y^-$（屈从压力、断言相反内容的答案）。
2. **连续对抗训练**：把离散 token 上的最坏扰动搜索松弛到 embedding 空间，攻击者把每个 token embedding 移动不超过 $\epsilon$（$\|\delta_i\|_p \le \epsilon$），用 $K$ 步投影符号梯度上升逼近最坏扰动 $\delta^*$。
3. **训练目标**（两种）：
   - **CAT**：unlikelihood 目标——提高 $y^+$ 概率、降低 $y^-$ 概率，外加效用数据 $\mathcal{D}_u$ 的通用指令损失：
     $$\min_\theta -\mathbb{E}\Big[\lambda_t \log f_\theta(y^+|\tilde{x}) - \lambda_a \log f_\theta(y^-|\tilde{x})\Big] - \mathbb{E}_{\mathcal{D}_u}\Big[\lambda_u \log f_\theta(y|x)\Big]$$
   - **CAPO**：对抗式 IPO/DPO，用冻结的初始模型 $f_{\theta_0}$ 作参考，替代效用项，惩罚偏离原始分布。

![[fig1_overview_twoline_v25_page1.png|800]]

> 图1：CATCH 方法概览（overview）。展示从诚实/不诚实目标构造到连续对抗训练的完整流程。

## 实验结果

### 数据集与评估
三个"不诚实"的表现形式（manifestations）：
- **MASK**：指令驱动的不诚实（instructed dishonesty）
- **Among Us**：激励驱动的不诚实（incentive-driven），模型扮演 Impostor 玩多智能体游戏
- **AuditBench**：训练内化的隐藏行为（trained-in hidden behaviour）

### 主要结果

| 指标 | Llama-3.3-70B | Qwen3.6-27B |
|------|---------------|-------------|
| MASK 诚实率（Base→CATCH） | 34% → 86% | 47% → 91% |
| Among Us Impostor 胜率（Base→CATCH） | 39% → 20% | 48% → 12% |
| Among Us 认罪率（Base→CATCH） | 5% → 84% | 19% → 85% |
| AlpacaEval 胜率（CATCH，与 Base 持平） | 51.3% | 51.8% |

- **AuditBench**：SFT-adversarial 有机体上发现率 43%→79%，KTO-adversarial 上达到 93%。
- **能力保持**：CATCH 在 AlpacaEval 上与 Base 持平；而 StTP steering 把 Llama 降到 8.6%、Qwen 降到 45.2%。
- **效用数据消融**：on-policy 数据（Magpie）达到相似诚实度的同时把 AlpacaEval 维持在 51.3%，而 off-policy 数据（UltraChat）掉到 22.0%。用更强模型（Magpie-Qwen3.6）生成的 off-policy 数据甚至把 Llama 提到 85.3%（蒸馏效应）。

![[sel_catch_band_bold_novalues_ph4.0_page1.png|800]]

> 图2：CATCH 在 MASK 上提升诚实率，超越 prompting、SFT 与 SOTA steering 基线。

![[amongus_main_sbs_page1.png|800]]

> 图3：Among Us 游戏中，CATCH 让 Impostor 胜率下降、认罪率大幅上升。

![[fig8_pareto_2panel_alpha_nosft_page1.png|800]]

> 图4：效用数据消融的 Pareto 前沿——on-policy 数据在相同诚实度下保留更强能力。

## 深度分析

### 研究价值评估
- **理论贡献**：首次系统地把连续对抗训练从安全对齐迁移到诚实对齐，并阐明诚实对齐与安全对齐的关键差异（truth/lie 都依赖上下文）。
- **关键洞察**：**效用数据的选择**是诚实鲁棒训练中能力保持的核心变量——先前工作（Xhonneux et al.）把能力退化归因于"鲁棒性-效用权衡"，本文证明很大一部分成本来自 off-policy 的 UltraChat 数据选择。
- **实际价值**：CATCH 只需白盒访问 + 几百个目标 + LoRA，即可让开源模型获得可迁移的诚实行为。

### 方法优势
- **数据高效**：仅数百个自报告场景目标，远少于 SFT 所需的海量数据。
- **可迁移**：在 MASK 上训练的诚实行为迁移到 Among Us 与 AuditBench，而 prompting/SFT 基线不泛化。
- **能力保持**：通过 on-policy 效用数据，能力与 Base 持平，优于 steering。

### 局限性
- **需白盒访问**：无法应用于闭源 API。
- **LoRA 更新全层**：未验证"仅中间层更新"是否能进一步提升诚实（prior work 认为中间层承载大部分信号）。
- **诚实度量主观性**：MASK 等基准的"诚实"判定依赖 LLM-as-judge，存在噪声。

### 与相关论文对比
- **Xhonneux et al. (2024, "Efficient")**：安全对齐中的连续对抗训练（latent attack），本文把其框架迁移到诚实，并解决逐例目标构造问题。
- **Herbster et al. (2026, StTP)**：SOTA 诚实激活转向，本文与之在诚实度上持平、但在能力保持上显著超越。
- **Wang et al. (2025)**：主张 prompting/SFT 最有效，本文在小数据 regime 下给出相反结论（CATCH 更有效）。

## 我的综合评价

### 总体评分
**8.5/10** — 方法迁移清晰、洞察深刻（效用数据选择）、实验覆盖三种不诚实表现且能力保持做得漂亮；局限在于需白盒访问、诚实度量存在主观性。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 对抗训练迁移到诚实对齐，非全新但迁移有实质难点 |
| 技术质量 | 8/10 | 方法严谨，CAT/CAPO 双目标、扰动搜索规范 |
| 实验充分性 | 9/10 | 三基准 + 效用消融 + 能力保持，非常全面 |
| 写作质量 | 8/10 | 清晰，讨论部分对与 steering/SFT 的对比很到位 |
| 实用性 | 8/10 | 白盒开源模型可直接复现，闭源不可用 |

> [!tip] 关键启示
> 诚实对齐的关键不只是"怎么训"，更是"用什么数据保持能力"——on-policy 效用数据是能力不退化的重要变量。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读：诚实对齐 + 连续对抗训练 + 能力保持三者结合的代表性工作，尤其效用数据消融一节值得细读。
