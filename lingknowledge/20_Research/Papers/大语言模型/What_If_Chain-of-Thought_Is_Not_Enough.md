---
date: "2026-11-21"
paper_id: "arXiv:2611.26282"
title: "What If Chain-of-Thought Is Not Enough?"
authors: "Marc Jansen"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Chain-of-Thought
  - Reasoning
quality_score: "7.0/10"
created: "2026-11-21"
updated: "2026-11-21"
status: analyzed
---

# What If Chain-of-Thought Is Not Enough?

## 核心信息

| 项目 | 内容 |
| --- | --- |
| 论文 ID | arXiv:2611.26282 |
| 作者 | Marc Jansen（单一作者） |
| 机构 | 德国鲁尔西部应用科学大学 计算机科学研究所（Computer Science Institute, University of Applied Sciences Ruhr West, Bottrop, Germany） |
| 发布时间 | 2026-11-19 |
| 分类 | cs.CL, cs.AI |
| 链接 | [arXiv](https://arxiv.org/abs/2611.26282) \| [PDF](https://arxiv.org/pdf/2611.26282) |

## 摘要翻译

**英文原文**

> Reasoning capabilities in large language models have evolved from prompting-based methods to internalized model behaviors. In this paper, we investigate whether the internal thinking model of an LLM can be deliberately changed and whether such a change is beneficial. Instead of relying on Chain-of-Thought, we train models to follow Question--Option--Criteria (QOC), a structured decision process that decomposes a task into explicit questions, competing options, and evaluation criteria. We present a three-generation development pipeline that ranges from a basic QOC decision core to richer answer synthesis and statistically enriched QOC reasoning. We then evaluate the resulting models with respect to both internal consistency and output quality. Across the reported experiments, QOC-based fine-tuning improves answer quality relative to non-fine-tuned base models and yields particularly strong gains once models are large enough to execute the underlying QOC computations reliably. The results suggest that the internal thinking model of an LLM is trainable and that structured alternatives to Chain-of-Thought can provide a promising path toward more reliable, interpretable, and task-adapted decision support.

**中文翻译**

> 大语言模型的推理能力已从基于提示的方法演变为内化于模型本身的行为。本文探究了一个问题：LLM 的"内部思维模型"能否被刻意改变，以及这种改变是否有益。作者不依赖思维链（Chain-of-Thought），而是训练模型遵循"问题—选项—准则"（Question–Option–Criteria，QOC）这一结构化决策流程，将任务分解为明确的问题、相互竞争的选项与评估准则。文中提出了一条三代开发管线，从基础的 QOC 决策核心逐步扩展到更丰富的答案合成，再到带有统计增强的 QOC 推理。随后，作者从内部一致性和输出质量两个维度对所得模型进行评估。实验表明，基于 QOC 的微调相对于未微调的基础模型提升了答案质量，且当模型规模大到足以可靠地执行底层 QOC 计算时，增益尤为显著。结果表明：LLM 的内部思维模型是可训练的，结构化地替代思维链的方法有望为更可靠、可解释、任务适配的决策支持开辟一条有前景的路径。

**核心要点提炼**

- 核心主张：LLM 的"内部思维模型"不是固定的，可以通过微调被刻意重塑。
- 关键手段：用 QOC（问题—选项—准则）结构化决策流程取代自由形式的 CoT 推理。
- 三代管线：Gen1 纯 QOC 决策核心 → Gen2 增加答案合成/解释层 → Gen3 增加统计增强。
- 双重评估：内部一致性（QOC 计算是否正确）+ 输出质量（LLM-as-a-Judge 对比）。
- 关键结论：QOC 微调显著优于同尺寸基础模型，且规模越大内部一致性越强。

## 研究背景与动机

大语言模型的推理能力演进经历了清晰的阶段迁移：早期依靠 **CoT 提示**（如 [[Chain-of-Thought Prompting|Wei et al. 2022]]、[[Large Language Models are Zero-Shot Reasoners|Kojima et al. 2022]]）让"不会思考"的基础模型把中间推理步骤外化出来；随后这些被外部诱导的推理轨迹又反过来成为训练信号，通过 STaR（[[STaR Self-Taught Reasoner|Zelikman et al. 2022]]）和逐步蒸馏（[[Distilling Step-by-Step|Hsieh et al. 2023]]）让模型"内化"结构化推理行为。

本文沿着同一条发展逻辑推进，但做了一个关键替换：**把 CoT 换成更具显式决策导向的 QOC 框架**（源自 [[Questions, Options, and Criteria|MacLean et al. 1991]] 的设计空间分析）。

核心假设是：QOC 在决策支持类任务上提供更好的脚手架，因为它强制分离三个要素——

1. **决策问题（Question）**：要回答什么；
2. **竞争选项（Options）**：在哪些备选之间抉择；
3. **评估准则（Criteria）**：按什么维度评判选项优劣。

这种结构在"可解释性"至关重要的场景尤其有价值：模型不只输出一个最终推荐，还能暴露"为什么某些选项占优、权衡在哪里发生"，这契合 AI 决策支持中可解释性的目标（[[Explanation in AI|Miller 2019]]、[[Towards a Rigorous Science of Interpretable ML|Doshi-Velez & Kim 2017]]）。

## 研究问题

1. **能否刻意改变 LLM 的内部思维模型？** —— 不追求泛泛的推理啰嗦度，而是训练模型内化一个特定的结构化决策流程（QOC）。
2. **这种改变是否有益？** —— 既考察内部一致性（QOC 计算是否正确），也考察外部输出质量（是否被判定为更优）。
3. **思维模型的深度是否与模型规模适配？** —— 更深的内部思维结构是否对大小模型同样有用，还是不同规模受益于不同深度的结构化推理？

## 方法概述

### 核心思想

QOC 作为**内部思维模型**（而非仅仅是输出格式）被训练进 LLM。与 CoT 的自由形式顺序推理不同，QOC 强制将决策分解为可检查、可验证、可复用的中间结构。

### QOC 决策流程

给定一个由"问题 + 支持上下文"组成的输入任务，模型执行如下固定分析流程：

1. **明确决策问题**：从输入中提炼出必须回答的具体决策问题；
2. **识别选项**：枚举相互竞争的备选方案；
3. **提取/构造准则**：确定评判这些选项的相关评估维度；
4. **分配准则权重**：为每个准则赋重要性权重 $w_j$，表示它对最终决策的影响强度；
5. **逐项打分**：对每个选项在每个准则下评分 $s_{ij}$；
6. **加权求和**：对每个"选项—准则"对，将准则权重乘以选项得分，再跨准则求和，得到每个选项的总分：

$$Score_i = \sum_{j} w_j \cdot s_{ij}$$

7. **选出最优选项**：总分最高的选项即为推荐答案。

这一流程把定性判断转化为结构化排序，使推荐结果由显式的中间计算"推导"出来，而非隐式选出——这正为评估内部一致性提供了抓手：可检查准则是否合理、加权与打分是否正确、乘积与总和是否无误、得分最高者是否真的被选为推荐。

### 三代开发管线

三代共享同一条骨干管线：**合成数据生成 → 结构化 QOC 分析 → 监督微调**。每代只在"答案合成阶段额外产生和使用哪些产物"上有所差异。

> **Gen1 — QOC 纯决策核心**
> 仅使用共享核心管线：QOC 智能体生成推理块并选出最优选项，训练与推理的输出最小化——只返回最优选项，无额外解释、无叙述。

![[arch_gen1.png|600]]

> 图1：第一代管线生成合成决策任务，应用结构化 QOC 分析识别最优选项，并直接输出该最优选项。

> **Gen2 — QOC + 解释层**
> 在最优选项选择后增加一个专门的解释组件：将 QOC 思维块连同识别出的最优选项，在第二步传回 LLM（与生成该结构的同一模型家族），让其解读结果并生成更丰富、面向用户、且以 QOC 结构为根据的答案。训练时两个产物都被保留：中间 QOC 推理表示 + 更丰富的文本回答。

![[arch_gen2.png|600]]

> 图2：第二代架构整合了一个第二阶段解释模块，合成出以结构化 QOC 结果为根据的、更丰富的自然语言回答。

> **Gen3 — QOC + 统计增强 + 解释**
> 在 QOC 分析与最终答案合成之间加入统计增强组件：除 QOC 块和最优选项外，系统还计算选项表现上的简单统计指标（如跨准则得分的标准差等离散度指标，反映稳定性与敏感性）。这些统计量连同 QOC 结果与最优选项一并提供给 LLM，使最终回答能同时纳入结构化决策逻辑与定量比较证据。

![[arch_gen3.png|600]]

> 图3：第三代管线在 QOC 决策核心之外引入统计增强，最终回答融合结构化决策逻辑与定量比较证据。

### 实现管线

实现层面分为五个层次，端到端地把决策任务转化为监督微调样本。合成数据由当前配置下最强的开源权重模型生成——本文中由 **Qwen3.5-27B** 扮演这一角色，以最大化推理轨迹与监督信号的质量。

![[implementation_pipeline.png|600]]

> 图4：QOC 训练工作流的实现管线，从任务构造、QOC 处理到训练数据准备与模型微调。

五个层次：

1. **任务生成层（Task Generation）**：Python 脚本从不同领域构造合成决策任务（问题 + 合成上下文），避免单一窄域。
2. **QOC 处理层（QOC Processing）**：对每个任务执行 QOC 流程——确定决策问题、枚举选项、推导准则、赋权重、逐项打分，加权聚合出最优选项。
3. **产物构造层（Artifact Construction）**：把中间推理输出转化为结构化产物。深度依代而异——Gen1 含选项/准则/权重/支撑度、加权乘积与总和；Gen2 增加二次 LLM 生成的、以 QOC 为根据的详细答案；Gen3 再增统计度量。
4. **微调数据准备层（Fine-Tuning Data Preparation）**：把产物组装为监督样本。QOC 分析被注入响应的"思考块"，基于 QOC 生成的答案被注入为"最终答案"，确保模型不是只学最终答案，而是学反映 QOC 推理结构的输出。
5. **模型微调层（Model Fine-Tuning）**：用样本微调 Qwen3.5 各尺寸基础模型，使其从通用行为转向以显式 QOC 分析为根基的结构化决策。

## 实验结果

### 数据集与评估场景

- **基准集**：100 道示例问题，每题配上下文，来自 QOC 适用的不同决策领域。
- **模型家族**：Qwen3.5 全尺寸——0.8B、2B、4B、9B、27B。
- **合成数据生成器**：Qwen3.5-27B（最强开源权重模型）。

### 两个评估维度

1. **内部一致性**：模型是否正确执行 QOC 框架所需的计算（权重 × 选项支撑值 → 求和 → 选出最高分选项），既检查中间计算与最终和是否正确，也检查是否选出总分最高者。
2. **输出质量**：用 **LLM-as-a-Judge** 对比微调模型与未微调基础模型。裁判模型为 **Google Gemini Flash Lite 3.1**，并辅以**精确双侧二项检验**（显著性阈值 $p = 0.05$）判断偏好计数是否显著偏离"等概率（0.5）"的原假设。

### 内部一致性结果

| 模型 | 完全正确样例 | 正确选项计算 | 错误选项计算 |
| --- | ---: | ---: | ---: |
| Qwen-3.5-0.8B-QOC | 2 | 19 (3.75%) | 488 (96.25%) |
| Qwen-3.5-2B-QOC | 15 | 298 (63.54%) | 171 (36.46%) |
| Qwen-3.5-4B-QOC | 65 | 449 (88.91%) | 56 (11.09%) |
| Qwen-3.5-9B-QOC | 87 | 474 (97.33%) | 13 (2.67%) |
| Qwen-3.5-27B-QOC | 95 | 490 (98.99%) | 5 (1.01%) |

> 表1：QOC 微调 Qwen3.5 模型的内部一致性。"正确样例"指所有 QOC 相关计算全部正确；"正确/错误选项"指单个选项级得分计算的分类。

**规模与内部一致性呈单调强相关**：0.8B 几乎无法执行 QOC（仅 2 例全对，96% 选项计算错误）；2B 已有过半选项计算正确但完整样例仍大量失败；4B 起（88.91%）内部一致性已较强；9B、27B 达到很高水平，27B 在 100 例中 95 例全对、选项级正确率近 99%。

### 微调 vs 基础模型（LLM-as-a-Judge）

下表为"微调 QOC 模型答案被裁判偏好的次数（共 100 次成对判断）"。$^{*}$ 表示微调模型显著占优，$^{\dagger}$ 表示基础模型显著占优。

| 微调模型 | vs 27B | vs 9B | vs 4B | vs 2B | vs 0.8B |
| --- | ---: | ---: | ---: | ---: | ---: |
| Qwen-3.5-27B-QOC | 61$^{*}$ | -- | -- | -- | -- |
| Qwen-3.5-9B-QOC | 53 | 79$^{*}$ | -- | -- | -- |
| Qwen-3.5-4B-QOC | 49 | 81$^{*}$ | 82$^{*}$ | -- | -- |
| Qwen-3.5-2B-QOC | 35$^{\dagger}$ | 62$^{*}$ | 66$^{*}$ | 87$^{*}$ | -- |
| Qwen-3.5-0.8B-QOC | 12$^{\dagger}$ | 31$^{\dagger}$ | 26$^{\dagger}$ | 71$^{*}$ | 98$^{*}$ |

> 表2：裁判对 QOC 微调模型 vs 未微调基础模型的偏好次数。

**主结果分析**：

- **对角线（同尺寸对比）全部显著占优**：0.8B-QOC 被偏好 98 次、2B-QOC 87 次、4B-QOC 82 次、9B-QOC 79 次、27B-QOC 61 次。说明 QOC 微调在所有规模上都显著优于同尺寸基础模型。
- **小模型增益最大**：对角线优势从 0.8B 的 98 一路降到 27B 的 61，说明基础模型越弱，微调带来的相对收益越大。
- **跨规模对比（非对角线）更严苛**：9B-QOC vs 27B 基础（53）、4B-QOC vs 27B 基础（49）均不显著，即未可靠偏离等概率；但 4B-QOC vs 9B 基础（81）、2B-QOC vs 4B 基础（66）显著为正。0.8B-QOC 和 2B-QOC 面对远大于己的基础模型时则出现显著负偏差（$^{\dagger}$），说明更大的基础模型占优。

### Bradley–Terry 全局排序

| 模型 | BT 分数 | 95% CI |
| --- | ---: | --- |
| Qwen-3.5-27B-QOC | 6.435 | [4.939, 7.932] |
| Qwen-3.5-4B-QOC | 6.091 | [4.643, 7.539] |
| Qwen-3.5-9B-QOC | 6.086 | [4.623, 7.549] |
| Qwen-3.5-27B（基础） | 5.988 | [4.546, 7.430] |
| Qwen-3.5-2B-QOC | 5.282 | [3.844, 6.720] |
| Qwen-3.5-9B（基础） | 4.727 | [3.293, 6.161] |
| Qwen-3.5-4B（基础） | 4.718 | [3.282, 6.153] |
| Qwen-3.5-0.8B-QOC | 3.892 | [2.492, 5.292] |
| Qwen-3.5-2B（基础） | 3.122 | [1.675, 4.569] |
| Qwen-3.5-0.8B（基础） | 0.000 | 参考 |

> 表3：基础与微调模型的 Bradley–Terry 分数（从全部成对比较中聚合出单一潜在强度）。

全局排序与成对结果一致：微调 QOC 模型占据最强位置（27B-QOC 最高），但基础 27B 绝对强度仍强；0.8B-QOC 虽大幅优于自身基础模型，在全局排序中仍弱于更大模型。

### 消融 / 代际对比说明

需注意：**evaluation 一节明确只评估了 Gen2 微调模型**（"使用 QOC 作为内部思维过程的 generation 2 微调模型"）。Gen1 与 Gen3 的架构与实现虽有完整描述，但正文未给出二者独立的量化实验数据。讨论中给出的"小模型适合 Gen1、大模型支持 Gen2/Gen3"属于**基于当前数据的解读**，而非严格实验结论——这是本文一个明显的方法论缺口。

## 深度分析

### 研究价值

1. **提出"思维模型即一等研究问题"的视角**：将推理改进从"规模 + 通用推理方法"拓展到"选择并训练合适的内部推理格式"，为推理研究提供了互补路径。
2. **可审计的结构化中间表示**：QOC 把定性判断变为可检查、可复用、可监督的计算，天然契合可解释决策支持的诉求。
3. **严谨的统计评估**：引入精确二项检验区分"显著偏差"与"等概率兼容"，并用 Bradley–Terry 把成对偏好聚合为全局排序，实验方法较扎实。

### 方法优势

- QOC 强制分离问题/选项/准则，让"为什么选它"有据可查，而非仅给最终推荐。
- 微调数据显式包含中间 QOC 结构与最终答案，鼓励模型内化推理过程而非模仿孤立输出。
- 三代渐进设计清晰展示了"结构化选择 → 结构化+解释 → 统计增强+结构化+解释"的演进逻辑。

### 局限性

1. **仅一个模型家族、一个思维模型**：结论的可泛化性有限（论文讨论中也明确承认）。
2. **仅 Gen2 被量化评估**：三代管线中 Gen1、Gen3 缺少独立实验数据，"思维深度随规模适配"的结论是解读而非实验证实。
3. **合成任务与受控对比**：能支撑方法学分析，但无法覆盖真实部署的复杂性。
4. **裁判偏好的因果不清晰**：LLM-as-a-Judge 偏好的是"更优答案"，但未充分分离"结构化解释本身"与"决策正确性"各自的贡献。
5. **内部一致性 ≠ 决策正确性**：QOC 计算正确只说明执行了该框架，不保证真实世界答案的客观正确。

## 与相关论文对比

| 相关工作 | 与本文关系 | 差异点 |
| --- | --- | --- |
| [[Chain-of-Thought Prompting|Wei et al. 2022]] | 本文要"替代"的基线范式 | CoT 是自由形式顺序推理，QOC 是显式决策导向结构 |
| [[Large Language Models are Zero-Shot Reasoners|Kojima et al. 2022]] | 提示诱导推理的早期代表 | 本文不靠提示，而是通过微调内化思维模型 |
| [[STaR Self-Taught Reasoner|Zelikman et al. 2022]] | 同理用自生成推理轨迹做训练 | STaR 仍以自由 rationale 为主，本文用固定 QOC 结构 |
| [[Distilling Step-by-Step|Hsieh et al. 2023]] | 同样把推理轨迹蒸馏进小模型 | 本文关注"换一种思维结构"，而非单纯蒸馏 |
| [[ReAct Synergizing Reasoning and Acting|Yao et al. 2023]] | 同样强调显式推理轨迹提升可解释性 | ReAct 面向推理+行动，本文面向结构化决策支持 |
| [[Questions, Options, and Criteria|MacLean et al. 1991]] | QOC 的概念源头（设计空间分析） | 本文把 QOC 从"输出格式"升级为"可训练的内部思维模型" |
| [[Judging LLM-as-a-Judge|Zheng et al. 2023]] | 本文评估所用的裁判范式 | 本文用 Gemini Flash Lite 3.1 作裁判 |
| [[Self-Consistency Improves CoT|Wang et al. 2023]] | 同为 CoT 增强路径 | 本文不采样多条推理路径，而是重构单一推理结构 |

## 我的综合评价

**总体评分：7.0 / 10**

论文立意新颖——把"内部思维模型"的可训练性作为核心命题，并用结构化的 QOC 取代 CoT，视角有启发性；实验统计方法（二项检验 + Bradley–Terry）较严谨。但证据面偏窄：仅一个模型家族、仅 Gen2 被量化评估、依赖合成任务，且"思维深度随规模适配"这一重要论断缺乏直接实验支撑，因此整体可信度与普适性打了折扣。

| 维度 | 评分 | 说明 |
| --- | ---: | --- |
| 创新性 | 8.0/10 | "思维模型可训练 + 结构化替代 CoT"立意新，QOC 迁移到 LLM 内部思维有原创性 |
| 技术质量 | 7.0/10 | 三代管线与 QOC 计算流程清晰，但实现细节（训练配置、损失、prompt 模板）披露不足 |
| 实验充分性 | 6.0/10 | 双维度评估 + 严格统计好，但仅评估 Gen2、单一模型家族、合成任务、无真实任务验证 |
| 写作质量 | 7.5/10 | 结构清晰、逻辑连贯，讨论坦诚；但图表数据间存在"三代设计 vs 仅评估 Gen2"的落差 |
| 实用性 | 6.5/10 | 对可解释决策支持有潜力，但离真实部署（真实数据、多模型）仍有距离 |

## 相关论文

- [[Chain-of-Thought Prompting|Wei et al. 2022]] — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (NeurIPS 2022)
- [[Large Language Models are Zero-Shot Reasoners|Kojima et al. 2022]] — Large Language Models are Zero-Shot Reasoners (NeurIPS 2022)
- [[Self-Consistency Improves CoT|Wang et al. 2023]] — Self-Consistency Improves Chain of Thought Reasoning (ICLR 2023)
- [[STaR Self-Taught Reasoner|Zelikman et al. 2022]] — STaR: Self-Taught Reasoner (arXiv:2203.14465)
- [[Distilling Step-by-Step|Hsieh et al. 2023]] — Distilling Step-by-Step! (ACL 2023)
- [[ReAct Synergizing Reasoning and Acting|Yao et al. 2023]] — ReAct: Synergizing Reasoning and Acting (arXiv:2210.03629)
- [[Questions, Options, and Criteria|MacLean et al. 1991]] — QOC: Elements of Design Space Analysis (Human-Computer Interaction)
- [[Explanation in AI|Miller 2019]] — Explanation in Artificial Intelligence (Artificial Intelligence)
- [[Judging LLM-as-a-Judge|Zheng et al. 2023]] — Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena (arXiv:2306.05685)
