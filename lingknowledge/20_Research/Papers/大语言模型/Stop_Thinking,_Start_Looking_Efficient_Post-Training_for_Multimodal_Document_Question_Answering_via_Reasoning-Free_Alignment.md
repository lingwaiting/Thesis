---
date: "2026-07-19"
paper_id: "2607.14682"
title: "Stop Thinking, Start Looking: Efficient Post-Training for Multimodal Document Question Answering via Reasoning-Free Alignment"
authors: "Harikrishnan P M, Goutham Vignesh, Ganesh Parab, Saisubramaniam Gopalakrishnan, Vishal Vaddina, Varun V, Rohit Agrawal"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 多模态
  - 强化学习
  - GRPO
  - 文档视觉定位
  - Visual-Grounding
  - Document-QA
quality_score: "8.5/10"
created: "2026-07-19"
updated: "2026-07-19"
status: analyzed
---

# Stop Thinking, Start Looking: Efficient Post-Training for Multimodal Document QA via Reasoning-Free Alignment

## 核心信息
- **论文ID**：2607.14682
- **作者**：Harikrishnan P M, Goutham Vignesh, Ganesh Parab, Saisubramaniam Gopalakrishnan, Vishal Vaddina, Varun V, Rohit Agrawal
- **机构**：Phi Labs, Quantiphi（印度/加拿大）
- **发布时间**：2026-07-16
- **会议/期刊**：ICML 2026 投稿
- **链接**：[arXiv](https://arxiv.org/abs/2607.14682) | [PDF](https://arxiv.org/pdf/2607.14682)
- **引用**：--

## 摘要翻译

### 英文摘要
Efficient multimodal document question answering with explicit visual grounding, locating the precise document region that supports each answer remains an open challenge. Current approaches bifurcate into Supervised Fine-Tuning (SFT), which requires large annotated datasets and reaches optimization plateaus, and reasoning-centric Reinforcement Learning (RL), which depends on verbose intermediate traces that inflate inference token cost without clear benefit. We introduce Perception-RFT, a training framework that applies Group Relative Policy Optimization (GRPO) to multimodal document QA, bypassing intermediate reasoning tokens to directly align visual features with structured grounding outputs. To rigorously evaluate the necessity of reasoning, we construct a reasoning variant under identical reward settings. We find that reasoning-enabled models suppress their reasoning traces during training, converging to direct perception-based policies at the 4B parameter scale, reducing per-query inference token length by more than 60%, while reasoning-enabled RL underperforms perception-only training. Through a fine-grained analysis of Qwen3-VL-4B optimization dynamics, we confirm that SFT saturation and cold-start RL instability established in text-domain post-training extend to multimodal, and identify a previously uncharacterized Grounding Divergence: a selective trade-off between semantic robustness and geometric precision on two out of distribution (OOD) benchmarks (4,828 samples) under joint RL optimization. We further show that an early SFT→RL transition achieves comparable precision with 65% less training data.

### 中文翻译
多模态文档问答中的显式视觉定位——精确定位支持每个答案的文档区域——仍是一个开放挑战。当前方法分为两类：监督微调（SFT）需要大量标注数据且会达到优化平台期；以推理为中心的强化学习（RL）依赖冗长的中间推理痕迹，增加了推理 token 成本而无明显收益。我们提出 Perception-RFT，一个将 GRPO 应用于多模态文档 QA 的训练框架，绕过中间推理 token，直接对齐视觉特征与结构化定位输出。为严格评估推理的必要性，我们在相同奖励设置下构建了推理变体。我们发现启用推理的模型在训练中会抑制其推理痕迹，在 4B 参数规模下收敛到直接的感知策略，每查询推理 token 长度减少 60% 以上，而推理版 RL 弱于纯感知训练。通过 Qwen3-VL-4B 优化动态的细粒度分析，我们确认了文本域后训练中的 SFT 饱和和冷启动 RL 不稳定性扩展到多模态，并发现了一个此前未被描述的 Grounding Divergence 现象：在两个 OOD 基准（4828 样本）上联合 RL 优化时，语义鲁棒性与几何精度之间存在选择性权衡。我们还发现早期 SFT→RL 过渡可在减少 65% 训练数据的情况下达到可比精度。

### 核心要点提炼
- **研究背景**：多模态文档视觉定位（DVG）需要模型同时进行语义提取和精确几何定位，现有 SFT 和推理 RL 方法各有明显局限
- **研究动机**：到底需不需要推理？SFT 是否真的会饱和？这些问题在多模态领域未被系统研究
- **核心方法**：Perception-RFT = GRPO + Direct Perception 约束 + Gated Dense Reward，完全绕过推理 token
- **主要结果**："不做推理"比"做推理"在 DVG 上表现更好，推理 token 减少 62%，RFTs 在 ID 上超越 SFT（$F1_{\text{all}}$: 0.668→0.718）
- **研究意义**：首次在多模态文档 QA 中系统证明了推理对感知主导任务不仅不需要、甚至有害

## 研究背景与动机

### 领域现状
文档视觉定位（Document Visual Grounding, DVG）要求模型从文档图像中同时提取文本答案并给出精确的边界框。当前主流方法分为两大范式：

1. **SFT 路线**：如 DOGR，依赖大规模标注数据集强制泛化，但存在优化平台期——进一步训练收益递减
2. **推理 RL 路线**：如 DocThinker，认为精确定位需要显式中间推理来桥接语义理解和空间定位

### 现有方法的局限性
- SFT 达到饱和点后，继续训练无法改善几何精度，反而可能损害 OOD 语义灵活性
- 推理方法引入大量 token 开销，且推理痕迹对感知主导任务是否必要未被验证
- 绝大多数 MLLM 在无外部 OCR 输入时 IoU 接近零，严重依赖 OCR 管道

### 研究动机
核心问题：**SFT 扩展是否能真正提升鲁棒性？冗长的推理痕迹对 DVG 是必需还是不必要的计算负担？** 本文在文本域后训练研究的基础上，首次在多模态文档 QA 中系统地探究这些问题。

## 研究问题

**核心研究问题**：在多模态文档视觉定位中，显式推理是否必要？直接感知对齐（绕过推理）能否比推理驱动方法更有效？

具体包括：
- SFT 饱和行为是否扩展到多模态？
- 冷启动 RL 不稳定性是否也存在于视觉任务？
- 推理痕迹在 RL 优化过程中是被保留还是被压缩？
- 联合 RL 优化是否引入未表征的泛化权衡？

## 方法概述

### 核心思想
**"Stop Thinking, Start Looking"** — 文档视觉定位是感知主导任务，直接对齐视觉特征与空间输出比推理中介生成更有效。Perception-RFT 用 GRPO 做 RL，但通过系统提示显式禁止推理，迫使模型直接从像素映射到坐标。

### 方法框架

#### 整体架构

![[Perception_RFT_diagram_page1.png|800]]

> 图1：Perception-RFT 框架。左：系统提示强制 Direct Perception 约束。右：Gated Dense Reward 机制将优化面分为 Shaping State 和 Success State。

#### 各模块详细说明

**模块1：Direct Perception 约束**
- **功能**：通过系统提示显式禁止模型生成推理痕迹
- **核心提示**：*"Do NOT include explanations, reasoning, or intermediate steps; Output ONLY the final valid JSON result."*
- **效果**：优化目标从"最大化逻辑一致性"转变为"最大化联合语义和几何精度"

**模块2：GRPO 优化框架**
- **功能**：对每组采样 $G$ 个输出，基于组内归一化奖励分布计算优势 $A_i$
- **目标函数**：
  $$\mathcal{J}(\theta) = \mathbb{E}_{q \sim D} \left[ \frac{1}{G} \sum_{i=1}^{G} \left( \frac{\pi_\theta(o_i|q)}{\pi_{old}(o_i|q)} A_i - \beta D_{\mathrm{KL}}(\pi_\theta \| \pi_{ref}) \right) \right]$$
- **关键**：与传统 GRPO 不同，Perception-RFT 用它来强制直接感知对齐，而非引出潜在推理

**模块3：Gated Dense Reward（关键创新）**
- **三个组成部分**：
  - $R_{\text{format}}$：二值奖励，验证 JSON 格式正确性
  - $R_{\text{sem}}$：ANLS 二次缩放，惩罚近似匹配
  - $R_{\text{loc}}$：**门控 IoU**——低于阈值 $\tau=0.6$ 时显著缩放到 $\lambda \cdot \text{IoU}$，超过阈值跳变到 1.0（"悬崖"优化面）
- **设计动机**：防止模型学会预测"安全"的过大边界框来最大化 IoU

**模块4：推理变体（对照实验）**
- Reasoning-RFTb：允许模型生成 `<think>` 标签内的推理痕迹
- 奖励函数和优化目标完全相同
- 推理痕迹无监督信号

**模块5：数据构建**
- 从 DocILE 和 FormNLU 聚合 23,696 个 QA 样本
- 三阶段变换策略：语义增强（LLM 生成键别名）、措辞泛化（训练/评估使用不相交模板）、动态子采样（每 epoch 随机选最多 20% 的键）

## 实验结果

### 实验设置
- **模型**：Qwen3-VL-4B + LoRA（r=16, α=16）
- **硬件**：单张 NVIDIA A100（80GB）
- **框架**：Unsloth
- **训练**：SFT 3 epochs（1113 steps），RL 6k samples（GRPO G=8）
- **评估**：ID（6194 样本）+ OOD（DOGR-Bench 800 + MMDocBench 4028）

### 主要结果

| 模型 | 设置 | ID $F1_{\text{all}}$ | OOD DOGR $F1_{\text{all}}$ | OOD MMDoc $F1_{\text{all}}$ |
|------|------|---------------------|---------------------------|----------------------------|
| Qwen3-VL-4B | Zero-Shot | 0.262 | 0.359 | 0.389 |
| Qwen3-VL-4B | SFT | 0.668 | 0.666 | 0.555 |
| **Qwen3-VL-4B** | **RFTs** | **0.718** | **0.685** | **0.569** |
| Qwen3-VL-4B | RFTb | 0.411 | 0.600 | 0.552 |
| Gemini 3.0 Flash | Zero-Shot | 0.581 | 0.715 | 0.701 |

### 关键发现

**1. SFT 饱和 → RL 突破**
- SFT 在 $F1_{\text{all}}$ 达 0.668 后饱和
- RFTs 进一步提升至 0.718（语义 0.756→0.773，定位 0.769→0.821）

**2. 推理消融：不做推理 > 做推理**

| 模型 | ID $F1_{\text{all}}$ | OOD $F1_{\text{all}}$ |
|------|---------------------|----------------------|
| RFTb | 0.411 | 0.600 |
| Reasoning-RFTb | 0.303 | 0.382 |

- 推理 token 长度从 ~191 降至 ~72（62% 减少）
- 推理版训练方差更大，收敛更不稳定

**3. Grounding Divergence 现象**
- 定位精度在 OOD 上可迁移（0.416→0.759），但语义提取不跟随改善
- 所有训练变体在 DOGR-Bench 上的语义 F1 低于零样本基线（0.722 vs 0.743）
- 不同于灾难性遗忘（所有能力均匀退化），这是**非对称泛化**

**4. 数据效率**
- RFTs-Early（仅 300 SFT steps → RL）匹配或超过全量 SFT
- 训练数据减少 ~65%

## 深度分析

### 研究价值评估

#### 理论贡献
- **首次系统证明**：在多模态 DVG 中，推理对感知主导任务不必要甚至有害
- **Grounding Divergence**：发现 RL 优化在 OOD 上产生非对称泛化——几何精度可迁移，语义鲁棒性不可迁移
- **验证文本域发现**：确认 SFT 饱和和冷启动 RL 不稳定性的跨模态普适性
- **方法论贡献**：Gated Dense Reward 的门控设计具有通用参考价值

#### 实际应用价值
- 推理 token 减少 62% → 直接降低推理延迟和部署成本
- 单张 A100 即可完成所有实验 → 对资源受限场景友好
- 企业审计场景（需同时满足精确文本提取和定位）是直接应用场景

### 方法优势
1. **效率**：推理 token 减少 62%，训练数据减少 65%
2. **简洁**：不需要推理数据标注，不需要复杂的 prompt 工程
3. **可复现**：单卡 A100 + 开源模型，全流程可复现

### 局限性
1. **仅验证 4B 规模**：更大模型上的 scaling behavior 未知
2. **冷启动推理对比**：缺少 SFT→RL 的推理版本（因无推理标注的 DVG 数据），推理是否在充分 SFT 初始化后有用仍是开放问题
3. **Grounding Divergence 未解决**：OOD 语义退化仍是开放挑战
4. **领域局限**：仅在金融文档上训练，跨域泛化有限

### 适用场景
- ✅ **适合**：文档密集型企业应用（审计、金融分析）、需要精确边界框的场景
- ⚠️ **谨慎**：跨域部署（需评估 Grounding Divergence）
- ❌ **不适合**：需要多步推理的复杂文档理解任务

## 我的综合评价

### 价值评分
**8.5/10** — 方法简洁有力，发现重要且反直觉，但规模验证有限

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | "不做推理更好"在 DVG 领域是新发现，Gated Reward 设计巧妙 |
| 技术质量 | 8/10 | 方法扎实，对照组设计严谨，实验设置合理 |
| 实验充分性 | 7/10 | 主实验充分，但缺少更大规模模型的验证 |
| 写作质量 | 8/10 | 结构清晰，论证充分，视觉化效果好 |
| 实用性 | 9/10 | 直接降低推理成本，单卡可复现，实用价值高 |

> [!tip] 关键启示
> 对感知主导的视觉任务，强制模型"直接看"比"先想再看"更有效——这一发现挑战了当前"一切皆推理"的趋势，值得其他视觉任务借鉴。

> [!warning] 注意事项
> - Grounding Divergence 意味着 RL 后必须在目标域上重新评估语义能力
> - 4B 规模的结论不一定适用于更大模型
> - 方法针对结构化输出（JSON），不一定适用于自由文本生成

> [!success] 推荐指数
> ⭐⭐⭐⭐½ 强烈推荐！对多模态 RL 对齐和文档理解方向的研究者和工程师都有重要参考价值。
