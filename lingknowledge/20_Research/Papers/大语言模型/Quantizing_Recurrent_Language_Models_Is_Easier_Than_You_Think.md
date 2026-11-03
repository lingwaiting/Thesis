---
date: "2026-11-03"
paper_id: "arXiv:2611.01864"
title: "Quantizing Recurrent Language Models Is Easier Than You Think"
authors: "Philipp Nazari, Neehal Tumma, Daniela Rus, T. Konstantin Rusch"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - 量化
  - 循环模型
  - 状态量化
quality_score: "8.6/10"
related_papers: []
created: "2026-11-03"
updated: "2026-11-03"
status: analyzed
---

# Quantizing Recurrent Language Models Is Easier Than You Think

## 核心信息
- **论文ID**：arXiv:2611.01864
- **作者**：Philipp Nazari, Neehal Tumma, Daniela Rus, T. Konstantin Rusch
- **机构**：MIT CSAIL（Daniela Rus、T. Konstantin Rusch 均为 MIT 成员）
- **发布时间**：2026-11-01
- **会议/期刊**：cs.LG / cs.AI / cs.CL（预印本）
- **链接**：[arXiv](https://arxiv.org/abs/2611.01864) | [PDF](https://arxiv.org/pdf/2611.01864)
- **引用**：--

## 摘要翻译

### 英文摘要
Linear recurrences are becoming an increasingly common building block in large language models, replacing the growing KV cache of softmax attention with a fixed-size recurrent state that enables efficient decoding. However, the quantization stack has largely been developed around softmax Transformers, whose numerical properties and inference state differ from those of recurrent models. Our goal is to understand these differences and study the design space of quantization methods for recurrent models. ...

### 中文翻译
线性循环（linear recurrence）正日益成为大语言模型的常见基础构件，用固定大小的循环状态取代不断增长的 KV cache，从而实现高效解码。然而，现有的量化技术栈主要围绕 softmax Transformer 开发，其数值特性和推理状态与循环模型存在差异。本文的目标是理解这些差异，并研究循环模型量化方法的设计空间。

### 核心要点提炼
- **研究背景**：线性循环模型（如 Mamba、RWKV 等）正在替代 softmax 注意力以降低解码时的 KV cache 开销，但量化方案仍停留在为 Transformer 设计的框架上。
- **研究动机**：循环模型的数值特性（衰减循环、固定状态）与 softmax 注意力不同，直接套用现有量化方法会导致精度损失。
- **核心方法**：提出 LoReS 状态量化管线，区分"状态外量化"（extra-state）与"状态内量化"（intra-state），结合 value-axis 尺度分组、基于衰减的混合精度、随机舍入三大技术。
- **主要结果**：在 Qwen-3.5 9B、Nemotron-H 8B、Kimi-Linear 48B-A3B 上实现稳健的 8-bit 状态量化，并在更短解码区间上达到更低的等效精度；在 vLLM 中实现端到端解码加速。
- **研究意义**：首次系统性地刻画循环模型量化的设计空间，证明衰减循环天然比 softmax 注意力更易量化（残差离群通道更弱）。

## 研究背景与动机

### 领域现状
线性循环模型通过把每个 token 的 softmax 注意力 KV cache 压缩为一个固定大小的循环状态，显著降低了长序列解码的内存与计算开销。这类模型（包括 Mamba、RWKV、以及 Kimi-Linear 等工业级模型）正成为大模型推理优化的热点方向。

### 现有方法的局限性
量化是推理优化的关键手段（INT8/INT4 权重与激活量化），但几乎所有成熟的量化工具（GPTQ、AWQ、SmoothQuant 等）都围绕 softmax Transformer 设计：
- softmax 注意力会形成强烈的残差离群通道（outlier channels），需要 incoherence processing（如旋转、逐通道缩放）来缓解量化误差；
- KV cache 量化和循环状态量化是两个不同的数值问题，现有方法没有区分。

### 研究动机
由于循环模型与 softmax Transformer 的数值分布、推理状态都不同，直接复用 Transformer 的量化栈可能既低效又不准确。本文旨在回答：**循环模型的量化设计空间是什么？它与 Transformer 量化有何本质区别？**

## 研究问题

### 核心研究问题
1. 循环模型的"状态外"（权重与激活，受序列混合器间接影响）与"状态内"（解码时的循环状态本身）量化有何不同特性？
2. 衰减循环是否比 softmax 注意力更易量化？离群通道的差异从何而来？
3. 如何设计一个稳健的循环模型状态量化管线，在真实规模模型（8B–48B）上取得实际加速？

## 方法概述

### 核心思想
把循环模型量化拆解为两个目标，并针对每个目标设计专门技术：
- **状态外量化（extra-state）**：量化循环状态之外的权重与激活。发现衰减循环产生的残差离群通道比 softmax 注意力弱，因此无需 incoherence 处理即可量化。
- **状态内量化（intra-state）**：量化解码过程中循环状态本身。反复的状态更新带来不同于 KV cache 量化的数值问题（误差累积、重量化）。

在此基础上提出 **LoReS** 状态量化管线，包含三项技术：
1. **Value-axis 尺度分组**：按输出通道幅值对齐尺度，匹配不同通道的动态范围；
2. **基于衰减的混合精度**：对衰减缓慢的头/通道保留更高精度，保护长时记忆；
3. **随机舍入**：在反复重量化下限制误差累积。

### 方法框架

![[2611.01864_fig1.png|600]]

> 图1：LoReS 状态量化管线概览，区分状态外量化与状态内量化两条路径。

#### 各模块详细说明

**模块1：状态外量化分析**
- **功能**：量化权重与激活（循环状态之外的张量）。
- **关键发现**：在受控的 1.3B 模型中，衰减循环的残差离群通道显著弱于 softmax 注意力，因此可以直接量化而无需 incoherence processing（如旋转矩阵）。

**模块2：状态内量化（LoReS 核心）**
- **功能**：量化解码过程中反复更新的循环状态。
- **关键技术**：
  - value-axis 尺度分组：匹配输出通道幅值；
  - 基于衰减的混合精度：保护缓慢衰减的头/通道；
  - 随机舍入：限制反复重量化下的误差累积。

### 数学公式（Markdown LaTeX）

循环状态更新可写为：

$$h_t = A h_{t-1} + B x_t$$

其中 $A$ 为对角衰减矩阵（decay），$B x_t$ 为输入投影。量化后的状态 $\hat{h}_t$ 在每次更新后需重新量化：

$$\hat{h}_t = Q\big(A \hat{h}_{t-1} + B x_t\big)$$

反复的 $Q(\cdot)$ 是误差累积的来源，随机舍入（stochastic rounding）通过无偏性限制其漂移。

## 实验结果

### 实验设置
- **模型**：Qwen-3.5 9B、Nemotron-H 8B、Kimi-Linear 48B-A3B（覆盖主流循环架构）。
- **实现**：在 vLLM 中集成，评估端到端解码速度。
- **目标**：8-bit 状态量化稳健性 + 更短解码区间上的更低等效精度。

### 主要结果
- 在三个模型上均实现**稳健的 8-bit 状态量化**，未出现显著精度崩溃；
- 在更短解码区间上可达到**更低的等效精度**（低于 8-bit 的有效位宽）；
- 在 vLLM 中展示端到端解码加速；
- 受控实验证实：衰减循环的残差离群通道弱于 softmax 注意力，因而更易量化。

### 消融分析
论文区分了状态外与状态内两类量化目标，分别验证其数值特性；LoReS 的三项技术（尺度分组 / 混合精度 / 随机舍入）各自针对状态内量化的误差来源。

## 深度分析

### 研究价值评估

#### 理论贡献
- 首次系统刻画循环模型量化的设计空间，区分"状态外"与"状态内"两类不同性质的量化问题。
- 揭示衰减循环与 softmax 注意力在离群通道特性上的本质差异，为循环模型专用量化提供理论依据。

#### 实际应用价值
- 为 Mamba / RWKV / Kimi-Linear 等线性循环模型的推理部署提供可直接落地的量化方案；
- vLLM 集成表明其工程可落地性，对长上下文、低延迟推理场景有直接价值。

### 方法优势详解
- **无需 incoherence processing**：相比 Transformer 量化的旋转/逐通道缩放，循环模型状态外量化更简单、开销更低。
- **混合精度保护长时记忆**：针对衰减缓慢通道保留高精度，避免长程依赖信息丢失。

### 局限性分析
- 实验主要覆盖 8-bit 状态量化，更低精度（4-bit）下的稳健性未充分验证；
- 短解码区间上的"更低等效精度"收益需更多端到端基准佐证；
- 未与最新的 Transformer 低比特量化（如 QuaRot、SpinQuant）做严格对照。

### 适用性与场景分析
- **适用场景**：长上下文推理、KV cache 受限的部署环境、需要低延迟解码的循环模型服务。
- **不适用场景**：仍以 softmax 注意力为主的模型架构、需要极低比特（<4-bit）量化的场景。

## 技术路线定位
本文属于**循环模型推理优化**技术路线，核心特点：
- 从 Transformer 量化的通用框架转向循环模型专用设计；
- 结合数值分析（离群通道、衰减特性）与工程实现（vLLM）。

发展脉络：Mamba/RWKV 线性注意力 → 循环模型推理优化 → 本文（循环模型专用量化 LoReS）→ 未来（更低比特、更多架构覆盖）。

## 未来工作建议
1. 探索 4-bit 及以下精度下的状态量化稳健性；
2. 将 LoReS 拓展到更多循环架构（gated delta rule、混合注意力-循环模型）；
3. 与 transformer 低比特量化方法做严格对照实验。

## 我的综合评价

### 价值评分
**8.6/10** - 选题切中"循环模型崛起但量化栈滞后"的真实痛点，理论洞察（离群通道差异）与方法贡献（LoReS 三技术）扎实，在真实规模模型上验证，工程可落地。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 首次系统区分状态外/内量化，洞察清晰 |
| 技术质量 | 9/10 | 受控实验 + 三模型验证，方法严谨 |
| 实验充分性 | 8/10 | 覆盖主流循环模型，但低比特不足 |
| 写作质量 | 9/10 | 结构清晰、动机明确 |
| 实用性 | 9/10 | vLLM 集成，直接可用 |

## 我的笔记

%% 用户可以在这里添加个人阅读笔记 %%

## 相关论文

### 背景相关
- [[RWKV|RWKV]] - 线性循环语言模型的代表架构
- [[Mamba|Mamba]] - 状态空间模型，循环状态解码

> [!tip] 关键启示
> 循环模型天然比 softmax 注意力更易量化——衰减循环的残差离群通道更弱，这为专用量化栈提供了理论依据。

> [!warning] 注意事项
> - 状态内量化（反复更新）与 KV cache 量化是不同问题，不能混用；
> - 低比特（<4-bit）量化仍有待验证。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐。循环模型推理优化的必读之作，理论与工程兼顾。
