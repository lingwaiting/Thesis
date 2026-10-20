---
date: "2026-10-20"
paper_id: "arXiv:2610.25212"
title: "Calibration-Free Early Stopping for Chain-of-Thought Reasoning via E-processes"
authors: "Dong-Jae Lee, Gyojin Han, Junmo Kim"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - Chain-of-Thought
  - Test-Time-Scaling
  - E-process
  - Anytime-Valid-Inference
  - Adaptive-Inference
quality_score: "9.0/10"
related_papers: []
created: "2026-10-20"
updated: "2026-10-20"
status: analyzed
---

# Calibration-Free Early Stopping for Chain-of-Thought Reasoning via E-processes

## 核心信息
- **论文ID**：arXiv:2610.25212
- **作者**：Dong-Jae Lee, Gyojin Han, Junmo Kim
- **机构**：韩国科学技术院（KAIST）
- **发布时间**：2026-10-18
- **会议/期刊**：arXiv（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.25212) | [PDF](https://arxiv.org/pdf/2610.25212)
- **引用**：--

## 摘要翻译

### 英文摘要
Reasoning large language models (LLMs) benefit from test-time scaling, motivating adaptive policies that spend tokens when they help and stop early otherwise. Existing rules halt when a confidence or stability signal crosses a threshold, but such thresholds rarely transfer across models and datasets, and validation-calibrated rules lose their guarantee under shifts in test-time horizon or signal dynamics. We reframe adaptive stopping as sequential evidence accumulation along the reasoning trajectory rather than threshold calibration on held-out data. Our framework runs two e-processes on a shared per-chunk signal: a commit stream tracks the signal's position relative to its slow trend, and an abstain stream tracks its signed velocity, making the two anti-correlated by construction. Under signal-level nulls on the per-chunk increments, Ville's inequality yields an anytime-valid signal-level false-alarm bound at user-specified significance levels, with no held-out calibration. Across various model–dataset combinations, the framework matches or exceeds calibration-based stopping on the token–accuracy Pareto front using zero calibration data, and the same significance level transfers unchanged across cells where calibration becomes infeasible.

### 中文翻译
推理型大语言模型受益于 test-time scaling，这催生了"该花 token 时多花、能早停时早停"的自适应策略。现有方法在置信度或稳定性信号跨越某个阈值时停止，但这类阈值很难在模型与数据集之间迁移，而且基于验证集校准的规则在测试时 horizon 或信号动态发生偏移时就不再具有保证。本文把自适应停止重新框架化为"沿推理轨迹的序贯证据累积"，而非"在留出数据上的阈值校准"。框架在共享的逐块信号上运行两个 e-process：一个 commit 流追踪信号相对于其慢趋势的位置，一个 abstain 流追踪信号的带符号速度，二者在构造上即反相关。在逐块增量的信号级零假设下，Ville 不等式在用户指定的显著性水平上给出 anytime-valid 的信号级误报界，且无需任何留出集校准。在各种模型-数据集组合上，该框架用零校准数据即可在 token-accuracy Pareto 前沿上匹配或超越基于校准的停止方法，且同一个显著性水平在那些校准变得不可行的组合间原封不动地迁移。

### 核心要点提炼
- **研究背景**：test-time scaling 带来"何时停止推理"的计算分配问题，固定预算或启发式阈值都难以迁移。
- **研究动机**：校准式规则（如 Conformal Thinking）依赖留出集，其保证在信号动态/预算偏移时失效，且很多组合下校准本身不可行。
- **核心方法**：把自适应停止重新建模为 e-process 序贯证据累积，提出 CES 框架——用两个反相关的 e-process（commit 追踪位置、abstain 追踪速度）做对称决策。
- **主要结果**：Ville 不等式提供 anytime-valid 的 α 级误报界，零校准数据即可匹配/超越 Conformal Thinking 的 Pareto，α 可跨组合原样迁移。
- **研究意义**：为推理模型的早停提供了首个"免校准 + 随时有效"的严格框架，把停止规则从"阈值工程"推向"统计保证"。

## 研究背景与动机

### 领域现状
推理型 LLM 通过 test-time scaling 提升数学、科学、符号推理等任务表现，长思维链已成为标准推理工具。但长 CoT 也带来计算分配难题：有些样本很早就收敛却继续消耗 token，有些样本耗尽预算也毫无进展。已有自适应推理方法监控某个置信度/稳定性信号，在信号跨过阈值时停止，把固定预算策略转化为实例相关的停止规则。

### 现有方法的局限性
这类方法把"预算选择"问题换成了"阈值选择"问题：基于置信度、熵或稳定性的阈值几乎无法跨模型/数据集迁移。风险控制类方法（如 Conformal Thinking）通过在留出验证集上把阈值校准到指定错误率来解决，但其保证以"校准分布与部署分布一致"为前提。而在模型×基准×提示格式的大量组合下，逐格校准要么不可用、要么在严格风险目标与有限验证预算下不可行。更本质的问题是结构性的：一条 CoT 轨迹不是单一预测，而是一串依赖的中间状态序列，要控制的事件是"不安全的停止规则在轨迹上任何时刻是否触发"，而非单个样本是否越过阈值。

### 研究动机
作者提出三条设计目标：(i) 不依赖留出校准集；(ii) 用单一决策规则对称处理 commit 与 abstain；(iii) 用户可直接控制显著性水平 α。这直接导向把 e-process 引入 CoT 停止问题——序贯证据累积器，其错误保证在任何停止时刻都成立。

## 研究问题

### 核心研究问题
1. 能否用 e-process 为推理模型的早停提供"免校准、anytime-valid"的错误控制保证？
2. 如何用单一决策规则对称地处理 commit（判定答案已稳定）与 abstain（判定继续推理无益）两种决策？
3. 该框架能否在零校准数据下匹配甚至超越校准式方法（如 Conformal Thinking）的 token-accuracy Pareto，并实现跨组合的 α 迁移？

## 方法概述

### 核心思想
不询问"原始信号 s_t 是否越过校准阈值"，而是检验"轨迹是否累积了支持停止的序贯证据"。作者构造双 e-process：E_t^+ 累积 commit 证据、E_t^- 累积 abstain 证据；越过上界表示继续推理是冗余的，越过下界表示继续推理不太可能改善答案并触发 fallback。

### 方法框架

#### 整体架构
CES 框架把每个 chunk 的信号 s_t（可以是置信度、熵、答案稳定性等任意已有信号）映射为有界证据增量 φ_t^m ∈ [-1,1]，通过乘法更新非负财富过程 E_t^m = ∏(1 + λ_i^m φ_i^m)，其中 λ 为可预测的下注比例。在信号级零假设 H_0^m : E[φ_t^m | F_{t-1}] ≤ 0 下，E_t^m 是非负上鞅，由 Ville 不等式保证 Pr(sup_t E_t^m ≥ 1/α_m) ≤ α_m，且对任意停止时刻（包括自适应选择）都成立。

![[traj_examples_alpha01_v2_page1.png|600]]

> 图：CES 的轨迹示例。commit 流（位置）与 abstain 流（速度）在构造上反相关，二者分别驱动"提前提交"与"提前弃权"两个对称出口。

#### 各模块详细说明

**模块1：位置-速度分解（Position-Velocity Decomposition）**
- **功能**：把共享信号 s_t 分解为两个互补的决策信号。
- **输入**：逐块信号 s_t。
- **输出**：commit 增量（位置，追踪 s_t 相对其慢趋势的偏离）与 abstain 增量（速度，追踪 s_t 的带符号速度）。
- **关键技术**：用 EMA-deviation 变换把轨迹信号映射到有界证据增量；两个流在构造上反相关——上升的 s_t 同时给 commit 提供证据、消耗 abstain 证据。

**模块2：下注比例（Betting Rate）**
- **功能**：控制证据累积的功率与保守性之间的权衡。
- **输入**：历史证据增量。
- **输出**：可预测的下注比例 λ_i。
- **关键技术**：λ 可固定、从小组合中选，或采用 aGRAPA 等自适应策略最大化期望对数财富增长，且保证 1 + λ_i φ_i ≥ 0 使财富非负。

**模块3：双 e-process 早期出口（Dual E-Process Early Exits）**
- **功能**：对称地做出 commit / abstain 决策。
- **输入**：两个财富过程 E_t^+、E_t^-。
- **输出**：停止时刻 τ_m = inf{t : E_t^m ≥ 1/α_m}。
- **关键技术**：单一决策规则同时覆盖 commit 与 abstain，停止决策不依赖任何单一的"信号→正确性"映射。

### 关键创新
1. **免校准的 anytime-valid 保证**：Ville 不等式给出信号级误报界，α 是唯一用户参数，无需留出集。
2. **位置-速度双 e-process 构造**：两个流反相关，对称处理 commit/abstain，避免单一映射带来的偏置。
3. **跨组合 α 迁移**：同一显著性水平在模型/数据集/预算变化时原样适用，克服校准式方法"逐格校准"的痛点。

## 实验结果

### 实验设置
- **模型与数据集**：多组模型-数据集组合，涵盖数学/科学（含 GPQA-Diamond）与代码推理等；backbone 包括 DeepSeek-R1-Distill-Llama-8B/70B 等。
- **信号**：置信度、EAT（Emission Answer Token）、稳定性等已有轨迹统计量。
- **对照**：与 Conformal Thinking 的 token-accuracy Pareto 直接对比，并显式计算中间探测开销（含 FLOPs 归一化）。

### 主要结果
- 在多种模型-数据集组合下，CES 用零校准数据即可在 token-accuracy Pareto 前沿上**匹配或超越** Conformal Thinking。
- 在交叉组合部署（cross-cell transfer）中，同一个 α 原样迁移，而 CT 的校准在严格风险目标下不可行或无法迁移。
- 消融实验验证了 φ+ 构造与 λ 更新规则各自的贡献；单调 commit 控制与停止结果分布说明 commit 证据随轨迹单调累积。

### 结果分析
CES 的核心优势在于"保证与部署无关"：校准式方法把规则绑定到单一 cell 的统计量上，而 CES 的信号级界天然地跨预算与信号动态迁移。这意味着在真实部署中（模型、基准、提示格式千变万化），CES 无需为每个 cell 重新收集验证集即可提供一致的错误控制。

## 深度分析

### 研究价值
- **理论贡献**：把 e-process / anytime-valid 推断首次系统引入 LLM 推理早停，提供了严格的错误控制语义。
- **实际应用**：为 test-time scaling 的自适应推理提供免校准的"即插即用"停止规则，落地门槛低。
- **领域影响**：直接回应当前"自适应推理 / 预算控制"的热点，与 Conformal Thinking 形成清晰的对照与互补。

### 优势
- 保证严格（anytime-valid）且不依赖校准数据，实用性强。
- 位置-速度分解的对称设计优雅，避免单一阈值映射的脆弱性。
- 实验覆盖多 backbone、多数据集、跨 cell 迁移与 FLOPs 归一化，扎实。

### 局限性
- 保证是"信号级"的（对逐块增量的零假设），不直接等价于"答案正确性"的误差控制，二者之间的 gap 需要用户理解。
- 依赖已有信号的"质量"，信号本身设计（confidence/entropy 等）不在本文范围。
- 实验结果主要集中于相对 Conformal Thinking 的 Pareto 对比，未与更多启发式早停策略做全面横向比较。

### 适用场景
- 需要可解释、有保证的自适应推理停止策略的推理模型部署。
- 多模型/多数据集/多变预算的统一早停接口，尤其是校准数据难以获取的场景。

## 与相关论文对比

与 Conformal Thinking 的直接对比是本文核心：CT 用 split-conformal 在留出集上校准阈值，保证是"固定时刻、绑定校准分布"的经验界，无法跨 cell 迁移；CES 用 e-process 提供 anytime-valid、信号级的界，无需校准即可迁移。这一对比凸显了"免校准 + 随时有效"相对"校准式风险控制"在自适应推理场景下的结构性优势。此外，与 [[Think_More_or_Read_More_A_Token-Budget_Theory_of_Reasoning_with_Shared_Context|token-budget 理论]] 这类从理论角度研究推理预算的工作形成互补——一个回答"何时停"，一个回答"读还是想"。

## 技术路线定位

本文属于**推理模型自适应推理 / test-time scaling 效率**方向，位于"预算分配—早停策略—统计保证"技术路线的上游：它不改进信号本身，而是提供一个免校准、有保证的停止策略容器，供任意已有信号接入，与下游的推理效率优化（稀疏注意力、弹性宽度等）正交且互补。

## 未来工作建议

1. **作者建议**：探索更多信号与更广泛的任务类型，以及信号级保证与答案级保证之间的桥梁。
2. **基于分析的延伸**：将 CES 与更先进的信号设计（如学习式 probe）结合，看能否在保证不变的前提下进一步提升 Pareto。
3. **方法延伸**：研究 abstain 后 fallback 策略的选择（如切换更大模型 / 检索增强）与 CES 的联合优化。

## 我的综合评价

### 价值评分
- **总体评分**：**9.0/10** — 理论严谨、实用价值高，为推理早停提供了漂亮的免校准保证框架。
- **分项评分**：
  - 创新性：9/10 — 把 anytime-valid 推断引入 CoT 早停，视角新颖。
  - 技术质量：9/10 — 构造清晰，保证严格，位置-速度分解优雅。
  - 实验充分性：8/10 — 跨模型/数据集/迁移/FLOPs 归一化，覆盖较全。
  - 写作质量：8/10 — 结构清晰，理论附录完整。
  - 实用性：9/10 — 免校准、即插即用，落地门槛低。

### 突出亮点
- "免校准 + anytime-valid"这一组合精准命中校准式方法的软肋。
- 位置-速度反相关分解让 commit/abstain 对称优雅。
- α 跨组合原样迁移，直接解决真实部署中的迁移痛点。

### 重点关注
- 信号级零假设与答案级正确性之间的 gap 如何解释。
- 下注比例 λ 的自适应策略（aGRAPA 等）对实际功率的影响。

### 可借鉴点
- 用 e-process 把"阈值选择"重构为"证据累积"，是一种可推广到其他自适应决策场景的建模思路。
- 双流反相关构造可作为"对称二元决策"的通用模板。

### 批判性思考
- 保证的"信号级"性质对普通用户是否足够直观？是否存在被误解为"答案级正确率保证"的风险？
- 当信号本身是 adversarial 或非平稳时（如模型中途换风格），零假设是否仍成立？

## 相关论文
- [[Characterising_and_Analysing_Retrieval-Augmented_Generation_Techniques|Characterising RAG Techniques]]
- [[Think_More_or_Read_More_A_Token-Budget_Theory_of_Reasoning_with_Shared_Context|token-budget 理论]] - 推理预算的互补理论视角

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.25212)
- [PDF](https://arxiv.org/pdf/2610.25212)

> [!tip] 关键启示
> 自适应推理的停止问题本质上是"序贯证据累积"而非"阈值校准"——用 e-process 可以在免校准的前提下获得 anytime-valid 的误报控制，且保证天然跨模型/数据集/预算迁移。

> [!warning] 注意事项
> - 保证是"信号级"的，不等同于"答案正确率"的误差控制，部署时需向用户澄清两者区别。
> - 框架假设信号在 chunk 级别可用，信号采集本身的开销需纳入整体预算。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读！为推理早停提供了优雅且严格的免校准框架，是 test-time scaling 效率方向的重要进展。
