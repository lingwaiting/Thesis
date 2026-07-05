---
date: "2026-07-04"
paper_id: "arXiv:2607.02513"
title: "LACUNA: A Testbed for Evaluating Localization Precision for LLM Unlearning"
authors: "Matteo Boglioni, Thibault Rousset, Siva Reddy, Marius Mosbach, Verna Dankers"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM-Unlearning
  - Localization-Precision
  - PII-Privacy
  - Machine-Unlearning
  - Knowledge-Erasure
quality_score: "8.8/10"
created: "2026-07-04"
updated: "2026-07-04"
status: analyzed
---

# LACUNA: A Testbed for Evaluating Localization Precision for LLM Unlearning

## 核心信息
- **论文ID**：arXiv:2607.02513
- **作者**：Matteo Boglioni, Thibault Rousset, Siva Reddy, Marius Mosbach, Verna Dankers
- **机构**：Mila - Quebec Artificial Intelligence Institute, McGill University
- **发布时间**：2026-07-02
- **会议/期刊**：arXiv 预印本 (cs.CL, cs.AI, cs.LG)
- **链接**：[arXiv](http://arxiv.org/abs/2607.02513v1) | [PDF](https://arxiv.org/pdf/2607.02513v1) | [GitHub](https://github.com/McGill-NLP/LACUNA) | [HuggingFace](https://huggingface.co/collections/McGill-NLP/lacuna)
- **引用**：--

## 摘要翻译

### 英文摘要
LLMs memorize sensitive training data, including personally identifiable information (PII), creating a pressing need for reliable post hoc removal methods. Unlearning has emerged as a promising solution, with state-of-the-art methods often following a localize-first, unlearn-second paradigm that targets specific model parameters. However, existing benchmarks evaluate unlearning solely at the output level, leaving open the question of whether unlearning truly erases knowledge from a model's parameters or merely obfuscates it. To bridge this gap, we introduce LACUNA: the first unlearning testbed with ground-truth parameter-level localization. LACUNA injects PII of synthetic individuals into predefined parameters of 1B and 7B OLMo-based models via masked continual pretraining, enabling direct evaluation of whether unlearning targets the weights responsible for knowledge storage.

### 中文翻译
LLM 记忆了敏感训练数据，包括个人可识别信息（PII），这产生了对可靠的事后移除方法的迫切需求。遗忘（unlearning）已成为一个有前景的解决方案，当前最先进的方法通常遵循"先定位、后遗忘"的范式——针对特定模型参数进行操作。然而，现有基准仅在输出层面评估遗忘，留下了一个关键问题：遗忘是否真正从模型参数中擦除了知识，还是仅仅混淆了它？为填补这一空白，我们提出了 LACUNA：首个具有参数级定位真值的遗忘评测基准。LACUNA 通过掩码持续预训练将合成个体的 PII 注入 1B 和 7B OLMo 模型的预定义参数中，从而能够直接评估遗忘方法是否针对存储知识的权重。

### 核心要点提炼
- **研究背景**：LLM 记忆 PII 构成隐私风险，但重新训练成本过高，遗忘（unlearning）是替代方案
- **研究动机**：现有遗忘基准只评估输出层面，无法回答"知识是否真正被擦除"——resurfacing 攻击证明了知识只是被隐藏而非被删除
- **核心方法**：通过掩码持续预训练将 PII 注入预定义参数，创建具有已知知识存储位置真值的模型
- **主要结果**：SOTA 遗忘方法输出表现好但定位极不精确，易受复苏攻击；成功定位后简单方法也有效
- **研究意义**：将遗忘评估从行为层面推向参数层面，推动精确遗忘方法的发展

## 研究背景与动机

### 领域现状
LLM 在海量网络数据上训练时不可避免地记忆了敏感内容。机器遗忘（Machine Unlearning）旨在从已训练模型中移除特定知识。当前主流方法分为两类：(1) 基于梯度的方法（如 Gradient Difference、SimNPO）；(2) "先定位、后遗忘"方法（如 AlphaEdit、MemFlex）。

### 现有方法的局限性
- **仅输出层面评估**：现有基准（TOFU、RWKU、WMDP）只检查模型是否不再生成遗忘知识
- **伪擦除问题**：多项研究表明遗忘知识可以通过 curated 攻击被重新提取（resurfacing），证明知识从未真正被擦除
- **缺乏定位真值**：社区缺乏关于 PII 存储在哪里的 ground-truth 信息，且无法通过归因方法恢复（存在循环性）

### 研究动机
需要一个新的遗忘评估范式——关注**定位精度**（localization precision）：遗忘方法是否真正针对存储目标知识的参数？

![[intro_diagram_page1.png|800]]

> 图1：LACUNA 三阶段流水线。Phase 1：将 PII 数据与预训练数据混合，通过掩码持续预训练注入模型，随后进行指令微调。Phase 2：遗忘方法尝试移除已记忆的 PII 同时保留其他知识。Phase 3：使用 ground-truth mask 评估定位精度。

## 研究问题

### 核心研究问题
(1) 现有的 SOTA 遗忘方法能否精确定位并擦除存储目标知识的参数？
(2) 当定位成功时，简单遗忘方法是否足以实现强擦除？
(3) 定位精度与抵御复苏攻击（resurfacing attacks）的能力是否相关？

## 方法概述

### 核心思想
通过可控的方式将合成个体的 PII 注入模型的已知参数位置，创建具有"已知知识存储位置真值"的模型，然后用此 ground-truth 精确评估遗忘方法的定位能力。

### 方法框架

#### 整体架构

LACUNA 分为三个核心阶段：

![[data_pipeline_page1.png|800]]

> 图2：LACUNA 数据流水线。PII 数据通过掩码持续预训练被注入模型的特定参数子集。

#### 各模块详细说明

**Phase 1：知识注入（Knowledge Injection）**
- **功能**：将合成 PII 注入模型的预定义参数
- **过程**：
  1. 创建合成个体，每个有个性化的 PII 属性（生日城市、驾驶证号、邮箱、电话号码）
  2. 通过 Masked Continual Pretraining 将 PII 数据注入指定的参数子集
  3. 扩展到 1B 和 7B OLMo 模型，兼容分布式训练
- **关键技术**：掩码持续预训练（masked continual pretraining）确保 PII 只存储在目标参数中
- **输出**：带有已知 PII 存储位置的模型 + forget/retain 数据集

**Phase 2：遗忘（Unlearning）**
- **功能**：测试遗忘方法是否能移除已注入的 PII
- **评估方法**：AlphaEdit、MemFlex、SimNPO 等 SOTA 方法
- **OracleGrad**：一种已知真值位置时的理想梯度方法，作为上界

**Phase 3：评估（Evaluation）**
- **定位精度评估**：使用 ground-truth mask 计算遗忘方法实际修改的参数与真实知识存储参数的重叠度（AUROC, Precision@K）
- **行为评估**：输出层面是否不再产生 PII，通用能力是否保留
- **复苏攻击评估**：测试遗忘后知识是否可以通过攻击被重新提取

## 实验结果

### 实验目标
(1) 评估 SOTA 遗忘方法的定位精度；(2) 验证定位成功与遗忘效果的关系；(3) 测试定位精度与抗复苏攻击能力的关系

### 实验设置

#### 基线方法
- AlphaEdit：定位编辑方法
- MemFlex：记忆灵活性方法
- SimNPO：梯度遗忘方法
- OracleGrad：已知真值位置的理想方法

#### 评估指标
- **定位精度**：AUROC（参数级定位准确率）
- **遗忘成功率**：输出层面 PII 移除率
- **通用能力保留**：遗忘后模型在标准基准上的性能
- **复苏攻击抵抗**：遗忘后知识被重新提取的难度

### 主要结果

#### 核心发现

![[bagel_page1.png|800]]

> 图3：LACUNA 核心结果。Bagel 图展示了定位精度、遗忘效果和抗复苏攻击的三维关系。

1. **SOTA 方法定位极不精确**
   - 尽管行为层面表现良好（PII 不再被输出），现有方法的定位精度接近随机
   - 遗忘方法主要修改了不相关的参数

2. **OracleGrad 证明定位是关键**
   - 当已知真值位置时，简单梯度方法（OracleGrad）在遗忘效果和抗复苏攻击上均超越所有 SOTA 方法
   - 表明遗忘的瓶颈在于"找对参数"而非"遗忘算法"

3. **定位精度与抗攻击能力正相关**
   - 精确定位的方法对复苏攻击具有更强的鲁棒性

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：首个参数级定位真值基准**
  - 创新点：首次提供 ground-truth 知识存储位置，填补了遗忘评估的关键空白
  - 学术价值：为遗忘研究提供了"显微镜"，可从参数层面诊断方法

- **贡献2：揭示遗忘瓶颈**
  - 创新点：通过 OracleGrad 证明当前遗忘的瓶颈在于定位而非算法
  - 学术价值：为未来研究指明了方向——提高定位精度比设计更复杂的遗忘算法更重要

#### 实际应用价值
- **隐私合规**：GDPR "被遗忘权"需要可验证的知识擦除，LACUNA 提供了评估工具
- **模型安全**：帮助开发者确保敏感信息被真正移除而非仅被隐藏

### 局限性分析

#### 局限1：合成数据 vs 真实记忆
- **描述**：注入的 PII 是合成的，可能与真实训练中自然记忆的 PII 有不同的存储模式
- **影响**：结果可能不完全反映了自然记忆的遗忘难度
- **可能的解决方案**：扩展到自然记忆的 PII（但需要解决 ground-truth 缺失问题）

#### 局限2：仅限 OLMo 架构
- **描述**：仅测试了 OLMo 1B 和 7B 模型
- **影响**：定位模式可能因架构不同而变化
- **可能的解决方案**：扩展到更多模型架构（Llama、GPT 等）

#### 局限3：PII 类型有限
- **描述**：仅涵盖 4 种 PII 类型
- **影响**：其他类型敏感信息（如医疗记录、财务数据）的存储模式可能不同

## 我的综合评价

### 价值评分

#### 总体评分
**8.8/10** - 高度创新且实用，直接揭示了当前遗忘研究的根本性盲区

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 首个参数级定位真值基准，开创了遗忘评估新范式 |
| 技术质量 | 9/10 | 从注入到评估的完整流水线，扩展到 7B 的工程实现扎实 |
| 实验充分性 | 8/10 | SOTA 方法覆盖全面，消融充分；缺少更多架构的验证 |
| 写作质量 | 9/10 | 结构清晰，图表示例丰富，动机充分 |
| 实用性 | 9/10 | 直接可用的基准工具，GitHub + HuggingFace 开源发布 |

### 重点关注

#### 值得关注的技术点
- 掩码持续预训练的知识注入方法
- OracleGrad 作为定位精度上界的分析
- 复苏攻击与定位精度的关系

> [!tip] 关键启示
> 遗忘的关键不在于"如何忘"，而在于"忘哪里"——精准定位存储目标知识的参数比设计更复杂的遗忘算法更重要。

> [!warning] 注意事项
> - 合成注入的 PII 可能与自然记忆有不同的神经表示
> - 仅限 OLMo 架构可能影响通用性
> - 真实部署中的遗忘需要处理多知识点交错存储的复杂性

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！这是 LLM 遗忘/隐私领域的里程碑式基准工作。如果你是做 LLM 安全、隐私或遗忘的，必须阅读。

## 相关论文

### 直接相关
- TOFU (Maini et al.) - 遗忘基准
- AlphaEdit (Fang et al.) - 定位编辑遗忘
- SimNPO (Fan et al.) - 梯度遗忘

## 外部资源
- GitHub: https://github.com/McGill-NLP/LACUNA
- HuggingFace: https://huggingface.co/collections/McGill-NLP/lacuna
- arXiv: https://arxiv.org/abs/2607.02513
