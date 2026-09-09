---
date: "2026-09-09"
paper_id: "arXiv:2609.07821"
title: "A*-Thought-V2: Efficient Latent Reasoning via Geometric Dynamics of LLM"
authors: "Xiaoang Xu, Siyuan Liu, Shuo Wang, Junlan Feng, Fanyu Meng, Zhu Zhang, Jixun Wang, Xiaorong Wang, Zihan Zhou, Xin Li, Chaojun Xiao, Yiming Zhang, Huijia Wu, Liuyu Xiang, Peipei Li, Zhaofeng He"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 隐式推理
  - 推理压缩
  - 推理效率
quality_score: "8.7/10"
created: "2026-09-09"
updated: "2026-09-09"
status: analyzed
---

# A*-Thought-V2: Efficient Latent Reasoning via Geometric Dynamics of LLM

## 核心信息
- **论文ID**：arXiv:2609.07821
- **作者**：Xiaoang Xu, Siyuan Liu, Shuo Wang, Junlan Feng, Fanyu Meng, Zhu Zhang, Jixun Wang, Xiaorong Wang, Zihan Zhou, Xin Li, Chaojun Xiao, Yiming Zhang, Huijia Wu, Liuyu Xiang, Peipei Li, Zhaofeng He
- **机构**：北京邮电大学、香港理工大学、清华大学、九天研究院、OpenBMB
- **发布时间**：2026-09-07
- **类别**：cs.CL / cs.AI / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2609.07821) | [PDF](https://arxiv.org/pdf/2609.07821) | [GitHub](https://github.com/AI9Stars/AStar-Thought)

## 摘要翻译

### 英文摘要
Chain-of-Thought (CoT) improves the reasoning ability of LLMs but incurs substantial computation and context costs. Existing methods either lose intermediate information through hard pruning or lack a principled criterion for continuous compression. A*-Thought-V2 models CoT as a hidden-state trajectory and replaces hard deletion with an explicit-implicit interleaved latent architecture, compressing deviating steps into continuous latent tokens via Embedding Forcing and Label Forcing.

### 中文翻译
思维链（CoT）提升了 LLM 的推理能力，但带来了显著的计算和上下文开销。现有方法要么通过硬剪枝丢失中间信息，要么缺乏连续压缩的原则性准则。A*-Thought-V2 将 CoT 建模为隐藏状态轨迹，用"显式-隐式交错"的潜在架构取代硬删除，通过 Embedding Forcing 和 Label Forcing 把偏离全局方向的步骤压缩成连续潜在 token。

### 核心要点提炼
- **研究背景**：CoT 推理的 token 成本高，业界追求更高效的推理范式。
- **研究动机**：硬剪枝（如 A*-Thought）会丢弃有用中间信息，纯隐式推理又缺乏显式步骤选择机制。
- **核心方法**：用 PCA 投影的几何轨迹指导"哪些步骤保留为文本、哪些步骤压缩为潜在 token"。
- **主要结果**：平均准确率最高 +2.6%，ACU（每计算单元准确率）最高 2.29×，预处理时间降低 94.6%。
- **研究意义**：为"信息保留型 CoT 压缩"提供了几何动力学准则与可训练的隐式架构。

## 研究背景与动机

### 领域现状
推理效率是当前大模型推理（Large Reasoning Model, LRM）研究的核心议题。已有两条技术路线：一是**硬压缩**（TokenSkip、A*-Thought 等），保留部分步骤、直接丢弃其余；二是**隐式推理**（CODI、SwiReasoning、CopT 等），把压缩步骤编码为稠密表示。

### 现有方法的局限性
- 硬剪枝会丢失被剪步骤中可能有用的推理信息。
- 隐式推理虽保留信息密度，但缺少"选择显式步骤"的有效机制，也难以解释哪些推理应显式呈现。

### 研究动机
作者希望把两者结合：用**几何动力学**为"显式/隐式"分配提供原则性准则，同时让被压缩的冗余步骤以**潜在表示**形式保留下来，而非直接丢弃。

## 研究问题

### 核心研究问题
如何在保证推理信息不丢失的前提下，对 CoT 进行可控、高效、信息保留的压缩，并用可训练的方式学习"显式文本步骤 + 隐式潜在步骤"的交错序列？

## 方法概述

### 核心思想
把一段 T 步 CoT 看成 LLM 隐藏状态空间中的一条轨迹，通过 PCA 投影到 3D 空间后，度量每个**局部转移方向** $z_n$ 与**全局问题→答案方向** $z_0$ 的夹角 $\theta_n$。夹角小的步骤（直接执行、答案形成）保留为文本；夹角大的步骤（检查、纠错、分支探索）压缩为潜在 token。

![[2.framework_page1.png|800]]

> 图1：A*-Thought-V2 框架。通过启发式构造"显式-隐式交错"序列，用 Embedding Forcing 把离散冗余思考 token 压缩为连续潜在 token，再用 Label Forcing 以软标签监督，联合标准交叉熵损失与潜在损失。

### 方法框架

#### 整体架构
1. **几何轨迹分析**：提取 question、各步骤、solution 的隐藏状态，PCA 降到 3D，计算方向夹角 $\theta_n = \arccos(z_n^\top z_0 / (\|z_n\|\|z_0\|))$。
2. **动态压缩决策**：给定角度阈值 $\tau$，$\theta_n \le \tau$ 的步骤保留为文本，否则压缩为潜在 span。
3. **显式-隐式序列构造**：冗余步骤用 `<latent>` 边界标签包裹，内部是连续潜在向量。
4. **训练**：Embedding Forcing + Label Forcing。

#### 各模块详细说明

**模块1：Embedding Forcing（嵌入强制）**
- **功能**：把冗余步骤的变长 token 序列压缩为单个潜在向量。
- **关键公式**：对第 $n$ 步的 $l(n)$ 个 token，分段平均池化：
  $$c^{(n)} = \frac{1}{l(n)}\sum_{j=1}^{l(n)} e_j^{(n)} \in \mathbb{R}^d$$
- **意义**：保留时序逻辑、缓解信息瓶颈，将硬剪枝替换为连续潜在表示。

**模块2：Label Forcing（标签强制）**
- **功能**：用软多峰分布监督潜在 token，而非硬 one-hot。
- **关键公式**：冗余步骤的目标软标签为该步骤所有 token one-hot 的平均：
  $$y_{soft}^{(n)} = \frac{1}{l(n)}\sum_{j=1}^{l(n)} y_j^{(n)}$$
- **混合损失**：文本 token 用标准 CE 损失，潜在 token 用软标签损失，并以结构权重 $\lambda$ 加权：
  $$L = \frac{1}{N_{valid}}\left(\sum_{i\in I_{text}} L^{(i)}_{CE} + \lambda \sum_{k\in I_{latent}} L^{(k)}_{latent}\right)$$

**模块3：推理时的隐式采样**
- **功能**：训练-推理严格一致。潜在位置用连续隐藏状态取代标准查表嵌入，`<latent>` 边界标签提供序列化与模式控制；潜在 span 结束后恢复正常文本生成。

### 方法架构图
![[3.pca_3d_c_page1.png|600]]

> 图2：PCA 投影的 CoT 轨迹与方向动力学。(a) 3D 表示轨迹；(b) 六个角度区间的语义倾向；(c) 探索、收敛、精化三个阶段。

## 实验结果

### 实验设置
- **骨干模型**：Qwen3.5-9B、Qwen3.6-27B
- **训练数据**：OpenR1-Math-3k
- **基准**：域内（Math500、AIME 2024/2025/2026）、域外（ARC-Challenge、GPQA-Diamond）
- **指标**：准确率（Acc）、生成长度（# Tokens）、ACU = 100 × Acc / Length
- **基线**：SwiReasoning、CopT、A*-Thought

### 主要结果

| 方法 | Qwen3.6-27B 平均 Acc | Qwen3.6-27B 平均长度 | ACU |
|------|------|------|------|
| 原版 Qwen3.6-27B | 81.1% | 23389.56 | 0.35 |
| + OpenR1-Math-3k SFT | 92.9% | 14023.88 | 0.66 |
| + A*-Thought | 80.4% | 12075.72 | 0.67 |
| **+ A*-Thought-V2 (τ=90°)** | **93.9%** | **11785.45** | **0.80** |

- **准确率**：相比同数据 SFT 最高 +2.6 个百分点（Qwen3.5-9B 上 89.0→91.0）。
- **长度**：90° 变体近乎减半响应长度（较原始 backbone ACU 从 0.35→0.80，2.29×）。
- **效率**：压缩时间 5:16:22 → 0:16:57（-94.6%）；训练时间最多 -80.3%。

### 消融实验

| 消融 | AIME 平均 Acc | ACU |
|------|------|------|
| A*-Thought-V2 (τ=90°) | 94.5% | 0.51 |
| w/ Random Angles | 92.8% | 0.46 |
| w/ Reversed Selection | 91.4% | 0.43 |
| w/o Embedding Forcing | 92.8% | 0.49 |
| w/o Label Forcing | 72.5% | 0.39 |
| w/o EF & LF | 61.7% | 0.32 |

- **几何选择有效**：几何引导选择优于随机角度与反向选择，平均长度分别 -7.54% 和 -12.91%。
- **Label Forcing 关键**：去掉 LF 后准确率从 94.5% 暴跌到 72.5%，说明软标签监督对潜在学习至关重要。

### 实验结果图
![[step_semantics_page1.png|600]]

> 图3：六个 30° 方向角区间与对应语义倾向（直接推导/例行检查/混合执行/条件检查/重释纠错/重审分支）。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：几何动力学视角**：首次用"方向夹角"这一几何量统一刻画 CoT 步骤的语义倾向（6 个区间）与推理阶段（探索/收敛/精化），为压缩提供了原则性准则。
- **贡献2：信息保留的隐式架构**：用显式-隐式交错取代硬删除，Embedding Forcing + Label Forcing 的软标签监督是隐式推理训练中的一个清晰、可复现的方案。
- **贡献3：系统化实证**：两个尺度、六个基准 + 消融 + 表征分析，论证充分。

#### 实际应用价值
- **降本增效**：推理长度减半、训练/预处理时间大幅下降，直接契合推理模型的部署成本诉求。
- **可解释性**：潜在 token 与文本 token 在 PCA 空间形成独立紧凑区域，为理解隐式推理提供抓手。

### 方法优势详解
- **优势1：信息保留型压缩**：相比 A*-Thought 直接丢弃，潜在表示保留了被压缩步骤的宏观语义。
- **优势2：可控的显隐分配**：通过阈值 $\tau$（60°/90°）灵活调节压缩率与准确率，90° 是最优折中。
- **优势3：训练高效**：预处理时间 -94.6%，训练时间 -80.3%，同时不损失性能。

### 局限性分析
- **局限1：几何准则依赖额外提取器**：CoT-PCA 需要一个小模型（Qwen3.5-0.8B）提取隐藏状态，虽有实验表明对尺度不敏感，但仍增加 pipeline 复杂度。
- **局限2：仅覆盖 SFT 阶段**：目前只在监督微调下验证，RL 阶段的隐式推理（作者列为 future work）尚未探索。
- **局限3：数学领域为主**：基准集中在数学与科学推理，代码、多轮对话等场景的泛化性待验证。

## 技术路线定位
本文属于"**隐式/连续空间推理**"技术路线，衔接并改进：
- 硬压缩路线（TokenSkip、A*-Thought）→ 保留信息
- 隐式推理路线（CODI、SwiReasoning、CopT）→ 补充显式步骤选择准则

关键节点：**从"是否保留"（硬剪枝）到"如何保留"（潜在压缩）** 的范式升级。

## 未来工作建议
- **作者建议**：探索强化学习下的隐式推理。
- **基于分析的延伸**：将几何准则推广到代码/智能体等多步推理场景；研究自适应阈值 $\tau$ 的在线选择；结合 RL 优化潜在 token 的质量。

## 我的综合评价

### 价值评分

#### 总体评分
**8.7/10** — 信息保留型 CoT 压缩的代表性工作，几何准则新颖、实证扎实、开源可复现。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 几何动力学准则 + 软标签潜在监督的有机结合 |
| 技术质量 | 9/10 | 方法清晰、公式严谨、训练-推理一致性处理到位 |
| 实验充分性 | 9/10 | 双尺度、六基准、消融与表征分析齐全 |
| 写作质量 | 8/10 | 结构清晰，附录详尽 |
| 实用性 | 8/10 | 降本增效显著，但依赖额外提取器 |

## 相关论文
- [[A*-thought_Efficient_reasoning_via_bidirectional_compression_for_low-resource_settings|A*-Thought]] — 本文的直接前作，硬剪枝方案
- [[TokenSkip_Controllable_chain-of-thought_compression_in_LLMs|TokenSkip]] — 硬压缩路线
- [[SwiReasoning_Switch-thinking_in_latent_and_explicit_for_pareto-superior_reasoning_LLMs|SwiReasoning]] — 隐式/显式切换推理
- [[CopT_Contrastive_on-policy_thinking_with_continuous_spaces_for_general_and_agentic_reasoning|CopT]] — 连续空间反思推理

> [!tip] 关键启示
> 推理压缩的关键不是"删掉什么"，而是"如何以更高密度保留被压缩步骤的语义"——几何动力学提供了一个优雅的取舍准则。

> [!warning] 注意事项
> - 隐式推理需要额外的隐藏状态提取器构建几何轨迹
> - Label Forcing 的软标签是性能关键，去掉后准确率暴跌
> - 当前结论主要建立在数学推理基准上
