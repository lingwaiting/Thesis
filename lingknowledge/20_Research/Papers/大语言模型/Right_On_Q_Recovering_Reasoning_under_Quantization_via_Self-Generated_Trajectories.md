---
date: "2026-10-22"
paper_id: "arXiv:2610.29378"
title: "Right On Q: Recovering Reasoning under Quantization via Self-Generated Trajectories"
authors: "Boyuan Zheng, Jing Xiong, Chenwei Wu, Hui Shen, Zesen Zhao, Hei Ting Una Chan, Xueshen Liu, Zhen Zhang, Liyue Shen, Zechun Liu, Z. Morley Mao"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 量化
  - Quantization
  - QAT
  - 蒸馏
  - On-Policy
  - 推理
quality_score: "8.5/10"
related_papers: []
created: "2026-10-22"
updated: "2026-10-22"
status: analyzed
---

# Right On Q: Recovering Reasoning under Quantization via Self-Generated Trajectories

## 核心信息
- **论文ID**：arXiv:2610.29378
- **作者**：Boyuan Zheng, Jing Xiong, Chenwei Wu, Hui Shen, Zesen Zhao, Hei Ting Una Chan, Xueshen Liu, Zhen Zhang, Liyue Shen, Zechun Liu, Z. Morley Mao
- **机构**：密歇根大学安娜堡分校 / 加州大学圣塔芭芭拉分校 / Meta AI
- **发布时间**：2026-10-21
- **会议/期刊**：arXiv（cs.CL / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.29378) | [PDF](https://arxiv.org/pdf/2610.29378)
- **引用**：--

## 摘要翻译

### 英文摘要
Quantization-aware training (QAT) aims to recover reasoning capabilities in low-bit large language models (LLMs). Existing recipes typically supervise the quantized student with the original full-precision model on fixed response sequences. However, the resulting low-bit quantized models still struggle on long-horizon reasoning tasks and often fail to terminate within a token budget that suffices for the full-precision model. We identify two challenges in closing this gap. First, fixed-response supervision leaves a mismatch between training prefixes and those encountered during student generation, and this trajectory divergence grows as responses lengthen. Second, on its own prefixes, the student underpredicts some next-token candidates and overpredicts others, and this distributional misalignment becomes more frequent at high-entropy prefixes. We present OnQ, a low-bit recovery pipeline that combines on-policy distillation and candidate-wise Dynamic KL. Starting from block-wise calibration, the quantized student generates responses and receives full-precision teacher supervision on its own prefixes. Dynamic KL weights forward and reverse KL by each candidate's teacher–student probability ratio to balance correction of under- and overprediction. Across Qwen3-1.7B, 4B, and 8B at 2- and 3-bit precision, OnQ achieves the highest nine-benchmark average, with the largest gains on long-horizon reasoning benchmarks. At 2 bits, it outperforms every baseline on every benchmark at every model size, improving the Qwen3-8B average by 10.26 points over the strongest baseline. It also produces shorter responses with fewer truncations and repetitive errors.

### 中文翻译
量化感知训练（QAT）旨在恢复低比特大语言模型的推理能力。现有方法通常用原始全精度模型在**固定响应序列**上监督量化学生模型。然而，得到的低比特量化模型在长程推理任务上仍表现挣扎，且常常在全精度模型能完成的 token 预算内无法终止。作者识别出弥合这一差距的两大挑战。第一，固定响应监督使训练前缀与学生生成时遇到的前缀之间存在错配，这种"轨迹发散"随响应变长而加剧。第二，在学生自己的前缀上，学生会**低估**某些下一 token 候选、同时**高估**另一些，这种"分布错配"在高熵前缀处更频繁。作者提出 OnQ——结合**在线蒸馏（on-policy distillation）**与**候选级动态 KL（candidate-wise Dynamic KL）**的低比特恢复流水线。从逐块校准出发，量化学生生成响应，并接受全精度教师在其**自己的前缀**上的监督。动态 KL 按每个候选的"教师–学生概率比"来加权前向与反向 KL，以平衡对低估与高估的修正。在 Qwen3-1.7B、4B、8B 的 2-bit 与 3-bit 精度上，OnQ 在全部六种设置中取得最高的九基准平均分，且在长程推理基准上增益最大。2-bit 时，它在每个规模、每个基准上都超越所有基线，将 Qwen3-8B 平均分相对最强基线提升 10.26 分。它还产生更短的响应、更少的截断与重复错误。

### 核心要点提炼
- **研究背景**：低比特量化降低显存占用，但恢复推理能力仍困难，QAT 是关键手段。
- **研究动机**：现有 QAT 用固定响应蒸馏，忽略了量化改变生成分布导致的"轨迹发散"与"分布错配"两大问题。
- **核心方法**：OnQ = 逐块校准 → 在线蒸馏（学生自生成轨迹上接受教师监督）→ 候选级动态 KL（按教师/学生概率比加权 FKL/RKL）。
- **主要结果**：六种模型/比特设置全部取得最高九基准平均分；2-bit 时全基准全规模最优；生成更短、截断与重复错误更少。

## 研究背景与动机

### 领域现状
低比特量化是 LLM 部署的关键技术，但激进的量化（如 2-bit）会显著损害推理能力，甚至使模型在长程推理上"想得更久却无法终止"（Lotfi et al. 2026）。量化感知训练（QAT）通过在量化前向传播中适配参数来恢复精度，通常以全精度模型为教师进行蒸馏（EfficientQAT、BitDistiller、ReasoningQAT、LLM-QAT 等）。这些方法几乎都训练在**固定响应序列**上。

### 现有方法的局限性
作者通过分析 Qwen3-1.7B 的 2-bit 模型（图1）识别出两大挑战：
1. **轨迹发散（trajectory divergence）**：固定响应蒸馏只监督"参考前缀"，但量化改变了学生的生成方式——响应越长，学生越依赖自己的输出，其上下文偏离参考前缀越远。长程任务上 QAT 基线恢复很少、且频繁触顶（无法在预算内终止）。
2. **分布错配（distributional misalignment）**：在学生自己的前缀上，校准后的学生呈现**双侧**概率错误（同时低估某些候选、高估另一些），且在高熵前缀处更频繁。

### 研究动机
固定响应监督无法覆盖学生实际访问的轨迹；而训练在学生自己的前缀上又暴露了双侧分布错配。因此需要：(1) 把监督放到学生自生成的轨迹上（在线蒸馏）；(2) 用能同时修正低估与高估的目标（候选级动态 KL）。

## 研究问题

### 核心研究问题
如何在激进低比特量化下，恢复 LLM 的长程推理能力？具体而言，如何同时解决"轨迹发散"与"分布错配"两大挑战？

## 方法概述

### 核心思想
让量化学生"从自己的错误中学习"：学生通过量化前向自生成响应，全精度教师在这些**学生自己的前缀**上提供下一 token 分布；再用候选级动态 KL 按"教师–学生概率比"加权前向/反向 KL，平衡对低估与高估候选的修正。

![[2610.29378_page2.png|600]]

> 图1：低比特固定响应 QAT 在长程推理上的失败（a 精度、b 截断、c 预测错误），以及 OnQ 的改进（图2 为方法概览）。

![[2610.29378_page3.png|600]]

> 图2：OnQ 概览。左：在线蒸馏在自生成前缀上训练校准后的学生；右：动态 KL 按教师–学生概率比加权前向/反向 KL。

### 方法框架

#### 整体架构
OnQ 分两阶段：
1. **逐块校准（block-wise calibration）**：用 Block-AP 逐块最小化重建误差（MSE），为在线训练提供起点。
2. **在线蒸馏 + 动态 KL（OPD + DKL）**：学生采样响应，教师提供同前缀的下一 token 分布，用候选级动态 KL 对齐。

#### 各模块详细说明

**模块1：量化与初始化**
- **功能**：均匀非对称权重量化（group size 128，BF16 激活），RTN 初始化 + 逐块校准。
- **公式**：`w_int = clamp(⌊w/s⌉+z, 0, 2^N−1)`，`ŵ = s(w_int − z)`。
- **关键技术**：同时更新 latent 权重 W 与 scale s（Learned Step Size）。

**模块2：在线蒸馏（On-Policy Distillation）**
- **功能**：在学生的自生成前缀上接受教师监督，缓解轨迹发散。
- **处理流程**：每个 step 采样 B 个 prompt，学生自生成响应 y，教师提供 `P(v|h)`（h = (x, y<t)）；冻结 rollout 策略与教师目标来更新学生。
- **关键技术**：借鉴 OPD / GKD / exposure-bias 缓解思想（Agarwal et al. 2024；Lu & Thinking Machines Lab 2025）。

**模块3：候选级动态 KL（Dynamic KL）**
- **功能**：按每个候选的教师/学生概率比加权 FKL 与 RKL，平衡低估/高估修正。
- **公式**：`D_dyn(h) = Σ_v [ α_v·p_v·log(p_v/q_v) + (1−α_v)·q_v·log(q_v/p_v) ]`，其中 `α_v = sg[p_v/(p_v+q_v)]`。
- **规则**：低估（p_v>q_v）→ α_v>1/2，偏向 FKL；高估（q_v>p_v）→ α_v<1/2，偏向 RKL。
- **实现**：教师/学生 top-20 token 并集 + 残差类别 ⊥，ϵ=1e-8 平滑。

**模块4：响应长度调度**
- **功能**：训练初期学生易重复（尤其长响应末尾），用渐进 cap 避免在重复文本上浪费监督。
- **规则**：steps 1–50 cap 4096、51–100 cap 8192、之后 16384。

## 实验结果

### 实验目标
验证 OnQ 在低比特推理恢复上是否优于 PTQ 与 QAT 基线，并分析其生成行为改进。

### 实验设置
- **模型/比特**：Qwen3-1.7B / 4B / 8B × W2 / W3（2/3-bit 权重，BF16 激活）。
- **基线**：PTQ（GPTQ、AutoRound）+ QAT（EfficientQAT、BitDistiller、ReasoningQAT）。
- **基准**：9 个——GSM8K、MATH-500、AIME 24–26（数学）；HumanEval+、MBPP+、LiveCodeBench（代码）；MMLU-Redux、GPQA-Diamond（知识/科学）；IFEval（指令遵循）。
- **训练**：25,600 多域 prompt；OPD 用 verl + vLLM；1.7B 400 步 / 4B/8B 300 步。

### 主要结果
- **全面最优**：六种模型/比特设置全部取得最高九基准平均分，54 个基准-设置对中 48 个最高。
- **2-bit 全胜**：在每个规模、每个基准上超越所有基线。Qwen3-8B W2 平均 70.68，相对最强基线（ReasoningQAT 60.24 / BitDistiller 60.42）提升 **10.26 分**。
- **精度越低增益越大**：2-bit 的领先幅度普遍大于 3-bit；Qwen3-4B W2 的平均分甚至超过 Qwen3-8B 的所有 2-bit 基线（"比自身大一倍的模型"）。
- **长程任务增益最大**：最大领先常落在 AIME 与 LiveCodeBench（长程推理/竞赛编程）。
- **生成更高效**：OnQ 用更少 token 达更高精度；截断率在六种设置中均最低；重复错误从基线的 11.4–54.0% 降到 2.4%（MATH-500）。
- **消融**：固定响应 FKL/DKL vs 在线 DKL（+8.40 分差距）；动态 KL 优于单方向 FKL（GKD）与 RKL（Vanilla OPD）。

## 深度分析

### 研究价值评估

#### 理论贡献
- 明确界定并量化"轨迹发散"与"分布错配"两大低比特推理恢复瓶颈，为后续工作提供了清晰的问题框架。

#### 实际应用价值
- 2-bit 模型能以接近 3-bit 的水平工作，显著降低推理显存成本；方法开源，工程可复现（verl + vLLM 栈）。

### 方法优势详解
- **问题定位精准**：图1 的诊断直接驱动方法设计，动机链条完整。
- **候选级动态 KL 有据**：FKL 擅长拉回严重低估候选（99.7%）、RKL 擅长压住严重高估候选（86.3%），动态 KL 各取所长（95.1%/84.7%）。
- **生成质量改善**：不仅更准，还更短、更少重复——这对实际部署体验很重要。

### 局限性分析
- **算力门槛高**：需全精度教师 + 自生成 rollouts，训练成本高于固定响应 QAT。
- **只覆盖权重量化**：激活仍为 BF16，未探索权重+激活联合低比特。
- **模型家族单一**：仅在 Qwen3 系列验证，跨家族泛化未知。

### 适用性与场景分析
- **适用**：需要激进低比特部署（显存受限）又希望保留推理能力的场景。
- **不适用**：无全精度教师可用、或对训练算力极度敏感的边端场景。

## 技术路线定位

本文属于 **低比特量化 + 蒸馏** 技术路线，核心特点是"把蒸馏监督从固定响应迁移到学生自生成轨迹，并用候选级动态 KL 修正双侧概率错配"。

```
PTQ（GPTQ/AutoRound）→ QAT 固定响应蒸馏（EfficientQAT/BitDistiller/ReasoningQAT）→ OnQ（在线蒸馏 + 动态 KL）→ 未来：W/A 联合低比特、跨家族
```

### 具体子方向
量化感知训练中的"在线/自生成蒸馏（on-policy distillation）"子方向，与 OPD、GKD、ToDi（候选级 KL）等一脉相承。

## 未来工作建议

1. **权重+激活联合低比特**：把 OnQ 扩展到 W/A 联合量化，进一步压缩。
2. **跨模型家族验证**：在 Llama、Mistral、Gemma 等家族上检验普适性。
3. **训练成本优化**：降低在线 rollouts 与教师推理的开销（如教师蒸馏、缓存重用）。

## 我的综合评价

### 总体评分
**8.5/10** —— 问题真实、诊断驱动设计、实验结果全面且显著，是低比特推理恢复方向上有代表性的工作；局限在于算力门槛与单家族验证。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 把在线蒸馏 + 候选级动态 KL 系统化地用于低比特推理恢复 |
| 技术质量 | 9/10 | 诊断→设计→消融链条完整，候选级 DKL 有局部梯度分析支撑 |
| 实验充分性 | 9/10 | 3 规模 × 2 比特 × 9 基准 × 多基线，48/54 最优 |
| 写作质量 | 8/10 | 结构清晰，附录详尽 |
| 实用性 | 8/10 | 2-bit 落地价值高，但训练算力门槛不低 |

## 相关论文

### 直接相关
- EfficientQAT（Chen et al. 2025）；BitDistiller（Du et al. 2024）；ReasoningQAT（Okoshi et al. 2026）
- 在线蒸馏：OPD（Agarwal et al. 2024）；GKD；ToDi（Jung et al. 2025）

### 背景相关
- PTQ：GPTQ（Frantar et al. 2022）；AutoRound（Cheng et al. 2024）
- 量化推理损伤研究：Liu et al. 2025；Lotfi et al. 2026

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.29378)

> [!tip] 关键启示
> 低比特推理恢复的瓶颈不在"量化损失"本身，而在"蒸馏监督与真实生成轨迹脱节"——让量化学生从自己的轨迹上学习，并用候选级动态 KL 修正双侧概率错配，是恢复长程推理的关键。

> [!warning] 注意事项
> - 训练需要全精度教师 + 在线 rollouts，算力门槛高
> - 仅验证权重量化（激活仍 BF16）、仅 Qwen3 家族
> - 跨模型家族与 W/A 联合量化的泛化性未知
