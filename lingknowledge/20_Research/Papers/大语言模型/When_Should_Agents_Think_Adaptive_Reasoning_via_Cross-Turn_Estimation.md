---
date: "2026-10-11"
paper_id: "arXiv:2610.12061"
title: "When Should Agents Think? Adaptive Reasoning via Cross-Turn Estimation"
authors: "Yiruo Cheng, Shen Huang, Xiaoshuai Song, Jiejun Tan, Guanting Dong, Pengjun Xie, Ji-Rong Wen, Zhicheng Dou"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Agent
  - Adaptive-Reasoning
  - Reinforcement-Learning
  - Reasoning-Efficiency
quality_score: "8.4/10"
created: "2026-10-11"
updated: "2026-10-11"
status: analyzed
---

# When Should Agents Think? Adaptive Reasoning via Cross-Turn Estimation

## 核心信息
- **论文ID**：arXiv:2610.12061
- **作者**：Yiruo Cheng, Shen Huang, Xiaoshuai Song, Jiejun Tan, Guanting Dong, Pengjun Xie, Ji-Rong Wen, Zhicheng Dou
- **机构**：中国人民大学高瓴人工智能学院 + 阿里巴巴 Token Hub
- **发布时间**：2026-10-08
- **会议/期刊**：arXiv 预印本（cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2610.12061) | [PDF](https://arxiv.org/pdf/2610.12061)
- **引用**：--

## 摘要翻译

### 英文摘要
Large language model (LLM)-based agents have demonstrated strong capabilities on complex tasks. They typically perform reasoning before each action throughout an interaction trajectory. However, reasoning may not be necessary at every turn, as reasoning produced earlier can continue to support subsequent actions. A key challenge is therefore to determine when existing reasoning remains sufficient and when a new reasoning step is needed, without relying on costly generation-based verification. We find that decreases in the likelihood of subsequent reference actions after removing additional reasoning closely track whether those actions remain recoverable given earlier reasoning, providing an effective and lightweight signal for estimating cross-turn action support. Based on this observation, we propose Reasoning Adaptation through Cross-Turn Estimation (RACE), a training approach for adaptive agent reasoning. RACE introduces a Likelihood-Guided Progressive Reasoning Cover Detection (LoGiC) procedure that progressively identifies reasoning turns whose removal has limited impact on the current and subsequent reference actions. The resulting removal signals are incorporated into both supervised fine-tuning and agentic reinforcement learning, enabling the policy to learn when to reason and when to act directly. Extensive experiments on four representative agent benchmarks show that RACE substantially reduces reasoning cost while maintaining or improving task performance.

### 中文翻译
基于大语言模型（LLM）的智能体在复杂任务上展现出强大能力，它们通常在整条交互轨迹中于每个动作之前都进行推理。然而，并非每一轮都需要推理——更早产生的推理可以持续支撑后续动作。因此关键挑战在于：如何在不依赖昂贵"生成式验证"的前提下，判断已有推理何时仍然足够、何时需要新的推理步骤。我们发现，移除额外推理后，后续参考动作似然的下降幅度，能够密切追踪这些动作在仅保留更早推理时是否仍然可恢复，从而为估计跨轮次的动作支撑提供了一个轻量且有效的信号。基于此，我们提出 **RACE**（Reasoning Adaptation through Cross-Turn Estimation，基于跨轮次估计的推理自适应），一种训练智能体自适应推理的方法。RACE 引入 **LoGiC**（Likelihood-Guided Progressive Reasoning Cover Detection，似然引导的渐进式推理覆盖检测）流程，逐步识别出那些移除后对当前及后续参考动作影响有限的推理轮次。所得移除信号被同时纳入监督微调（SFT）与智能体强化学习（RL），使策略学会何时推理、何时直接行动。在四个代表性智能体基准上的大量实验表明，RACE 在保持或提升任务性能的同时大幅降低了推理成本。

### 核心要点提炼
- **研究背景**：ReAct 式智能体在每个交互轮都显式生成推理，长轨迹下推理开销巨大；但早先的推理可能持续支撑后续动作。
- **研究动机**：现有自适应推理方法需逐轮"生成式验证"推理必要性，成本随轨迹增长而急剧上升，尤其难以用于 on-policy RL。
- **核心方法**：用"参考动作似然下降"作为跨轮推理支撑的轻量代理信号，通过 LoGiC 逐步识别可移除推理，构造"推理覆盖"，并用于 SFT + 覆盖感知的 agentic RL。
- **主要结果**：在 ScienceWorld/WebShop/AppWorld/DeepSearch 上，相比标准 SFT+RL，推理 token 分别减少 32.3%/80.2%/46.4%/62%，且任务性能保持或提升。
- **研究意义**：为"智能体何时该思考"提供了轻量、可扩展的训练范式，显著降低推理成本而不牺牲性能。

## 研究背景与动机

### 领域现状
大推理模型（LRM）显著提升了智能体在需要多轮规划与执行的复杂任务上的能力，主流范式（ReAct 等）在每轮动作前显式生成推理，隐式假设"每轮都要推理"。这一假设在长轨迹上带来可观的推理 token 开销。近期"自适应推理"方向（AdaptThink、DART、ARES 等）尝试通过判断每轮是否需要推理来降低成本。

### 现有方法的局限性
- **忽略跨轮持续性**：现有方法通常逐轮独立评估推理必要性，未显式建模"更早的推理可以继续支撑后续动作"这一跨轮持续性。
- **生成式验证成本高**：获取轮级移除信号需要在每个候选推理轮反复生成动作来对比，SFT 数据合成成本随轨迹变长而增加；在 on-policy RL 中，每一轮都要对策略演化后新采样的轨迹重复做生成式验证，引入巨大计算开销，限制可扩展性。

### 研究动机
关键不在于"独立判断每步推理能否移除"，而在于"估计已有推理还能支撑多远、何时需要新推理"。论文希望找到一种**轻量**的替代方案，避免反复的动作生成验证。

## 研究问题

### 核心研究问题
如何在不依赖昂贵生成式验证的前提下，估计已有推理对后续动作的跨轮支撑范围，从而训练智能体在"合适的时候思考、其余时候直接行动"，同时保持任务性能？

## 方法概述

### 核心思想
用"参考动作似然下降"作为"跨轮动作可恢复性"的轻量代理：若移除某轮推理后，后续参考动作的似然几乎不下降，说明该推理可被安全移除。LoGiC 据此逐步构造"推理覆盖"（reasoning cover），所得移除信号同时注入 SFT 与 RL。

### 方法框架

#### 整体架构
RACE 分两个阶段：**SFT 阶段**用 LoGiC 把"全程推理轨迹"转化为"自适应推理轨迹"，教模型何时可直接行动；**RL 阶段**引入覆盖感知的 agentic RL，在成功轨迹上应用 LoGiC 得到 SKIP 轮，通过"推理掩码"（masking）与"推理闭合"（closure）监督，让策略学会终止推理。

![[2610.12061_fig1.png|600]]

> 图1：RACE 训练方法概览。LoGiC 识别可安全移除的推理步骤，所得信号同时用于 SFT 与 agentic RL。

#### 关键组件说明

**组件1：LoGiC（似然引导的渐进式推理覆盖检测）**
- 三步：① 动作似然初始化（在全程推理上下文中计算每个参考动作的似然向量）；② 渐进式推理压缩（从第 2 轮起，逐轮移除推理并重构候选轨迹）；③ 似然验证（若移除后当前及后续所有参考动作的似然下降都在阈值 ε 内，则接受移除）。
- 接受移除的推理被逐步去掉，最终保留的推理构成"推理覆盖"。

![[2610.12061_fig2.png|600]]

> 图2：LoGiC 流程图——从第 2 轮开始测试推理是否可移除，并基于似然下降验证，接受的移除会更新后续轮次的评估上下文。

**组件2：SFT 数据合成**
- 对成功轨迹应用 LoGiC 得到自适应推理轨迹，作为 SFT 训练数据，loss 只作用于模型生成的推理与动作 token。

**组件3：覆盖感知的 Agentic RL**
- **推理掩码**：对成功 on-policy 轨迹，LoGiC 得到 SKIP 轮集合，将这些轮的推理 token 从 GRPO 目标中掩码（保留动作 token 用于任务优化）。
- **推理闭合**：在 SKIP 轮上施加"闭合监督"，鼓励策略生成推理终止 token `e`；最终 actor 目标 = GRPO + λ·L_close。

## 实验结果

### 实验目标
验证 RACE 能否在四个代表性智能体基准上降低推理成本并保持/提升任务性能。

### 数据集与基线
- **数据集**：ScienceWorld（科学推理）、WebShop（网页购物）、AppWorld（应用使用）、DeepSearch（知识密集型推理）。
- **基线**：ReAct 式推理智能体（DeepSeek-V4-Pro、GLM-5.3、GPT-5.5、Claude-Opus-4.6、Kimi-K3、Qwen3.8 系列等）+ 推理高效方法（Think Tool、ADaPT、Sparse ReAct、DART、AdaptThink，均用 Qwen3.5-9B 骨干）。

### 主要结果

- **竞争力**：RACE 以 Qwen3.5-9B 骨干在 ScienceWorld/WebShop/AppWorld/DeepSearch 上成功率达 73.28/53.40/65.13/35.00，接近甚至超过更大规模闭源模型。
- **效率**：相比 Qwen3.5-9B，RACE-SFT-RL 成功提升 54.76/37.80/18.98/5.75 点，同时推理 token 减少 36.5%/80.3%/39.4%/35.8%。
- **RL 增益**：相比 RACE-SFT，RACE-SFT-RL 进一步减少推理 token 28.6%/56.6%/42.9%/55.7%，同时成功提升。
- **消融**：移除 RL 中的 LoGiC 会显著增加推理成本（如 AppWorld 从 1,900 增至 8,574 token）；移除推理掩码也会降低成功率。

### 结果分析
RACE 并非简单地"统一缩短推理"，而是**在更多轮次跳过推理，同时让保留的推理自适应任务特征**：在状态密集型任务（ScienceWorld、AppWorld）上，推理集中在更吃力的交互轮；而在 WebShop、DeepSearch 上则倾向更简洁的推理。这说明 LoGiC 确实捕获了"何时需要推理"的结构化信号。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：刻画了智能体推理的"跨轮持续性"——更早的推理可支撑后续动作，且其有效范围各异。
  - 创新点：把"是否需要推理"从逐轮独立判断，转化为"估计跨轮支撑范围"的问题。
- **贡献2**：发现"参考动作似然下降"是"动作可恢复性"的轻量代理，避免了昂贵的生成式验证。
  - 创新点：只需对已有参考动作做 teacher-forced 打分，即可高效推导轮级移除信号，可扩展至 on-policy RL。

#### 实际应用价值
- **应用场景1**：降低 LLM 智能体的推理成本——在长轨迹交互任务中显著减少 token 消耗，直接降低部署成本与延迟。
- **应用场景2**：可作为通用"自适应推理"训练范式，与 GRPO 等主流 agentic RL 方法兼容，易于落地到多轮 agent 系统。

### 局限性
- LoGiC 依赖参考动作（来自成功轨迹），在失败轨迹或无参考轨迹的场景下不适用（论文对失败轨迹退化为标准 GRPO）。
- 似然下降作为代理信号依赖模型的标定质量，可能引入噪声。
- 实验基于 Qwen3.5 系列，跨模型/跨规模泛化仍有待进一步验证（论文已做 2B/4B/9B 的 scaling 分析）。

### 未来工作
- 探索在失败轨迹或无参考动作场景下的自适应推理信号。
- 将 LoGiC 推广到更通用的 agentic RL 框架与更多模型家族。
- 结合更细粒度的推理分段（而非整轮移除）进一步压缩成本。

### 与相关工作对比
- **vs AdaptThink**：AdaptThink 通过在奖励中加"非思考轮占比"奖励来学习推理分配；RACE 则用似然引导的跨轮估计直接推导移除信号，不引入需要人工调参的奖励项，且在效率上更优（WebShop/AppWorld/DeepSearch 用更少 token）。
- **vs DART / ARES**：这些方法依赖多次"无思考草案"的一致性判断或逐轮生成验证；RACE 用似然计算替代生成，成本更低。
- **研究路线归属**：属于"推理高效智能体"与"agentic RL"的交叉，补充了 agentic RL 中"自适应推理行为"这一较少被研究的维度。
