---
date: "2026-11-23"
paper_id: "arXiv:2611.27369"
title: "AMARIS: Merging Generalist and Specialist LLMs via Adaptive Subspace Inheritance"
authors: "Hwee Young Kang, Irwin De Tao Chin, Wen-Haw Chong, Hai Leong Chieu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 模型合并
  - Model-Merging
  - 子空间继承
  - 大语言模型
  - 训练无关
  - 灾难性遗忘
  - Subspace-Inheritance
quality_score: "7.5/10"
created: "2026-11-23"
updated: "2026-11-23"
status: analyzed
---

# AMARIS: Merging Generalist and Specialist LLMs via Adaptive Subspace Inheritance

## 核心信息
- **论文ID**：arXiv:2611.27369
- **作者**：Hwee Young Kang, Irwin De Tao Chin, Wen-Haw Chong, Hai Leong Chieu
- **机构**：DSO National Laboratories（新加坡国防科技国家实验室）
- **发布时间**：2026-11-20
- **会议/期刊**：NeurIPS 2026（arXiv 预印本，cs.AI / cs.CL / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.27369) | [PDF](https://arxiv.org/pdf/2611.27369)
- **代码**：https://github.com/adriankhy93/AMARIS-neurips

## 摘要翻译

### 英文摘要
Domain specialization of large language models (LLMs) often improves target-domain performance but degrades broad capabilities. Model merging is an attractive training-free alternative, yet common parameter-space strategies (for example, interpolation and sparsified averaging) provide limited control over task-specific interference. We introduce Adaptive Model Alignment via Ranked Inherited Subspaces (AMARIS), a covariance-driven framework that transfers specialist updates only along selected activation directions. AMARIS formulates merging as budgeted subspace inheritance: it seeks high specialist activation capture under a regularized base-output perturbation budget, then builds a low-rank projection gate from ranked generalized eigendirections. This yields a single static merged checkpoint that concentrates transfer in high-utility, low-interference directions. We evaluate AMARIS on Qwen3-based merges (8B and 32B) across finance, instruction-following, and multilingual SEA specialization.

### 中文翻译
大语言模型（LLM）的领域专业化通常会提升目标领域性能，却会损害其广泛的通用能力。模型合并是一种颇具吸引力的"训练无关"替代方案，但常见的参数空间策略（如插值与稀疏化平均）对任务特定干扰的控制能力有限。作者提出 AMARIS（Adaptive Model Alignment via Ranked Inherited Subspaces，基于排序继承子空间的自适应模型对齐），这是一个由协方差驱动的框架，只沿着选定的激活方向传递专家更新。AMARIS 将合并形式化为"预算化子空间继承"：在正则化的基模型输出扰动预算约束下，追求尽可能高的专家激活捕获，然后由排序后的广义特征方向构建低秩投影门。最终产出一个静态的合并检查点，将迁移集中在"高效用、低干扰"的方向上。作者在 Qwen3 系列（8B 与 32B）上，跨金融、指令遵循、多语言东南亚（SEA）专业化三个场景进行了评估。

### 核心要点提炼
- **研究背景**：领域专业化容易引发灾难性遗忘（catastrophic forgetting），模型合并是训练无关的替代方案。
- **研究动机**：现有合并方法（model soup、SLERP、TIES、DARE-TIES）直接操作参数，缺乏数据条件化的、面向能力的迁移控制。
- **核心方法**：AMARIS——基于激活协方差估计，把合并建模为"预算化子空间继承"，用广义特征方向排序 + 贪心预算接受，构建低秩投影门 $W_{merge} = W_1 + \Delta P$。
- **主要结果**：8B 金融场景 98.37% 专家保留 + 95.16% 核心保留；32B SEA 场景 99.20% 核心保留 + 94.80% 专家保留。
- **研究意义**：子空间约束合并是组合式能力整合的实用路线，无需额外训练。

## 研究背景与动机

### 领域现状
Transformer 基 LLM 越来越作为通用助手部署，但生产使用往往需要"广泛通用能力 + 高质量领域专业化"的艰难组合。标准做法是从强基座继续预训练或微调。模型合并（model merging）作为训练无关的替代方案，目标是在一个检查点里继承专家增益又不牺牲基座能力。

### 现有方法的局限性
- **直接参数操作**：model soup、SLERP、TIES、DARE-TIES 等直接操纵参数，不提供"哪些方向应该迁移"的数据条件化控制。
- **脆弱的权衡**：当源模型被调到不同分布时，容易产生脆弱的专家—通用权衡。
- **缺少显式优化**：缺乏面向"能力特定迁移"的显式、数据条件化的优化目标。

### 研究动机
引入 AMARIS，一个协方差驱动的合并框架，把迁移条件化到激活几何上：在每一层估计基模型与专家的激活协方差，选择"高专家激活捕获、低基模型干扰"的继承方向，在明确的预算下约束基模型输出扰动。

## 研究问题

### 核心研究问题
能否把检查点合并形式化为一个**预算化子空间继承**问题——在基模型输出扰动预算（由 α 控制）约束下最大化专家激活捕获，从而在无需额外训练的前提下，把迁移集中在"高效用、低干扰"的方向上？

## 方法概述

### 核心思想
对每个线性层，AMARIS 收集基模型偏好校准集上的层输入 $X_1$ 与专家偏好校准集上的 $X_2$，估计激活协方差 $C_1 = \frac{1}{N_1}X_1^\top X_1$、$C_2 = \frac{1}{N_2}X_2^\top X_2$，以及参数增量 $G = \Delta^\top \Delta$。合并形式为：

$$W_{merge} = W_1 + \Delta P$$

其中 $P$ 是正交投影器（$P = P^\top = P^2$）。只有被投影的输入分量 $XP$ 才受专家增量 $\Delta$ 影响，其余分量保持精确锚定在基模型 $M_1$。

![[2611.27369_fig1.png|600]]

> 图1：AMARIS 概览。每个检查点在其校准集上提供层输入统计；AMARIS 排序候选方向、在保留预算下选择子空间，并将投影后的增量折叠进静态检查点。

### 方法框架

#### 整体架构
1. **目标形式化**：最小化专家输出误差 $L_{specialist}(P) = \text{tr}((I-P)C_2(I-P)G)$，约束基模型扰动 $L_{base}(P) = \text{tr}(PC_1PG) \le \alpha\,\text{tr}(C_1G)$。
2. **激活能量代理**：由 $L_{base} \le \|\Delta\|_2^2\,\text{tr}(PC_1)$ 的界，得到可廉价排序的代理 $\max_{P=P^\top=P^2} \text{tr}(PC_2)$ s.t. $\text{tr}(PC_1) \le \alpha\,\text{tr}(C_1)$。
3. **方向打分**：单方向收益/代价比 $\rho(u) = \frac{u^\top C_2 u}{u^\top (C_1 + \eta I)u}$，其驻点满足广义特征问题 $C_2 u_i = \rho_i (C_1 + \eta I) u_i$。
4. **预算化贪心构造**：按估计特征值降序扫描候选方向，仅当扩大子空间仍满足保留预算 $\tau = \alpha\,\text{tr}(C_1G)$ 时才接受；接受的方向做欧氏正交化后构造 $P = UU^\top$。

#### 关键设计
- **数据条件化**：迁移方向由校准激活几何决定，而非全局插值系数。
- **显式控制旋钮 α**：α 直接控制允许的基模型扰动比例（α ∈ [0,1]）。
- **部署简单**：无可训练适配器、无学习路由器、无推理时条件分支，只有静态低秩投影门。

## 实验结果

### 实验目标
验证 AMARIS 在金融、指令遵循、多语言 SEA 三个场景下，达到有竞争力的"核心—专家"操作点（Pareto 前沿），且迁移具有统计显著性。

### 数据集与基线
- **合并对**：Qwen3-8B + ODA-Fin-RL-8B（金融）；Qwen3-8B + Light-IF-8B（指令遵循）；Qwen3-32B + Qwen-SEA-LION-v4-32B-IT（SEA）。
- **基线**：model soup、SLERP、TIES、DARE-TIES、RegMean、LEWIS 引导稀疏合并、线性插值等。

### 主要结果
- **8B 金融**：98.37% 专家保留 + 95.16% 核心保留。
- **8B 指令遵循**：103.55% 核心保留 + 90.28% 专家保留。
- **32B SEA**：99.20% 核心保留 + 94.80% 专家保留；在完整 SEAHELM 评估中，泰语较基座提升 4.34 分（部分迁移而非全量），且增益统计显著。

![[2611.27369_fig2.png|600]]

> 图2：核心—专家操作点。横轴 Core Avg. Preserved（%），纵轴 Specialist Preserved（%），三个子图对应 Finance、Instruction Following、SEA 三个场景。

![[2611.27369_fig3.png|600]]

> 图3：金融场景 α 扫描。左轴 AIME26，右轴 FPB，展示 α 预算对权衡曲线的控制作用。

### 结果分析
AMARIS 在三个设置中都达到 Pareto 操作点，且比强训练无关基线有竞争力。配对比对（paired-bootstrap）与 95% 置信区间分析表明增益统计显著。α 扫描展示了预算旋钮对"专家保留 vs 核心保留"权衡的平滑控制。

## 深度分析

### 研究价值评估
- **理论贡献**：把合并形式化为预算化子空间继承，给出显式目标 + 激活能量代理 + 广义特征方向排序的完整推导。
- **实践价值**：训练无关、部署简单（单静态检查点）、显式 α 控制，适合工程化能力组合。
- **领域影响**：为"组合式能力整合"提供了一条原则化、可扩展的路线。

### 方法优势
- **数据条件化控制**：相比直接参数操作的启发式方法，能按校准激活几何决定迁移方向。
- **显式预算**：α 是透明、可解释的控制旋钮，用户可自主权衡保留与迁移。
- **无需训练**：无适配器、无路由、无推理时开销。

### 局限性
- **依赖校准激活**：方法效果取决于校准集的质量与代表性。
- **单层/固定输入约束**：约束针对单层、固定输入，不构成端到端能力保证。
- **实验范围**：聚焦成对合并（pairwise merge）与共享架构/分词器的检查点。

## 我的综合评价

### 总体评分
**7.5/10** — 将模型合并形式化为预算化子空间继承，数学严谨、部署简单，是有价值的训练无关能力组合路线。

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 子空间约束合并 + 数据条件化控制是显著改进，但建立在成熟合并框架之上 |
| 技术质量 | 8/10 | 目标—代理—排序—贪心接受的推导严谨、自洽 |
| 实验充分性 | 8/10 | 三场景、多基线、配对 bootstrap 与置信区间，统计严谨 |
| 写作质量 | 8/10 | 结构清晰，动机与方法衔接自然 |
| 实用性 | 7/10 | 训练无关、静态检查点，便于工程落地；但需校准集 |

> [!tip] 关键启示
> 模型合并可以不做全局插值，而是"按激活方向有选择地继承专家更新"——用校准激活协方差决定哪些方向该迁移、哪些该保留。

> [!warning] 注意事项
> - 迁移方向与预算 α 对校准集质量敏感。
> - 约束是单层层面的，不等价于端到端能力保证。
> - 当前验证限于成对、同架构、同分词器的检查点合并。

## 相关论文
- [[20_Research/Papers/大语言模型/HiSA_Sparsifying_the_Hidden_Dimension_of_RoPE_Attention_in_LLMs|HiSA: Sparsifying the Hidden Dimension of RoPE Attention in LLMs]]
