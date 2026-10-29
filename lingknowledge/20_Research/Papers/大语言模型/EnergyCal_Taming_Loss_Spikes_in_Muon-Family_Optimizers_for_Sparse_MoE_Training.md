---
date: "2026-10-29"
paper_id: "arXiv:2610.37062"
title: "EnergyCal: Taming Loss Spikes in Muon-Family Optimizers for Sparse MoE Training"
authors: "Masato Fujitake"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 混合专家
  - MoE
  - 优化器
  - 训练稳定性
  - Muon
quality_score: "7.0/10"
created: "2026-10-29"
updated: "2026-10-29"
status: analyzed
---

# EnergyCal: Taming Loss Spikes in Muon-Family Optimizers for Sparse MoE Training

## 核心信息
- **论文ID**：arXiv:2610.37062
- **作者**：Masato Fujitake
- **机构**：--
- **发布时间**：2026-10-27
- **类别**：cs.LG, cs.AI, cs.CL
- **链接**：[arXiv](https://arxiv.org/abs/2610.37062) | [PDF](https://arxiv.org/pdf/2610.37062)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Sparse mixture-of-experts (MoE) language-model training can exhibit large loss spikes with Muon-family optimizers. We introduce EnergyCal, a lightweight rule that attenuates routed-expert updates according to the normalized squared Frobenius norm of their finite-step Newton-Schulz directions. EnergyCal leaves the spectral transform unchanged and adds no persistent optimizer state. We evaluate it on a 4B-parameter MoE model. Against a development-selected, lower-learning-rate MuonH recipe, EnergyCal-MuonH reduces mean thresholded loss-spike burden by 99.51% and final geometric-mean perplexity by 4.06%, with a modest throughput cost. With plain Muon at matched nominal learning rate and weight decay, the reduction in mean spike burden averages 95.98% across the two tested exponents, accompanied by small endpoint improvements. These findings support stabilization both with and without Hyperball in the evaluated setting.

### 中文翻译
稀疏混合专家（MoE）语言模型训练在使用 Muon 系列优化器时可能出现大幅的损失尖峰（loss spikes）。本文提出 EnergyCal，一个轻量级规则，它根据路由专家更新的有限步 Newton-Schulz 方向的归一化平方 Frobenius 范数来衰减这些更新。EnergyCal 保持谱变换不变，且不增加任何持久的优化器状态。作者在一个 4B 参数的 MoE 模型上评估。相比开发集选出的、使用更低学习率的 MuonH 配方，EnergyCal-MuonH 将平均阈值化损失尖峰负担降低了 99.51%，将最终几何平均困惑度降低了 4.06%，仅带来适度的吞吐量代价。在匹配名义学习率和权重衰减的普通 Muon 下，平均尖峰负担的降低在两个测试指数上平均达到 95.98%，并伴有小幅终点改善。这些发现表明，在评估设定下无论是否使用 Hyperball，该方法都能起到稳定作用。

### 核心要点提炼
- **研究问题**：稀疏 MoE 语言模型在使用 Muon 系列优化器训练时会出现损失尖峰
- **核心方法**：EnergyCal——按路由专家更新方向的归一化平方 Frobenius 范数衰减更新，保持谱变换不变、零额外持久状态
- **关键结果**：4B MoE 上，EnergyCal-MuonH 降低尖峰负担 99.51%、最终几何平均困惑度 4.06%；普通 Muon 下尖峰负担平均降 95.98%
- **特点**：轻量（无持久状态）、谱变换不变、吞吐量代价小

## 研究背景与动机

### 领域现状
Muon 系列优化器（基于 Newton-Schulz 正交化的谱变换优化器）在大模型训练中因其高效性受到关注。稀疏 MoE 由于路由机制的非平稳性，训练更容易出现损失尖峰（loss spikes），是稳定训练的一大挑战。

### 现有方法的局限性
1. **降低学习率**：开发集选出的低学习率 MuonH 配方能缓解尖峰，但牺牲了收敛速度
2. **引入额外状态/超参**：部分稳定化方法需要额外持久状态或复杂超参，工程成本高
3. **缺乏针对 MoE 路由专家的专用处理**：路由专家的更新与稠密层的统计特性不同，需要差异化处理

### 研究动机
在不牺牲谱变换、不引入持久优化器状态的前提下，针对 MoE 路由专家设计一个轻量、易实现的尖峰抑制规则，从而让 Muon 系列优化器在稀疏 MoE 上稳定高效地训练。

## 研究问题

### 核心研究问题
1. 稀疏 MoE 在 Muon 系列优化器下损失尖峰的根源（与路由专家更新有何关系）？
2. 如何设计一个轻量、无持久状态的尖峰抑制规则？
3. EnergyCal 在 MuonH 与普通 Muon 两种设定下的稳定化效果如何？

## 方法概述

### 核心思想
EnergyCal 是一个"能量标定"规则：用路由专家更新方向的归一化平方 Frobenius 范数（一种能量度量）作为衰减因子，对路由专家的更新进行缩放，从而抑制导致尖峰的过大更新，同时保持 Newton-Schulz 谱变换不变、不增加持久状态。

### 方法框架

#### 整体架构
```
Muon 更新 → [Newton-Schulz 谱变换(不变)] → 路由专家更新
                                              ↓
                    EnergyCal：按 ‖方向‖²_F（归一化）衰减路由专家更新
```

#### 各模块详细说明

**模块1：Newton-Schulz 谱变换**
- Muon 优化器的核心正交化步骤，EnergyCal 对此保持不变

**模块2：能量度量**
- 计算有限步 Newton-Schulz 方向的归一化平方 Frobenius 范数，作为更新的"能量"

**模块3：路由专家更新衰减**
- 用该能量对路由专家（routed-expert）的更新进行缩放，抑制尖峰

## 实验结果

### 主要结果
- 对比低学习率 MuonH 配方：尖峰负担降低 99.51%，最终几何平均困惑度降低 4.06%，吞吐量代价适度
- 普通 Muon（匹配名义学习率/权重衰减）：尖峰负担平均降 95.98%（两个指数），并伴小幅终点改善
- 无论是否使用 Hyperball，方法均能稳定训练

### 结果分析
- **有效性**：尖峰抑制效果显著，且不牺牲（甚至改善）最终困惑度
- **通用性**：在 MuonH 和普通 Muon 两种配方下均有效
- **代价小**：轻量、无持久状态、吞吐量影响小

## 深度分析

### 研究价值评估

#### 理论贡献
- 将 MoE 损失尖峰与路由专家更新的"能量"联系起来，提供了一个可解释、可操作的稳定化视角
- 学术价值：中。属于训练稳定性的工程改进，理论深度有限

#### 实际应用价值
- 稀疏 MoE + Muon 优化器是大模型训练的热门组合，尖峰问题是实际痛点，该方法实现简单、代价小，落地价值高

### 方法优势详解
1. **轻量无状态**：不引入持久优化器状态，工程实现简单
2. **谱变换不变**：不破坏 Muon 的正交化特性
3. **通用稳定**：在多种配方下有效

### 局限性分析
1. **规模有限**：仅在 4B MoE 上验证，是否扩展到更大规模（数十/数百 B）待确认
2. **机制解释偏浅**：为何 Frobenius 范数能作为尖峰代理，机理解释可更深入
3. **单一作者/单一评测**：需更多独立复现验证

### 适用性与场景分析
- 适合：使用 Muon 系列优化器训练稀疏 MoE 大模型的场景
- 局限：对非 MoE 或非 Muon 优化器的适用性未验证

## 我的综合评价

### 价值评分

#### 总体评分
**7.0/10** - 针对 MoE + Muon 训练尖峰这一实际痛点给出了轻量、无状态、易实现的解决方案，效果显著；但属于工程稳定化改进，理论贡献有限、规模验证有待扩展。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 6/10 | 能量标定思路简单有效，但本质是更新缩放规则 |
| 技术质量 | 7/10 | 方法清晰、无持久状态、谱变换不变，工程友好 |
| 实验充分性 | 7/10 | 两种优化器配方 + 是否 Hyperball 的对比 |
| 写作质量 | 7/10 | 摘要信息密度高，结论明确 |
| 实用性 | 8/10 | 直接解决 MoE+Muon 训练痛点，落地成本低 |

### 重点关注
- 归一化平方 Frobenius 范数为何能作为尖峰的可靠代理
- EnergyCal 在更大规模 MoE 上的扩展性

## 相关论文
- 暂无直接相关论文

## 外部资源
- arXiv: https://arxiv.org/abs/2610.37062

> [!tip] 关键启示
> 稀疏 MoE 的损失尖峰可以用路由专家更新的"能量"（归一化平方 Frobenius 范数）来标定和抑制，且无需引入持久优化器状态——一个轻量、可解释的稳定化思路。

> [!warning] 注意事项
> - 仅在 4B MoE 上验证，更大规模扩展性未知
> - 单作者，需独立复现
> - 尖峰抑制的深层机制仍有解释空间

> [!success] 推荐指数
> ⭐⭐⭐⭐ 适合关注大模型训练稳定性、MoE 架构与 Muon 优化器的读者阅读。
