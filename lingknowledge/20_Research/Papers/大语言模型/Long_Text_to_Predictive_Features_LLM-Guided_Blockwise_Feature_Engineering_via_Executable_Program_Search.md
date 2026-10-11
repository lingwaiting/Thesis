---
date: "2026-10-11"
paper_id: "arXiv:2610.12390"
title: "Long Text to Predictive Features: LLM-Guided Blockwise Feature Engineering via Executable Program Search"
authors: "Ziming Dai, Dabiao Ma, Ziheng Guo, Jack Dong, Zimu Zhou"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Feature-Engineering
  - Program-Synthesis
  - Financial-Risk-Control
  - LLM-Agent
quality_score: "8.3/10"
created: "2026-10-11"
updated: "2026-10-11"
status: analyzed
---

# Long Text to Predictive Features: LLM-Guided Blockwise Feature Engineering via Executable Program Search

## 核心信息
- **论文ID**：arXiv:2610.12390
- **作者**：Ziming Dai, Dabiao Ma, Ziheng Guo, Jack Dong, Zimu Zhou
- **机构**：香港城市大学 + Qfin Holdings + 天津大学 + 卡内基梅隆大学
- **发布时间**：2026-10-08
- **会议/期刊**：arXiv 预印本（cs.LG / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2610.12390) | [PDF](https://arxiv.org/pdf/2610.12390)
- **引用**：--

## 摘要翻译

### 英文摘要
Industrial risk-control systems typically rely on structured-data models for efficient prediction, yet substantial valuable information remains embedded in unstructured long text. Extracting this information through manual feature engineering is labor-intensive, while requiring a large language model (LLM) to process every real-time input may not meet practical deployment requirements. To address this challenge, we propose LLM-BlockFE, an LLM-guided offline feature construction framework that converts long text into executable feature programs, thereby avoiding LLM calls during online inference. LLM-BlockFE constructs feature programs by incrementally appending immutable code blocks and evaluates candidate features using a downstream model. To address the tendency of conventional greedy search to become trapped in suboptimal solutions, our method introduces a block-level rollback mechanism based on depth-calibrated credit allocation and advances multiple independent search trajectories in an interleaved manner, reducing redundant exploration by sharing fixed descriptions of each trajectory's exploration direction. After the search, the resulting programs are frozen and deployed to extract structured features for downstream prediction models. Across two public and two private datasets, LLM-BlockFE achieves absolute AUC improvements of 0.0069 to 0.0358 over the strongest baseline on each dataset in the full-dataset comparison. Post-launch monitoring across five deployed financial risk-control applications shows absolute KS improvements of 0.02 to 1.56 percentage points over the existing manually designed strategy.

### 中文翻译
工业风控系统通常依赖结构化数据模型进行高效预测，但大量有价值的信息仍隐藏在非结构化长文本中。通过人工特征工程提取这些信息耗时费力，而要求大语言模型（LLM）处理每一个实时输入又难以满足实际部署要求。为解决这一挑战，我们提出 **LLM-BlockFE**，一个 LLM 引导的离线特征构建框架，将长文本转化为可执行的特征程序，从而在在线推理时完全避免调用 LLM。LLM-BlockFE 通过增量追加不可变代码块来构建特征程序，并用下游模型评估候选特征。为缓解传统贪心搜索易陷入次优解的问题，我们的方法引入基于"深度校准的信用分配"的块级回退机制，并以交错方式推进多条独立搜索轨迹，通过共享各轨迹探索方向的固定描述来减少冗余探索。搜索结束后，所得程序被冻结并部署，用于为下游预测模型提取结构化特征。在两个公开和两个私有数据集上，LLM-BlockFE 在全量数据集对比中，相对每个数据集上的最强基线取得了 0.0069 至 0.0358 的绝对 AUC 提升。五个已部署金融风控应用的上线后监控显示，相对现有手工设计策略，绝对 KS 提升 0.02 至 1.56 个百分点。

### 核心要点提炼
- **研究背景**：工业风控用结构化模型（XGBoost 等）做高效预测，但长文本（客服通话、备注等）中富含风险信号却难以直接利用。
- **研究动机**：人工特征工程费力，逐样本调用 LLM 又带来在线延迟与开销；需要"离线用 LLM 发现特征、在线零 LLM 调用"的方案。
- **核心方法**：把长文本特征构建形式化为"离线合成可执行程序"，用"完整生成 + 增量提取"的块级更新 + 深度校准信用引导的回退 + 多轨迹交错搜索。
- **主要结果**：4 个数据集 AUC 提升 0.0069–0.0358；5 个真实风控应用上线后 KS 提升 0.02–1.56 pp。
- **研究意义**：为"把非结构化长文本信息融入工业结构化预测"提供了可解释、可维护、零在线 LLM 成本的实用范式。

## 研究背景与动机

### 领域现状
高 stakes 工业场景（如金融风控）要求模型高精度且满足推理延迟与可维护性约束，因此 XGBoost/LightGBM 等结构化模型仍被广泛使用。同时，长文本（客服通话记录、备注、信用报告等）日益成为重要信息源，但其非结构化形式难以直接用于结构化模型。现有文本表示方法（TF-IDF、密集向量、长序列编码器）要么语义能力有限，要么输出不可解释、仍需进一步处理。

### 现有方法的局限性
- **人工特征工程**：需领域专家持续设计清洗规则、解析逻辑与特征计算程序，随口语化、省略、复述、跨句语义变化而需不断扩展维护，成本高。
- **LLM 逐样本提取**：即使特征定义离线确定，对持续到达、需即时评分的新样本仍需在线 LLM 推理，带来延迟与计算开销，依赖在线服务基础设施。
- **现有 AutoFE**：主要对已有结构化列做变换/组合搜索，未直接解决"从原始长文本构建特征"的问题。

### 研究动机
把 LLM 的语义推理与代码生成能力**仅用于离线阶段**去发现特征并编写提取程序，在线阶段只执行冻结程序，实现"离线特征发现与在线计算解耦"。

## 研究问题

### 核心研究问题
如何自动从非结构化长文本构建紧凑、可解释、易部署的结构化特征，且在线阶段零 LLM 调用、特征逻辑可审查可维护？

## 方法概述

### 核心思想
把长文本特征构建形式化为"可执行程序搜索"：LLM 以"完整程序"为单位生成候选、用标记（`<BEGIN>...<END>`）提取新增代码块，保证增量更新与可回退；用深度校准的信用机制判断是继续扩展还是回退到父程序；多轨迹交错搜索并共享探索方向以减少冗余。

### 方法框架

#### 整体架构
LLM-BlockFE 三组件：① 下游引导的块构建（完整生成 + 增量提取 + 块内贝叶斯优化）；② 深度校准的信用引导回退（估计节点扩展出有效子节点的机会）；③ 异步多程序构建（多轨迹交错、共享方向描述）。搜索结束后冻结各轨迹最优程序，在线零 LLM 调用。

![[2610.12390_fig1.png|600]]

> 图1：LLM-BlockFE 总览——离线搜索文本特征程序，在线执行冻结程序进行预测。

#### 关键组件说明

**组件1：下游引导的块构建**
- 从种子程序 $P_0$ 出发，当前程序 $P_n=P_0\oplus b_{n,1}\oplus\cdots$。
- LLM 接收完整程序 + 少量文本样本 + 任务描述 + 工具 + 历史摘要，生成完整候选代码并用标记标出新增区域；系统用正则精确提取新增块 $b$，并严格检查标记外代码是否被非法修改。
- 候选先过执行检查，再做文本 AUC 粗筛，含可调参数则用贝叶斯优化调参（只影响新增块），最后用轻量 XGBoost 在下游验证集上算条件增益 $\Delta_{n,b}$，正增益才接受冻结。

![[2610.12390_fig2.png|600]]

> 图2：种子程序与 LLM 生成候选程序示意（完整生成 + 标记提取新增块）。

**组件2：深度校准的信用引导回退**
- 每个搜索轨迹是一棵以 $P_0$ 为根的搜索树，节点深度 = 已接受块数。
- 用经验贝叶斯部分池化估计"同深度基线成功率"，再用 Beta-Bernoulli 后验估计节点在接下来 H 次直接尝试中产生至少一个有效子节点的概率 $Q_b(H)$。
- 定义块信用 $C(b)=\log(Q_b(H)/Q_d^0(H))$；当相对机会 $R_b$ 低于阈值 ρ 时回退一个末端块，从父节点探索新分支。

**组件3：异步多程序构建**
- 多轨迹交错推进，各自维护搜索路径、块历史、回退状态与最优程序存档。
- 轨迹之间只共享"探索方向描述"（固定方向约束主探索目标，允许相同关键词/工具），避免重复探索。

## 实验结果

### 实验目标
验证 LLM-BlockFE 在公开与私有长文本数据集上的预测性能、推理效率、部署影响，以及各设计组件的贡献。

### 数据集与基线
- **数据集**：CFPB-Timely、GossipCop（公开），Remark、Report（Qfin 私有，各 10 万样本）。
- **基线**：TF-IDF(+LR/XGB)、TextStats、Embedding、OpenFE、CAAFE、OCTree、LLM-FE、FELIX。下游模型主要为 XGBoost，生成模型用 Seed-OSS-36B-Instruct。

### 主要结果

- **AUC 主对比**：Remark 0.6804（+0.0358）、Report 0.5545（+0.0209）、CFPB-Timely 0.8881（+0.0152）、GossipCop 0.9472（+0.0069），均超最强基线。
- **效率**：毫秒级在线推理，相比 FELIX 降延迟数个数量级（LLM 全部离线）。
- **线上部署**：5 个风控应用、15 个"应用-月"对比中 KS 全部提升，绝对提升 0.02–1.56 pp；A/B 测试覆盖约 10 亿元放款，坏账率从 8.0% 降至 7.7%。
- **消融**：移除下游 AUC 门控影响最大（AUC 掉 0.0492）；append-only 约束、贝叶斯优化、回退、信用引导、深度校准、跨程序共享均各自贡献。
- **泛化**：生成特征对 XGBoost/LightGBM/LR/MLP 均有效；换用 Qwen3.5-27B、DeepSeek-R1 也能提升性能。

### 结果分析
LLM-BlockFE 的关键优势在于"完整的架构解耦"：把 LLM 的复杂语义理解与特征搜索**完全限定在离线阶段**，在线只顺序执行冻结程序。SHAP 分析显示生成特征在私有数据集上贡献了 41.4%（Remark）/61.1%（Report）的归因，说明其捕获了结构化特征之外的有效信息。案例研究进一步展示了特征程序的可解释性——规则逻辑与阈值可直接追溯。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：把"长文本特征工程"形式化为"离线可执行程序合成"，给出完整程序生成 + 不可变块级更新的协议。
  - 创新点：为增量构建与局部回退提供显式操作边界，兼顾程序整体一致性与局部可逆性。
- **贡献2**：提出深度校准的信用引导回退机制，分离"当前程序预测性能"与"进一步扩展的机会"。
  - 创新点：用经验贝叶斯部分池化估计同深度基线，减少单路径搜索对早期选择的依赖。

#### 实际应用价值
- **应用场景1**：金融风控等工业预测——从长文本中挖掘风险信号，零在线 LLM 成本，可审查可维护。
- **应用场景2**：任何需要把非结构化文本融入结构化模型、且对延迟敏感的生产系统。

### 局限性
- 聚焦二分类金融风险预测，虽声称可扩展到其他监督学习，但多分类/回归未充分验证。
- 搜索依赖 LLM 生成质量与下游模型反馈，成本（token 消耗）与搜索预算的关系未深入讨论。
- 特征程序的可解释性依赖代码可读性，复杂程序仍可能难以直接审查。

### 未来工作
- 扩展到多分类、回归及其他非金融领域。
- 探索更高效的搜索策略以降低离线 LLM 调用成本。
- 结合更强的程序验证/可解释性工具，进一步保障生成特征代码的可靠性。

### 与相关工作对比
- **vs FELIX / Balek 等**：这些方法仍需 LLM 逐样本提取特征值，在线有额外推理开销；LLM-BlockFE 离线生成程序、在线零 LLM 调用，延迟降低数个数量级。
- **vs OpenFE / CAAFE / LLM-FE**：这些 AutoFE 方法主要对已有结构化列做变换/组合；LLM-BlockFE 直接针对"非结构化长文本 → 结构化特征"这一被忽视的问题。
- **研究路线归属**：属于"LLM 用于自动特征工程"与"程序合成"的交叉，强调工业可部署性与可解释性。
