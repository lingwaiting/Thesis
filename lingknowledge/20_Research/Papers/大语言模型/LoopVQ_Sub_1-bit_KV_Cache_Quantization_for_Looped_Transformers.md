---
date: "2026-11-05"
paper_id: "arXiv:2611.05304"
title: "LoopVQ: Sub 1-bit KV Cache Quantization for Looped Transformers"
authors: "Minsoo Cheong, Sungjoo Yoo"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - KV-Cache-Quantization
  - Looped-Transformer
  - Vector-Quantization
  - LLM-Inference
quality_score: "9.0/10"
created: "2026-11-05"
updated: "2026-11-05"
status: analyzed
---

# LoopVQ: Sub 1-bit KV Cache Quantization for Looped Transformers

## 核心信息
- **论文ID**：arXiv:2611.05304
- **作者**：Minsoo Cheong, Sungjoo Yoo
- **机构**：Seoul National University（首尔国立大学）
- **发布时间**：2026-11-03
- **会议/期刊**：MLSys 2025 投稿格式
- **链接**：[arXiv](http://arxiv.org/abs/2611.05304) | [PDF](https://arxiv.org/pdf/2611.05304)
- **分类**：cs.LG / cs.AI / cs.CL

## 摘要翻译

### 英文摘要
Looped transformers are a parameter-efficient alternative to standard transformers for language modeling. By reapplying the same layers across multiple loop steps, recent looped language models match the performance of non-looped models several times their size. While this architecture effectively reduces parameter memory, each loop step still stores its own keys and values, so the key-value (KV) cache grows linearly with the number of loop steps and becomes the main memory bottleneck. We observe that the KV cache of looped models is highly redundant along the loop axis and that the loop steps differ substantially in their sensitivity to quantization. Based on these observations, we propose **LoopVQ**, a KV cache vector quantization method for looped transformers that codes channels of multiple loop steps jointly, and fine-tunes the codebooks to match the output of the full-precision model. At 0.5 bits per element, LoopVQ outperforms existing KV cache vector quantization methods for standard LLMs on general, long chain-of-thought reasoning, and long-context retrieval tasks with Ouro and Huginn models. Its design is orthogonal to these methods, and combined with TaSQ, it retains much of the BF16 accuracy even at 0.25 bits per element. We also implement LoopVQ in SGLang, where it nearly halves the decode latency at a 32K-token context and raises the peak serving throughput by up to 4.3× and 6.1× on the 1.4B and 2.6B Ouro models compared to the BF16 baseline.

### 中文翻译
循环 Transformer（looped transformer）是语言建模中一种参数高效的替代架构，通过在多个循环步（loop step）中重复使用同一组层，近期的循环语言模型能以更小的参数量追平数倍于自身规模的普通模型。虽然这种架构有效降低了参数内存，但每个循环步仍要各自存储 key 和 value，因此 KV 缓存随循环步数线性增长，成为主要的内存瓶颈。作者观察到：循环模型的 KV 缓存在"循环轴"上高度冗余，且不同循环步对量化的敏感度差异显著。基于这两个观察，作者提出 **LoopVQ**——一种面向循环 Transformer 的 KV 缓存向量量化方法，它联合编码多个循环步的通道，并对码本做微调以逼近全精度模型的输出。在每元素 0.5 bit 下，LoopVQ 在通用、长思维链推理、长上下文检索任务上均优于面向普通 LLM 的现有 KV 缓存向量量化方法（Ouro 与 Huginn 模型）。其设计与这些方法正交，与 TaSQ 结合后甚至在每元素 0.25 bit 下仍保留大部分 BF16 精度。作者还在 SGLang 中实现了 LoopVQ，在 32K token 上下文下几乎将解码延迟减半，峰值服务吞吐在 1.4B 与 2.6B Ouro 模型上分别提升至多 4.3× 和 6.1×（相比 BF16 基线）。

### 核心要点提炼
- **研究背景**：循环 Transformer 用层复用换取参数效率，但 KV 缓存随循环步数线性膨胀，成为新的内存瓶颈。
- **研究动机**：现有 KV 量化方法面向标准 LLM，未利用循环模型"沿循环轴冗余 + 步间敏感度不均"这两个特有结构。
- **核心方法**：将多个循环步的通道联合编码成单个码本索引（loop-axis vector quantization），并用输出蒸馏式 KL 微调码本。
- **主要结果**：0.5 bit 下全面优于现有方法，0.25 bit 下结合 TaSQ 仍保住大部分精度；SGLang 实现将解码延迟近半、吞吐最高 6.1×。
- **研究意义**：把 KV 量化从"标准 LLM"推进到"循环 LLM"这一新兴高效架构，并给出了系统级实现。

## 研究背景与动机

### 领域现状
参数高效架构（尤其循环/深度循环 Transformer，如 Ouro、Huginn）正成为在有限显存下扩大有效模型容量的一条路线。但这类模型的核心矛盾是：**参数内存降下来了，激活/KV 内存反而升上去了**——因为每个循环步都要保存一套 KV。标准 KV 缓存量化（如 KIVI、RotateKV、TaSQ 等）主要面向非循环 LLM 设计，直接迁移无法利用循环结构的冗余性。

### 现有方法的局限性
- 现有 KV 量化把每个循环步当作独立的 KV 张量处理，忽略了沿循环轴的强相关性。
- 对"不同循环步对量化误差的敏感度不同"缺乏建模。
- Fisher 加权 k-means 等目标独立惩罚每个向量误差，无法捕捉跨循环步误差的传播与叠加。

### 研究动机
利用循环模型 KV 缓存的**循环轴冗余**与**步间敏感度不均**两个结构特性，设计更激进的亚 1-bit 量化方案，从根本上缓解循环 LLM 的 KV 内存瓶颈。

## 研究问题

### 核心研究问题
如何为循环 Transformer 设计一种亚 1-bit（≤0.5 bit/元素）的 KV 缓存向量量化方法，在显著压缩 KV 内存的同时保持输出精度，并给出可部署的系统实现？

## 方法概述

### 核心思想
把一个 token 在**多个循环步**上的 KV 通道"打包"成一个耦合向量，用单个码本索引统一编码，从而让一份索引服务多个循环步，位宽摊薄到 $b/G$ bit/元素；再用输出蒸馏式微调修正码本，补偿步间敏感度差异与误差传播。

### 方法框架

#### 整体架构
LoopVQ 由两阶段组成：(1) 循环轴向量量化（把 $T$ 个循环步划分为 $T_c$ 步的块，跨步联合编码通道）；(2) 循环感知的码本微调（以冻结权重、最小化量化模型与全精度模型输出分布 KL 为目标）。

![[2611.05304_fig1.png|800]]

> 图1：LoopVQ 方法总览。展示循环轴上的 KV 冗余、跨步联合编码（coupling）以及码本微调流程。

#### 各模块详细说明

**模块1：循环轴向量量化（Loop-axis Vector Quantization）**
- **功能**：将 $T$ 个循环步按 $T_c$ 连续步分块，块内同一 token 的 $c=G/T_c$ 个通道在 $T_c$ 步上组成耦合向量 $z_j$，用最近邻码本条目编码。
- **输入**：各循环步的 key/value 张量。
- **输出**：每 token 的码本索引（一份索引服务 $T_c$ 步）。
- **处理流程**：
  1. 按块 $\beta$ 划分循环步。
  2. 对每个 (层、头、通道组) 构建耦合向量。
  3. Fisher 加权 k-means 初始化码本（权重 $\omega_n=\|\partial\mathcal{L}/\partial z_n\|_2^2$ 对覆盖的 $T_c$ 步梯度求和）。
- **关键技术**：key 在 RoPE 之前量化，注意力对重构 key 施加 RoPE，与现有 KV 向量量化一致。
- **数学公式**：耦合向量 $[z_j^{(\beta,\ell,h,g)}]_{iT_c+m} = x^{(\beta T_c+m+1,\ell)}_{j,h,gc+i}$；解码 $\hat x = [e_{a(z_j)}]_{iT_c+m}$。

**模块2：循环感知码本微调（Loop-aware Codebook Fine-tuning）**
- **功能**：冻结模型权重，最小化量化缓存下输出分布与全精度输出分布的 KL 散度，修正码本。
- **输入**：校准序列的全精度 KV 缓存 $C$ 及其输出分布 $p_j$。
- **输出**：微调后的码本 $\mathcal{B}$。
- **处理流程**：
  1. 用当前码本前向，得到量化缓存 $\hat C(\mathcal{B})$ 与输出 $q_j(\mathcal{B})$。
  2. 目标 $\mathcal{J}(\mathcal{B}) = \mathbb{E}_s[\frac{1}{N}\sum_j \mathrm{KL}(p_j \| q_j(\mathcal{B}))]$。
  3. 每次迭代重算赋值（assignments），保证码本在推理所用的编码器下训练。
- **关键技术**：知识蒸馏式端到端微调；在 Ouro-1.4B 上把 KL 散度约减半。

**模块3：SGLang 系统实现（Serving）**
- **功能**：在 SGLang 中落地 LoopVQ 注意力核。
- **输入**：请求的 KV 缓存（sink + 最近 token 保持 bf16，其余存为共享码本索引）。
- **输出**：解码注意力输出。
- **处理流程**：
  1. 缓存分页（每页 8 token），sink $n_s$ 与最近 $n_r$ token 存 bf16，其余量化为索引。
  2. 每循环步加载该步码本切片到共享内存，从寄存器中的索引查 key/value。
  3. 每生成 8 个 token 后，将最旧 recent page 编码为索引。
- **关键技术**：一份索引跨 $T_c$ 步共享，量化的每 token 内存与同 $G$ 下单独编码各步相同。

## 实验结果

### 实验目标
验证 LoopVQ 在通用、长思维链推理、长上下文检索三类任务上相对现有 KV 量化方法的精度优势，以及在 SGLang 中的延迟/吞吐收益。

### 数据集与模型
- 模型：Ouro-1.4B、Ouro-2.6B、Huginn。
- 任务：通用 LM、长思维链推理（long CoT）、长上下文检索（如 NIAH、LongBench、RULER）。

### 主要结果

#### 主实验结果
- **0.5 bit/元素**：LoopVQ 在三类任务上均优于面向标准 LLM 的现有 KV 向量量化方法。
- **0.25 bit/元素**：与 TaSQ 正交组合后，仍保留大部分 BF16 精度。
- **系统收益**：32K 上下文下解码延迟近减半；峰值吞吐在 1.4B/2.6B Ouro 上分别提升至多 **4.3× / 6.1×**（相对 BF16 基线）。

#### 结果分析
核心增益来自两点：一是沿循环轴的联合编码把冗余"折叠"掉，二是输出蒸馏式微调补偿了 Fisher k-means 无法捕捉的跨步误差传播。两个机制的叠加让 LoopVQ 在极低位宽下仍保持接近全精度的输出分布。

### 实验结果图

![[2611.05304_fig6.png|800]]

> 图2：吞吐/延迟效率对比（efficiency_throughput / efficiency_latency）。

![[2611.05304_fig5.png|800]]

> 图3：长上下文检索（NIAH）结果。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：提出"循环轴联合量化"这一新的结构化量化范式，把 KV 量化的粒度从"单步张量"扩展到"跨步耦合向量"。
  - 创新点：利用循环 Transformer 沿循环轴的强相关性，使位宽可摊薄至 $b/G$ bit/元素。
- **贡献2**：指出 Fisher 加权 k-means 无法建模跨步误差传播与步间敏感度不均，并提出输出蒸馏式码本微调作为补救。
  - 影响范围：对循环/深度循环 LLM 的推理系统设计有直接指导意义。

#### 实际应用价值
- **应用场景1：循环 LLM 的长上下文推理部署**——KV 内存是首要瓶颈，LoopVQ 可将内存降到亚 1-bit 级别，直接提升单卡可服务的上下文长度与并发。
- **应用场景2：边缘/低成本推理**——参数高效 + KV 高效的组合，适合显存受限场景。

#### 领域影响
- **短期影响**：为循环 LLM（Ouro、Huginn 系）提供现成的 KV 量化方案与 SGLang 插件。
- **中期影响**：推动"量化方案需匹配架构结构"这一理念，催生更多架构感知的推理优化。
- **长期影响**：与 TaSQ 等正交方法组合，或推动亚 1-bit KV 量化成为循环 LLM 默认配置。

### 方法优势详解
- **优势1：循环轴冗余利用**——一份索引服务 $T_c$ 个循环步，是相对标准 LLM 方法独有的增益来源。
- **优势2：正交可组合**——与 TaSQ 等现有方法正交，组合后 0.25 bit 仍保住精度。
- **优势3：端到端系统落地**——不是纯理论，给出了 SGLang 内核与实测延迟/吞吐提升。

### 局限性分析
- **局限1：绑定循环架构**——方法强依赖循环/深度循环结构，对标准 LLM 无直接增益。
- **局限2：码本微调需校准数据**——依赖校准序列做输出蒸馏，校准分布偏移可能影响泛化。
- **局限3：实现复杂度**——跨步联合编码改变了缓存布局与编码时机，系统集成成本较高。

### 适用性与场景分析
- **适用场景**：循环/深度循环 LLM 的长上下文推理、高并发服务、显存受限部署。
- **不适用场景**：标准非循环 Transformer；对校准数据高度敏感的任务。

## 与相关论文对比

### 对比论文选择依据
选择 KV 缓存量化的代表性工作，以及循环 Transformer 相关工作作为参照系。

### [[KIVI]] / [[RotateKV]] / [[TaSQ]] - 标准 LLM 的 KV 量化

| 对比维度 | 现有方法（KIVI/RotateKV/TaSQ） | 本文方法（LoopVQ） |
|----------|-------------------------------|--------------------|
| 目标架构 | 标准 LLM | 循环/深度循环 LLM |
| 量化粒度 | 单步 KV 张量 | 跨循环步耦合向量 |
| 位宽 | 2-bit 级为主 | 亚 1-bit（0.25–0.5） |
| 结构利用 | 通道/异常值 | 循环轴冗余 + 步间敏感度 |
| 系统实现 | 部分有 | SGLang 内核，延迟/吞吐实测 |

### 对比总结
LoopVQ 并非与 KIVI/RotateKV/TaSQ 直接竞争，而是**互补**——它专门面向循环架构，且可与 TaSQ 组合。其创新点在"架构感知"而非"更激进的通用量化技巧"。

## 技术路线定位

### 所属技术路线
属于**循环/深度循环 Transformer + KV 缓存量化**的交叉路线，核心特点是"让量化方案适配架构结构"。

### 技术路线发展历程
```
标准 LLM KV 量化(KIVI/RotateKV/TaSQ) → 循环 LLM 兴起(Ouro/Huginn) → 架构感知 KV 量化(LoopVQ) → 亚 1-bit 组合方案
```

### 本文在技术路线中的位置
- **承上**：继承向量量化 + 输出蒸馏微调的技术积累。
- **启下**：示范了"结构冗余 → 更低位宽"的路径，为循环架构的其他激活/缓存优化铺路。

## 未来工作建议

### 作者建议的未来工作
1. 将 LoopVQ 思路扩展到其他循环架构与训练阶段的量化。
2. 探索与更多正交方法（旋转、混合精度等）的组合。

### 基于分析的未来方向
1. **方向1：训练阶段 KV 感知量化**——把量化误差纳入训练目标，可能进一步提升极限位宽。
2. **方向2：跨步耦合的硬件协同设计**——针对共享索引的访问模式设计专用硬件。

## 我的综合评价

### 价值评分

#### 总体评分
**9.0/10** - 针对新兴循环 LLM 的 KV 内存瓶颈，提出架构感知的亚 1-bit 量化方案，且有系统级落地与实测，工程与理论价值兼备。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 循环轴联合编码 + 输出蒸馏微调，针对性强但非颠覆性 |
| 技术质量 | 9/10 | 理论推导清晰，系统实现完整 |
| 实验充分性 | 9/10 | 三类任务 + 多模型 + 系统实测 |
| 写作质量 | 9/10 | 结构清晰，动机-方法-系统-实验完整 |
| 实用性 | 9/10 | 直接可部署（SGLang），显存收益显著 |

### 重点关注
- 循环轴冗余的量化利用思路。
- 输出蒸馏式码本微调如何补偿跨步误差。

## 相关论文
- [[KIVI]] - 2-bit KV 缓存量化（逐通道）
- [[RotateKV]] - 旋转辅助 KV 量化
- [[TaSQ]] - 与 LoopVQ 正交组合的量化方法

## 外部资源
- [arXiv](http://arxiv.org/abs/2611.05304)

> [!tip] 关键启示
> 量化方案应该"适配架构结构"——循环 Transformer 的循环轴冗余让亚 1-bit KV 量化成为可能，这比在标准 LLM 上继续压榨通用技巧更有增益空间。

> [!warning] 注意事项
> - 方法绑定循环架构，标准 LLM 不适用。
> - 码本微调依赖校准数据分布。
> - 系统集成（缓存布局/编码时机）复杂度较高。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读！这是循环 LLM 时代 KV 量化方向的开创性工作，兼具理论与系统价值。
