---
date: "2026-09-28"
paper_id: "2609.31619"
title: "Learning to Stop without Learning to Stop: Self-Supervised Confidence Training Improves Reasoning Efficiency"
authors: "Parsa Hosseini, Akasha Tigalappanavara, Sumit Nawathe, Chenrui Fan, Sourya Basu, Genta Indra Winata, Anirban Das, Soheil Feizi, Nima Chitsazan"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - 推理效率
  - 置信度
  - 自监督
quality_score: "9.0/10"
related_papers: []
created: "2026-09-28"
updated: "2026-09-28"
status: analyzed
---

# Learning to Stop without Learning to Stop: Self-Supervised Confidence Training Improves Reasoning Efficiency

## 核心信息
- **论文ID**：2609.31619
- **作者**：Parsa Hosseini, Akasha Tigalappanavara, Sumit Nawathe, Chenrui Fan, Sourya Basu, Genta Indra Winata, Anirban Das, Soheil Feizi, Nima Chitsazan
- **机构**：--（合作者含 Soheil Feizi（UMD）、Genta Indra Winata（HKUST）等，arXiv 元数据未给出统一机构）
- **发布时间**：2026-09-25
- **会议/期刊**：arXiv 预印本（cs.AI, cs.CL, cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2609.31619) | [PDF](https://arxiv.org/pdf/2609.31619)
- **引用**：--

## 摘要翻译

### 英文摘要
Reasoning models often generate very long reasoning traces, making inference computationally expensive. Existing approaches typically improve efficiency either through inference-time early-stopping mechanisms or by explicitly encouraging shorter reasoning during training, for example through reinforcement learning with length penalties. We show that substantial efficiency gains can instead emerge from a different kind of supervision: confidence. Using a self-supervised procedure, we fine-tune reasoning models to predict their confidence in the answer at intermediate points along their own reasoning trajectories using only 600 training problems. Confidence is used only as a training target: the loss contains no objective for reasoning length, efficiency, or stopping. At inference, the fine-tuned models use the standard generation procedure, with no confidence elicitation or early-stopping mechanism. Despite this, self-supervised confidence fine-tuning makes reasoning more efficient, reducing generated tokens by up to 25% at matched accuracy across Gemma, Qwen, Nemotron, and GPT-OSS models on mathematical, scientific, and coding reasoning benchmarks, with efficiency gains comparable to methods that explicitly optimize for shorter reasoning. Analysis of reasoning episodes further shows that confidence supervision largely preserves the base models' high-level reasoning composition rather than selectively suppressing particular behaviors. Our results suggest that efficient reasoning may emerge as a downstream consequence of learning metacognitive signals, without being directly optimized.

### 中文翻译
推理模型常常生成非常长的推理轨迹，使得推理计算开销高昂。现有方法通常通过推理时的早停机制，或在训练时显式鼓励更短的推理（例如带长度惩罚的强化学习）来提升效率。作者证明，显著的效率提升可以从一种不同的监督信号中涌现：**置信度（confidence）**。通过一种自监督流程，作者仅用 600 道训练题，微调推理模型在其自身推理轨迹的中间节点预测对答案的置信度。置信度**仅作为训练目标**：损失函数中不含任何关于推理长度、效率或停止的目标。推理时，微调后的模型采用标准生成流程，不进行任何置信度提取或早停机制。尽管如此，自监督置信度微调使推理更高效——在数学、科学、编程推理基准上，Gemma、Qwen、Nemotron、GPT-OSS 模型在精度匹配的前提下最多减少 25% 的生成 token，效率提升与显式优化"更短推理"的方法相当。对推理片段的进一步分析表明，置信度监督在很大程度保留了基模型的高层推理组成，而非选择性抑制某些行为。这些结果表明，高效推理可能是学习元认知信号的一个下游结果，而无需被直接优化。

### 核心要点提炼
- **研究背景**：推理模型（o1/R1 类）靠长 CoT 提升性能，但 token 开销巨大，推理成本成为落地瓶颈。
- **研究动机**：现有提速手段（推理时早停、RL 长度惩罚）都显式地"逼"模型少说，可能破坏推理结构；能否找到更自然的提速路径？
- **核心方法**：自监督置信度微调——让模型在推理轨迹中间预测"我对当前答案有多大把握"，仅 600 题，且置信度只做训练目标、不参与推理。
- **主要结果**：精度持平下最多 -25% token，跨 4 个模型家族、三大类基准，效果媲美显式长度优化方法。
- **研究意义**：揭示"高效推理可从元认知信号中涌现"，为推理效率提供了一条不牺牲推理质量的新范式。

## 研究问题

### 核心研究问题
能否在不显式优化"推理长度/停止时机"的前提下，仅通过让模型学习**对自身答案的置信度**，就自然地缩短推理、提升效率？

传统两条路径各有代价：
1. **推理时早停 / 置信度门控**：需额外推理时机制，可能截断关键推理步骤；
2. **RL 长度惩罚 / 短推理蒸馏**：显式压制长推理，可能破坏模型固有的高层推理组成。

作者假设：**效率可以是"元认知"的副产品**——若模型在推理途中就"知道"答案已足够可靠，它自然会更早收敛，无需外力约束。

## 方法概述

### 核心思想
用**置信度作为唯一的新增监督信号**，其余一律不动：不改变推理时的生成流程，不引入早停，不加长度惩罚。让"更高效"从"更自知"中涌现。

![[four_panel_epoch00_page1.png|800]]

> 图：自监督置信度训练的整体框架与效果——模型在推理轨迹中间点预测置信度，仅作为训练目标，推理时无任何额外机制。

### 方法框架

**1. 自监督置信度标签构造**
- 对每个训练问题，让基模型生成多条推理轨迹；
- 在轨迹的**中间节点**，用该轨迹最终答案的正确性作为"置信度"的软/硬标签（自监督，无需人工标注）。

**2. 置信度预测微调**
- 仅微调模型使其在中间位置输出"对当前答案的把握"；
- 损失**只含置信度预测误差**，明确排除长度、效率、停止等任何目标。

**3. 标准推理**
- 微调后模型推理时走标准自回归生成；
- **不提取置信度、不早停、不改采样**，因此部署零额外成本。

### 关键创新
1. **效率 = 元认知的涌现，而非被优化** - 首次系统证明仅靠置信度监督即可缩短推理，与显式优化可比。
2. **极低数据成本** - 仅 600 道训练题，与动辄上万条 RL 轨迹的方法形成鲜明对比。
3. **零推理时开销** - 不引入门控/早停/额外模型，落地友好。

## 实验结果

### 数据集
- 数学推理、科学推理、编程推理三大类基准（覆盖 Gemma、Qwen、Nemotron、GPT-OSS 四个模型家族）。

### 主要结果
- **-25% token**：精度匹配的前提下，生成 token 最多减少 25%。
- **媲美显式长度优化**：效率增益与"显式优化更短推理"的方法相当。
- **保结构**：分析显示置信度监督保留了基模型的高层推理组成，而非压制某类行为。

### 实验结果图

![[main_fig4_confidence_rounds_page1.png|800]]

> 图：置信度随推理轮次的变化，以及 token 减少与精度保持的关系。

![[reasoning_category_mix_shift_page1.png|800]]

> 图：推理类别的组成迁移分析——置信度监督大体保留原有推理构成。

## 深度分析

### 研究价值
- **理论贡献**：为"高效推理"提供了一种非对立的解释——它是元认知信号（置信度）的自然下游结果。
- **实际应用**：零推理时开销即可降本 25%，对推理模型的服务部署极具价值。
- **领域影响**：可能改变"长度惩罚"这一默认提速范式，启发新一代高效推理训练。

### 优势
- 数据与算力成本极低（600 题）。
- 与显式方法效果相当，却不牺牲推理结构。
- 部署无侵入，兼容现有推理管线。

### 局限性
- 效率提升幅度（-25%）在绝对意义上仍有限，且与基模型、任务类型强相关。
- 置信度标签的自监督构造质量未被充分消融。
- "为何置信度能缩短推理"的因果机制仍偏观察性。

### 适用场景
- 对推理延迟/成本敏感的生产级推理模型；
- 希望在**不改变推理协议**前提下压缩 token 的场景。

## 技术路线定位
本文属于**推理效率优化（Efficient Reasoning）**技术路线，具体子方向为**通过元认知/置信度监督实现无侵入式提速**，与早停、长度惩罚、短推理蒸馏形成互补。

## 未来工作建议
1. 探索置信度监督与长度惩罚/早停的**组合**是否产生叠加增益。
2. 在更大规模、更复杂任务上验证效率增益的鲁棒性。
3. 深入分析置信度改变推理的因果机制（是"更早确信"还是"更早放弃无关探索"）。

## 我的综合评价

### 价值评分
- **总体评分**：**9.0/10** - 立意新颖（"学会停但不需要学停"）、结果干净、落地价值高。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 把效率问题从"约束"转向"元认知"，视角独特 |
| 技术质量 | 8/10 | 方法简洁，但因果机制分析偏观察性 |
| 实验充分性 | 9/10 | 跨 4 模型家族、三大类基准，消融较全 |
| 写作质量 | 9/10 | 结构清晰、动机与结论呼应 |
| 实用性 | 9/10 | 零推理时开销，可直接落地 |

### 突出亮点
- "学会停"却"不学停"——用一个漂亮的反直觉结果揭示涌现式效率。
- 仅 600 题即可获得媲美显式优化的效果。

### 可借鉴点
- 自监督元认知信号的构造方式（中间节点置信度标注）可迁移到其他能力的无侵入式提升。

### 批判性思考
- -25% 的 token 压缩是否主要来自"更早终止冗余验证"，而非真正提升推理质量？需进一步验证。

## 相关论文
- [[20_Research/Papers/大语言模型|大语言模型]] - 相关领域

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.31619)
- [PDF](https://arxiv.org/pdf/2609.31619)

> [!tip] 关键启示
> 高效推理可能是"学会自信"的自然结果，而非"被逼简短"的产物。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐——推理效率优化的一个新范式，立意与结果俱佳。
