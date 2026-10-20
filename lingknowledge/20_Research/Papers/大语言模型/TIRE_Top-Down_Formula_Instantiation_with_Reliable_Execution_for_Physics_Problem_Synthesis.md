---
date: "2026-10-20"
paper_id: "arXiv:2610.25134"
title: "TIRE: Top-Down Formula Instantiation with Reliable Execution for Physics Problem Synthesis"
authors: "Xiaoyu Wu, Zhaoxin Yu, Xinglin Xiao, Nan Xu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - Physics-Reasoning
  - Data-Synthesis
  - Program-Guided
  - Science-Reasoning
quality_score: "8.5/10"
related_papers: []
created: "2026-10-20"
updated: "2026-10-20"
status: analyzed
---

# TIRE: Top-Down Formula Instantiation with Reliable Execution for Physics Problem Synthesis

## 核心信息
- **论文ID**：arXiv:2610.25134
- **作者**：Xiaoyu Wu, Zhaoxin Yu, Xinglin Xiao, Nan Xu
- **机构**：ScienceOne AI、中国科学院自动化研究所（State Key Laboratory of Multimodal AI Systems, CASIA）、中国科学院大学
- **发布时间**：2026-10-18
- **会议/期刊**：arXiv（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.25134) | [PDF](https://arxiv.org/pdf/2610.25134)
- **引用**：--
- **代码/数据**：[GitHub](https://github.com/ScienceOne-AI/TIRE) | [HuggingFace](https://huggingface.co/datasets/ScienceOne-AI/TIRE)

## 摘要翻译

### 英文摘要
Advancing physics reasoning in large language models requires scalable training data that connect physical laws, concrete problem scenarios, and verifiable numerical answers. Expert-designed problems offer top-down control over physical laws, conditions, and scenarios, but require costly human effort. LLM-based synthesis from texts scales efficiently, but model-generated answers and assessments leave supervision vulnerable to hallucinations. We introduce TIRE, a framework for Top-Down Formula Instantiation with Reliable Execution that brings knowledge-driven problem construction to large-scale data generation. Top-down instantiation uses reusable physical formulas and their applicability conditions to control the physical relations and numerical configurations of generated problems. Reliable execution establishes reference answers before problem generation, providing a common numerical foundation for problem construction and teacher distillation. Together, these mechanisms combine control over problem content with execution-grounded supervision for both supervised fine-tuning and reinforcement learning. Using TIRE, we construct TIRE-19k, spanning nine subfields of physics. Across 3 backbone LLMs and 4 benchmarks, TIRE-19k outperforms 5 representative datasets with equal amounts of SFT data, exceeding the strongest baseline by 2.86 percentage points on average. Further GRPO raises Qwen3-8B's average accuracy by 1.52 percentage points over its TIRE-19k SFT checkpoint.

### 中文翻译
提升大语言模型的物理推理能力需要可扩展的训练数据，这些数据要能把物理定律、具体问题场景和可验证的数值答案连接起来。专家设计的问题对物理定律、条件与场景具有自顶向下的控制力，但人工成本高昂。基于文本的 LLM 合成可高效扩展，但模型生成的答案与评估容易受幻觉影响。本文提出 TIRE——"自顶向下公式实例化 + 可靠执行"框架，把知识驱动的题目构建引入大规模数据生成。自顶向下实例化使用可复用的物理公式及其适用条件来控制生成题目的物理关系与数值配置；可靠执行在题目生成之前就确立参考答案，为题目构建与教师蒸馏提供统一的数值基础。二者结合，兼顾了对题目内容的控制与面向 SFT/RL 的执行落地监督。基于 TIRE，作者构建了 TIRE-19k，覆盖九个物理子领域。在 3 个 backbone LLM 与 4 个基准上，TIRE-19k 在等量 SFT 数据下超越 5 个代表性数据集，平均比最强基线高 2.86 个百分点；进一步的 GRPO 使 Qwen3-8B 的平均准确率在 TIRE-19k SFT checkpoint 之上再提升 1.52 个百分点。

### 核心要点提炼
- **研究背景**：物理推理既考验形式化数学推导，也要求物理场景、适用定律、有效条件与形式模型之间的一致性，因而比纯数学更难。
- **研究动机**：专家出题可控但难扩展；LLM 生成可扩展但易幻觉、缺乏独立数值参照——需兼顾"自顶向下控制"与"可扩展生成 + 可靠监督"。
- **核心方法**：TIRE 三模块（Physical Knowledge / Top-Down Formula Instantiation / Teacher Distillation），用公式实例化控制题目内容，用程序执行生成参考答案做监督。
- **主要结果**：TIRE-19k（19,144 例、9 个子领域、3,051 条公式）在 3 backbone×4 benchmark 上平均超越最强基线 2.86pp，GRPO 再加 1.52pp。
- **研究意义**：为科学推理（尤其物理）提供了"从显式物理知识生成训练数据"的可验证合成范式，可推广到其他学科。

## 研究背景与动机

### 领域现状
物理问题求解要求模型把自然语言场景翻译为物理模型，再经符号推导与数值计算得到可验证答案，是评估科学推理的重要任务，也关乎智能教育、工程分析、科研辅助等落地场景。当前模型在物理推理上的短板，很大程度上源于高质量、可验证训练数据的稀缺。数学领域已建立起系统化的可验证数据生成 pipeline（先构造编码合法解过程的程序/计算图/形式证明，再转为自然语言题目，最后用确定性验证器核对），但物理更复杂——它额外要求真实物理场景、适用定律、有效条件与形式模型之间的多一致。

### 现有方法的局限性
现有物理数据构建有两条路线：一条依赖教科书/试卷/题库的专家出题，自顶向下、可控但人工成本高、难以规模化；另一条让 LLM 直接从文本生成或补全题目与答案，可扩展但易引入幻觉，且质量评估往往依赖 LLM 本身、缺乏独立于物理的数值参照。物理定律与参数取值也受源文本与模型偏好限制，难以控制题目构造。

### 研究动机
挑战在于把"来自物理知识的自顶向下构造"与"可扩展生成 + 程序执行落地的可靠监督"结合起来。TIRE 正是为此设计：让可复用公式与其适用条件显式地驱动数值实例化与后续自然语言题目构造，同时让确定性程序输出在生成与选择两个环节充当共享的数值参照。

## 研究问题

### 核心研究问题
1. 如何把结构化物理知识（公式 + 适用条件）转化为可控、可扩展的物理训练数据？
2. 如何用程序执行在题目生成之前确立参考答案，从而为 SFT 与 RL 提供独立于模型的数值监督？
3. 这样生成的数据能否在等量训练数据下超越现有物理数据集，并支持进一步的 GRPO 增益？

## 方法概述

### 核心思想
TIRE 从物理公式编码的领域知识出发，自顶向下地生成物理求解训练数据。给定公式集合 F，构造数据集 D = {(q_i, τ_i, y_i*)}，其中 q_i 是自然语言题目、y_i* 是执行推导出的参考答案、τ_i 是最终答案与 y_i* 一致的教师推理轨迹。LLM 负责实例化物理关系与表达题目的语义决策，程序输出则在生成与选择之间充当共享的数值参照。

### 方法框架

#### 整体架构
TIRE 由三个模块构成：Physical Knowledge（提供可复用关系与适用条件）、Top-Down Formula Instantiation（把关系转化为数值题目）、Teacher Distillation（经数值答案验证筛选推理轨迹）。程序化操作贯穿其中：Program Validation 核对公式实现、Instance Execution 计算参考答案、Answer Verification 筛选教师轨迹。

![[TIRE_system_architecture_page1.png|600]]

> 图：TIRE 整体架构。蓝色块为 LLM 模块，绿色块为程序化操作；实线箭头表示数据流，虚线箭头表示知识条件化。执行推导的答案同时支撑题目构建与教师答案验证。

#### 各模块详细说明

**模块1：Physical Knowledge（物理知识获取）**
- **功能**：从源题目与官方解中抽取通用物理关系，构建公式库 F。
- **输入**：PHYSICS 与 SciBench 物理子集的题目及解答（仅作抽取素材，不作为训练样本）。
- **输出**：公式记录 φ = (n, e, d, r)——名称 n、方程 e: y=g(x)、物理描述 d、适用条件 r。
- **关键技术**：用 DeepSeek-R1 抽取；LLM 合理性过滤检查方程良构性与字段一致性，去重后形成公式库，使每条关系及其物理适用范围显式化。

**模块2：Top-Down Formula Instantiation（公式实例化）**
- **功能**：把公式关系转化为数值题目。
- **输入**：公式记录 φ。
- **输出**：通过验证的可执行程序 P_φ 及其测试集 T_φ，以及由实例化参数生成的自然语言题目。
- **关键技术**：Program Synthesiser 构造把独立输入映射到 y 的程序并内置物理常数、记录输入/输出含义与单位；测试输入落在公式适用域内，期望输出由显式公式推导式计算（不调用被测程序），Program Validation 仅保留全部测试通过的程序；随后实例化具体参数值并据此构造题目。

**模块3：Teacher Distillation（教师蒸馏）**
- **功能**：生成答案经过验证的教师推理轨迹。
- **输入**：生成题目 + 执行参考答案。
- **输出**：最终答案与参考答案一致的教师轨迹 τ_i。
- **关键技术**：教师独立求解生成题目，通过"最终答案与执行参考一致"来筛选推理轨迹，为 SFT 提供监督、为 GRPO 提供奖励参照。

### 关键创新
1. **知识驱动的自顶向下构造**：用公式记录（含适用条件）显式控制物理关系与数值配置，兼顾可控性与可扩展性。
2. **先执行、后出题**：在题目生成前用程序执行确立参考答案，为题目构建与教师蒸馏提供统一、独立于模型的数值基础。
3. **执行落地监督贯穿 SFT 与 RL**：同一参考既筛教师轨迹（SFT），又作 GRPO 奖励参照，闭环一致。

## 实验结果

### 实验设置
- **数据集**：TIRE-19k（19,144 例，覆盖 9 个物理子领域，基于 3,051 条公式记录）。
- **backbone**：3 个 LLM（含 Qwen3-8B 等）。
- **基准**：4 个物理推理基准，评估 in-domain 与 out-of-domain 泛化。
- **对照**：5 个代表性物理数据集（等量 SFT 数据）。

### 主要结果
- TIRE-19k 在 3 backbone × 4 benchmark 上超越 5 个代表性数据集，平均比最强基线高 **2.86pp**。
- 进一步的 GRPO 使 Qwen3-8B 平均准确率在 TIRE-19k SFT checkpoint 之上再提升 **1.52pp**。
- 消融实验验证了框架各组件的贡献；教师敏感性实验表明该方法对多种教师 LLM 均有效。

### 结果分析
TIRE 的增益来源清晰：自顶向下实例化保证了题目内容的物理正确性与数值可控性，可靠执行保证了监督信号的独立性（不受模型幻觉污染）。这使其既能作为高质量 SFT 数据，又能为 RL 提供干净的奖励参照，从而在等量数据下稳定领先，并支持后续强化学习进一步增益。

## 深度分析

### 研究价值
- **理论贡献**：为科学推理数据合成提供了"知识驱动 + 执行落地"的统一范式，可迁移到化学、生物等其他数理学科。
- **实际应用**：TIRE-19k 是可直接使用的物理推理训练数据，代码与数据全开源，落地价值高。
- **领域影响**：呼应数学领域"程序引导可验证数据合成"的成熟路线，补上了物理这一更复杂场景的空白。

### 优势
- 自顶向下控制 + 可扩展生成 + 可靠执行的组合，精准解决了"可控性"与"可扩展性"的两难。
- 先执行、后出题的设计巧妙，彻底绕开了"模型自评幻觉"这一监督污染源。
- 数据、代码、配置全开源，可复现性强。

### 局限性
- 依赖源公式库的质量与覆盖面，公式抽取环节本身仍依赖 LLM，可能引入偏差。
- 覆盖 9 个子领域但仍以基础物理为主，对更高阶/开放物理问题（含实验设计、定性推理）覆盖有限。
- 增益主要验证于 SFT/GRPO 的中小模型，更大规模模型与更多 RL 算法的泛化性待进一步验证。

### 适用场景
- 物理及更广泛科学推理的 SFT 与 RL 训练数据增强。
- 需要"可验证、可控制"训练数据的智能教育、科研辅助场景。

## 与相关论文对比

相比专家出题路线（PHYSICS、SciBench 等），TIRE 用公式实例化实现了可扩展的自顶向下控制；相比 LLM 文本合成路线（如 FormulaReasoning、RV-Syn、AMD 等程序引导合成），TIRE 的关键差异在于"物理适用条件"被显式纳入公式记录，且"先执行、后出题"确立了独立于模型的参考答案。它把数学领域成熟的"程序引导可验证合成"范式，适配到了物理这一要求"场景-定律-条件-模型多一致"的更复杂场景。

## 技术路线定位

本文属于**科学推理 LLM 的可验证数据合成**方向，位于"程序引导数据生成—执行落地监督—RL 增益"技术路线的中游：它把领域知识显式化为公式库，用确定性执行贯穿生成与选择，为下游 SFT 与 RL 提供高质量、可验证的训练数据。

## 未来工作建议

1. **作者建议**：扩展到更多物理子领域与更高阶问题类型。
2. **基于分析的延伸**：将公式库扩展到化学、生物等学科，验证范式的跨学科迁移能力。
3. **方法延伸**：研究公式实例化与更强 RL 算法（如过程奖励模型）的结合，进一步提升推理质量。

## 我的综合评价

### 价值评分
- **总体评分**：**8.5/10** — 工程扎实、数据可复现、增益明确，是科学推理数据合成方向的优质工作。
- **分项评分**：
  - 创新性：8/10 — 把数学领域范式适配到物理，思路清晰但非全新范式。
  - 技术质量：9/10 — "先执行、后出题"的设计干净，程序化监督闭环完整。
  - 实验充分性：8/10 — 3 backbone×4 benchmark、消融、教师敏感性，覆盖较全。
  - 写作质量：8/10 — 结构清晰，附录完整。
  - 实用性：9/10 — 数据与代码全开源，直接可用。

### 突出亮点
- "先执行、后出题"彻底切断模型幻觉对监督的污染。
- 显式纳入物理适用条件，可控性比一般程序引导合成更强。
- 数据全开源，GRPO 再加码，给出完整的 SFT→RL 路径。

### 重点关注
- 公式抽取环节的 LLM 偏差如何影响数据质量。
- "执行参考答案"在开放/多解物理问题上的适用边界。

### 可借鉴点
- "领域知识显式化 + 确定性执行作共享参照"是可复用于多学科数据合成的通用框架。
- 把"适用条件"作为一等领域对象纳入构造，是提升可控性的关键设计。

### 批判性思考
- 公式驱动天然偏向"可数值化"的物理问题，是否会遗漏"定性推理/概念理解"这一物理能力的重要维度？
- 教师轨迹仅按"最终答案一致"筛选，过程推理的正确性是否得到充分保证？

## 相关论文
- [[Characterising_and_Analysing_Retrieval-Augmented_Generation_Techniques|Characterising RAG Techniques]]

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.25134)
- [PDF](https://arxiv.org/pdf/2610.25134)
- [GitHub](https://github.com/ScienceOne-AI/TIRE)
- [HuggingFace 数据集](https://huggingface.co/datasets/ScienceOne-AI/TIRE)

> [!tip] 关键启示
> 科学推理数据的质量瓶颈在于"监督信号的独立性"——把领域知识显式化为公式库、在出题前用程序执行确立参考答案，就能同时获得可控的题目内容与不受幻觉污染的可验证监督。

> [!warning] 注意事项
> - 公式库的抽取仍依赖 LLM，源数据的偏差会传递到生成数据。
> - 方法天然偏向可数值化的物理问题，对定性推理覆盖有限。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 强烈推荐！为物理科学推理的数据合成提供了可复现、可扩展、可验证的范式，数据与代码全开源，可直接复现。
