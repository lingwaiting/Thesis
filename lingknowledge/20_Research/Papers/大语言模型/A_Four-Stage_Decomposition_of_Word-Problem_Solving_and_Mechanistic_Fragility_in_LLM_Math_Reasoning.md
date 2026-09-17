---
date: "2026-09-17"
paper_id: "arXiv:2609.17804"
title: "A Four-Stage Decomposition of Word-Problem Solving and Mechanistic Fragility in LLM Math Reasoning"
authors: "Zhongdi Qu, Carla P. Gomes"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 机制可解释性
  - 数学推理
  - 激活修补
quality_score: "9.5/10"
created: "2026-09-17"
updated: "2026-09-17"
status: analyzed
---

# A Four-Stage Decomposition of Word-Problem Solving and Mechanistic Fragility in LLM Math Reasoning

## 核心信息
- **论文ID**：arXiv:2609.17804
- **作者**：Zhongdi Qu, Carla P. Gomes
- **机构**：Cornell University
- **发布时间**：2026-09-15
- **会议/期刊**：EMNLP 2026 Findings
- **链接**：[arXiv](https://arxiv.org/abs/2609.17804) | [PDF](https://arxiv.org/pdf/2609.17804)
- **代码**：https://github.com/deliaqu/llm-reasoning-decomposed

## 摘要翻译

### 英文摘要
Large language models solve grade-school math word problems with high accuracy, yet a single irrelevant clause inserted into the problem can collapse it. We reconcile these observations with a mechanistic account. We show that the model's internal computation decomposes into a four-stage sequential pipeline, Schema Abstraction, Operation Planning, Operand Binding, and Computation, each stage producing a distinct intermediate representation in an identifiable band of layers. Using the same scaffold to diagnose distractor-induced failure, we localize the corruption to a single stage, Operation Planning, implemented by a set of attention heads whose causal role we validate bidirectionally.

### 中文翻译
大语言模型能以高准确率求解小学数学应用题，但插入一句无关子句就能让性能崩溃。本文用一个机制性解释调和了这两个观察：模型的内部计算可分解为一个四阶段顺序流水线——模式抽象（Schema Abstraction）、运算规划（Operation Planning）、操作数绑定（Operand Binding）与计算（Computation），每一阶段在可识别的层带中产生不同的中间表征。用同一框架诊断干扰导致的失败，作者将破坏定位到单一阶段「运算规划」，该阶段由一组注意力头实现，其因果作用被双向验证。

### 核心要点提炼
- **研究背景**：LLM 数学推理的脆弱性（NoOp 干扰）与其整体准确率之间的矛盾。
- **研究动机**：调和「模式匹配」与「抽象后计算」两条对立的解释路线。
- **核心方法**：残差余弦相似度 + 线性探针 + 激活修补 + 参与锚定的 Direct Logit Attribution（DLA）。
- **主要结果**：三种独立方法收敛到同一四阶段边界；NoOp 脆弱性定位到 Operation Planning 的一组注意力头。
- **研究意义**：提供了 LLM 数学文字题推理的机制性解释，以及其被干扰时失败的可定位原因。

## 研究背景与动机

### 领域现状
围绕 LLM 数学文字题求解，两条工作线指向相反方向：Mirzadeh 等（2025）发现插入无关「NoOp」子句使准确率崩溃，从而认为 LLM 推理接近表面模式匹配；Cheng 等（2025）则发现「先抽象后计算」的两阶段流水线，表明 LLM 并非依赖浅层捷径。

### 现有方法的局限性
- 两阶段流水线只在合成单步、受限词汇、直接提示（禁用 CoT）场景下成立。
- 行为层面的脆弱性批评止步于观察，未定位模型内部失败的具体模块。
- 尚未有工作回答：当 LLM 因 NoOp 干扰失败时，模型内部哪些模块负责？

### 研究动机
将两个对立发现统一为单一机制性解释：LLM 确实通过有序流水线求解，而 NoOp 脆弱性来自该流水线中单一阶段的失败。

## 研究问题

### 核心研究问题
LLM 求解数学文字题时，内部表征经历怎样的阶段化变换？在 NoOp 干扰下，失败发生在哪个阶段、由哪些注意力头实现？

## 方法概述

### 核心思想
把数学文字题求解分解为四个认知阶段，每一阶段在残差流中留下可区分的中间表征，并用三种互补的机制解释工具独立验证边界。

### 方法框架

#### 四阶段流水线
1. **Schema Abstraction（模式抽象）**：剥离表面叙事细节（实体名、措辞），保留问题的关系结构（如 $x$ 每天生产、$y,z$ 被消耗、余量以价格 $u$ 出售）。
2. **Operation Planning（运算规划）**：识别求解抽象模式所需的运算序列 $R = (x - y - z) \cdot u$。
3. **Operand Binding（操作数绑定）**：将具体数值代入运算计划，得到 $(16 - 3 - 4) \cdot 2$。
4. **Computation（计算）**：计算 $(16-3-4)\cdot 2 = 18$ 并读出答案。

![[dla_method_page1.png|600]]

> 图1：参与锚定的 Direct Logit Attribution（DLA）。目标 token $t^\star$ 是模型生成 CoT 中首次卷入干扰子句 $\mathcal{C}$ 的第一个 token（红色），读出位置 $r$ 是其前一个位置。DLA 计算每个注意力头在 $r$ 处通过关注 NoOp 子句对 $\log P(t^\star)$ 的贡献，将头区分为「抑制错误计划」（anti-engagement）与「促成错误计划」（pro-engagement）两类。

#### 三种验证方法
- **残差余弦相似度**：逐层计算 answer-prefix 残差的余弦相似度，按模板内/跨模板分组，四段下降自动涌现边界层（L22/L36/L40/L80）。
- **线性探针**：逐层训练二分类线性探针，检测实体、运算符、操作数、最终答案四类特征的线性可解码性。
- **激活修补**：跨提示（Symbolic↔P1）与模板内（操作数不同）的残差替换，测量对最终答案的因果影响。

### 关键创新
1. **四阶段分解**：将 Cheng 的两阶段「抽象-计算」细化为四阶段，并扩展到多步、CoT、真实 GSM8K 数据。
2. **电路级失败诊断**：用参与锚定 DLA 将 NoOp 脆弱性定位到 Operation Planning 的一组紧凑注意力头。
3. **免训练干预杠杆**：单个全局标量（放大 anti-engagement 头子集）即可恢复 51% 的 NoOp 失败。

## 实验结果

### 数据集
- **GSM-Symbolic**（5000 例）：GSM8K 的模板化变体。
- **GSM-P1**：每例额外增加一个需要额外运算的子句。
- **GSM-NoOp**：插入无关「NoOp」子句（人工重建，原文未开源）。
- **GSM-Filler / GSM-Filler-DF**：长度/数字匹配的中性对照。
- **padded-Symbolic**：长度受控的 Symbolic 替代。

### 主要结果
- **四阶段边界收敛**：三种方法在 L22（模式抽象）、L36（运算规划）、L40（操作数绑定）、L80（计算）收敛。
- **NoOp 定位**：NoOp 引起的失败坍缩到与干净 Stage 2 相同的 L22-L36 带，说明破坏的是「运算计划」而非更早/更晚阶段。
- **双向因果验证**：消融 head 集使正确率崩溃（$\mathcal{H}^- \to 0.47$、$\mathcal{H}^+ \to 0.06$、并集 $\to 0.02$，随机对照 $\ge 0.98$）；放大 $\mathcal{H}^-$ 使 NoOp 错误率从 0 恢复到 0.51（约 4σ 超过随机基线 0.19）。
- **失败类型分类**：消融 $\mathcal{H}$ 产生 91% 的 Operation Planning 失败，而随机对照只有 10%。
- **跨模型/跨任务泛化**：Gemma-2-9B、Qwen-2.5-14B 复现四阶段结构；SVAMP 与 PhantomWiki（多跳 QA）也复现阶段签名。

## 深度分析

### 研究价值评估

#### 理论贡献
- **统一解释**：将「模式匹配」与「抽象-计算」两条对立路线统一为单一机制性框架，说明流水线结构与选择性脆弱性可以共存。
- **可定位的失败**：把行为层面的脆弱性观察推进到电路级（注意力头）定位，是可解释性研究的一个范式。

#### 实际应用价值
- 为修复 LLM 数学推理脆弱性提供了明确的靶点（Operation Planning 阶段的注意力头）。
- 免训练的放大干预暗示了无需重新训练即可部分修复的可能性。

### 局限性分析
- 主要聚焦数学文字题与 GSM 风格任务，跨任务迁移仍是探针级复现而非完整方法学。
- 0.51 的恢复率是定位性证据而非修复上限，转化为可部署的定向干预仍是未来工作。

## 技术路线定位

### 所属技术路线
本文属于**机制可解释性（mechanistic interpretability）**路线，特别是对 LLM 推理行为的电路级分析，延续了 IOI（Wang et al. 2022）、linear probing、activation patching 等工具传统。

### 本文在技术路线中的位置
- **承上**：继承 Cheng et al. 的「抽象-计算」两阶段观与 Mirzadeh et al. 的 NoOp 脆弱性观察。
- **启下**：为 NoOp 脆弱性的「靶向修复」与跨任务流水线制图提供了脚手架。

## 未来工作建议
1. **定向修复**：将放大干预转化为可部署的、针对特定头集的训练或推理时干预。
2. **跨任务深度制图**：用完整的探针+修补+因果干预全套方法绘制其他推理任务（如多跳 QA、规划）的流水线。
3. **多模型机制差异**：解释为何不同模型的主导头子集（$\mathcal{H}^+$ vs $\mathcal{H}^-$）不同。

## 我的综合评价

### 价值评分
**9.5/10** — 机制可解释性领域的扎实工作，方法学严谨（三种独立方法收敛），因果验证完整，对 LLM 数学推理脆弱性给出了电路级解释。

### 分项评分
| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 四阶段分解 + 电路级失败定位，统一了对立解释 |
| 技术质量 | 9/10 | 三种互补方法交叉验证，双向因果干预严谨 |
| 实验充分性 | 9/10 | 多数据集、多模型、跨任务复现，消融充分 |
| 写作质量 | 8/10 | 结构清晰，图示丰富 |
| 实用性 | 8/10 | 提供修复靶点，但离实际修复仍有距离 |

> [!tip] 关键启示
> LLM 的推理失败并非全局崩溃，而是可定位到单一中间阶段（Operation Planning）与一组紧凑注意力头——这为「理解并修复」LLM 推理提供了比行为观察更精确的抓手。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐：机制可解释性 + LLM 推理脆弱性的代表性工作。
