---
date: "2026-11-21"
paper_id: "arXiv:2611.26248"
title: "TwinLab-Bench: Evaluating AI Scientists in Agentic Twins of Real Labs"
authors: "Yang Wan, Linchao Zhu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - AI-Scientist
  - Benchmark
  - Agent
quality_score: "8.7/10"
created: "2026-11-21"
updated: "2026-11-21"
status: analyzed
---

# TwinLab-Bench: Evaluating AI Scientists in Agentic Twins of Real Labs

## 核心信息

- **论文 ID**：`arXiv:2611.26248`
- **作者**：Yang Wan、Linchao Zhu（通讯作者）
- **机构**：浙江大学（Zhejiang University）
- **发布时间**：2026-11-19
- **领域**：大语言模型 / AI Scientist / Agent 评测
- **分类**：cs.AI, cs.CL, cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2611.26248) | [PDF](https://arxiv.org/pdf/2611.26248)
- **项目主页**：https://twinlab-bench.github.io/

## 摘要翻译

**英文原文**

> AI agents are increasingly expected to act as AI scientists, which requires three increasingly open abilities: carrying out a given computational workflow correctly, choosing the next measurements from a given design space, and designing their own experiments. Existing benchmarks mainly evaluate the first two abilities, in a terminal or in a closed loop that selects from predefined candidates. Evaluating the third ability requires reliably executing an experiment that an agent designs, with readings grounded in a real laboratory. We introduce TwinLab-Bench, which evaluates AI scientists on self-designed experiments in 52 tasks from laboratories in the physical sciences, the life sciences, and engineering. The benchmark supports more than 100 experimental action types, and the agent's report is scored by comparison with a hidden answer taken from real laboratory records. Each task has its own TwinLab agent, an agent that serves as the environment and executes the experiments that the tested agent designs, reading the raw materials of the laboratory and calling calculators that are built from its records. Compared with a language model that sees only the task description, the TwinLab agent almost eliminates readings that deviate from the real records and inconsistent answers to a repeated request, and closed-book probes control for answers recalled from memory. We find that these tasks remain hard for frontier models: the strongest, Claude Fable 5.2, averages a score of only 45.0, and we conduct an empirical study of where it fails.

**中文翻译**

> AI 智能体越来越被期望扮演"AI 科学家"的角色，这需要三种逐步开放的能力：正确执行给定的计算工作流、从给定的设计空间中选择下一步测量、以及自主设计自己的实验。现有基准主要评测前两种能力，它们要么在终端里完成，要么在一个从预定义候选中做选择的闭环里进行。而评测第三种能力，需要可靠地执行一个智能体自行设计的实验，且读数必须根植于真实的实验室。我们提出 TwinLab-Bench，它通过来自物理科学、生命科学与工程领域实验室的 52 个任务，评测 AI 科学家在自主设计实验上的表现。该基准支持超过 100 种实验动作类型，智能体提交的报告通过与真实实验室记录中提取的隐藏答案进行比对来打分。每个任务都有其专属的 TwinLab 智能体——一个充当环境、执行被测智能体所设计实验的智能体，它读取实验室的原始材料，并调用由这些记录构建的计算器。与只能看到任务描述的语言模型相比，TwinLab 智能体几乎消除了偏离真实记录的读数，以及重复请求时答案不一致的问题；同时通过闭卷探测来控制模型凭记忆回忆答案。我们发现这些任务对前沿模型依然很难：最强的 Claude Fable 5.2 平均得分仅为 45.0，并对它的失败之处进行了实证研究。

**核心要点提炼**

1. **填补能力空白**：首个系统性评测 AI 科学家"自主设计实验"这一最难能力的公开基准。
2. **真实实验室孪生**：用 TwinLab 智能体（环境）+ 原始记录 + 由记录构建的确定性"计算器"来为每个任务构造一个真实实验室的数字孪生。
3. **隐藏答案打分**：报告通过与真实实验室记录中提取的隐藏真相比对来评分，而非人工主观判断。
4. **前沿模型仍难**：最强模型 Claude Fable 5.2 平均分仅 45.0/100，Opus 5.5 为 40.2，其余模型在 5.4–31.7 之间。

## 研究背景与动机

论文把"AI 科学家"需要的能力划分为三个层级，开放性逐级递增：

1. **正确执行给定的计算工作流**（如分析给定数据集）：现有基准如 BixBench、ScienceAgentBench 已在评测。
2. **从给定设计空间中选择下一步测量**：SciGym、BoxingGym、BioDiscoveryAgent、ASB 等已在评测。
3. **自主设计自己的实验**：这是三者中最难、也最少被评测的能力，而自动化实验室正开始真正运行这类实验。

作者指出，现有科学基准存在一个根本性缺口：

- **知识问答类**（GPQA、LAB-Bench）只测记忆；
- **数据分析类**（ScienceAgentBench、BixBench）只给数据和问题；
- **终端类 / 自动 ML 类**（Terminal-Bench、MLE-bench、RE-Bench、PaperBench）能跑新实验，但都是"计算机内部的程序"；
- **交互式环境**（DiscoveryWorld、SciGym 等）的读数来自写进环境的规则，而非真实实验室数据。

**关键结论**：要评测"自主设计实验"，必须有一个能对智能体新设计的实验返回**真实数据**的环境——这正是 TwinLab-Bench 的目标。作者从"模拟器 / 回放"朝"真实实验室"这个目标迈进了一步：为每个真实实验室构建一个**孪生（twin）**。

初步研究（表 tab:prelim）验证了动机：仅让语言模型扮演实验室是行不通的——只给任务描述时，语言模型在真实实验室请求的回放上有 **41%** 偏离记录；给原始材料后降为 **14%**；而用 TwinLab 智能体则降到 **2%**。这为"必须构建专门的实验室孪生"提供了实证依据。

![[axes_1d_v21_page1.png|600]]

> 图 1：科学基准的两个坐标轴。(a) 从左到右，智能体设计的方法部分越来越多，TwinLab-Bench 测试能力 ③；(b) 从左到右，新实验的环境越来越接近真实实验室，TwinLab-Bench 使用环境 ④（真实实验室的孪生）。

## 研究问题

1. 如何构造一个能对智能体**任意设计的实验**返回**真实读数**的环境？
2. 如何确保评测分数来自智能体**自己的实验**，而非记忆（数据污染）或环境漏洞？
3. 前沿模型在"自主设计实验"这一能力上到底处于什么水平？它们的失败模式是什么？

## 方法概述

### 核心思想

为**每个真实实验室**构建一个"数字孪生"，由三个关键角色协同完成一次评测：

- **Actor 智能体（被测模型）**：接收实验室在某个时间截点前拥有的材料（notebook、原始数据、中间结论），在预算内自由提出假设、组合并设计实验，最后提交报告。
- **TwinLab 智能体（环境）**：一个编码智能体，用工具读取实验室文件、运行程序，执行 actor 设计的实验并返回读数。
- **Judge 智能体（评分）**：视觉语言模型，对照隐藏真相给报告打分。

三者职责与可见性严格隔离（论文表 tab:visibility）：TwinLab 和 Judge 能看到完整记录与隐藏真相，而 actor 只能看到截点前的交接材料。

![[teaser_v23_plain_page1.png|600]]

> 图 2：TwinLab-Bench 按学科（division）与院系（department）覆盖 52 个任务，每个任务由一个独立的 TwinLab 智能体驱动环境。

### 方法框架

**任务（Tasks）**：从约 375 个候选源中筛选出 52 个任务，每个任务基于一份真实研究记录（开放实验 notebook、原始仪器导出、论文及补充材料、公开数据集等）。49 个任务直接改编自真实实验室记录，3 个为"grounded synthesis"。任务按学科分布为：**物理科学 20、生命科学 20、工程 12**；按形式分为 **37 个 claim review（声明审查）、5 个 troubleshooting（故障排查）、10 个 research campaign（研究攻关）**。

任务构造的关键步骤是选定一个**截点（cutoff）**：截点前实验室已有的数据、协议、中间结论交给 actor；截点后的记录（隐藏真相）只给 TwinLab 和 Judge。专家路线在孪生中的重放平均得 **97.8/100**，作为任务有效性校验。

**难度控制**通过五个使任务依赖"实验判断"的属性实现：预算为专家路线的 1–3 倍；记录中提到的稀缺材料一旦用尽不可恢复；伪影有可信的良性解释；看似决定性的测量可能有其他成因；部分任务正确答案就是"预算内无法判定"。

**TwinLab 智能体**处理每个实验请求分三步：在任务材料中寻找依据（常需组合多条记录）；取记录中可比材料上已记录的数值，或在 shell 中运行计算器命令；把这些数字写成仪器自身格式的观察，并在存在时返回匹配的真实图片。**计算器**是从实验室自身数据编写的确定性程序（如重采样已记录测量、对物理模型拟合记录、填充真实仪器导出模板），且只做**局部外推**（local extension），每个外推值都有写明的依据。

**评分机制**：每个任务有 rubric，分两类条目——**结论项（conclusion items）**检查报告是否陈述正确答案，**验证项（verification items）**检查报告是否从本轮实验所得证据推出该答案。每个任务标注若干**核心项（core items）**，任一核心项未获满分则该 run 记 0 分。Judge 智能体引用报告文本或请求编号为每一条目打分，核心项满分需经两次独立复核。

**无效 run 审计**：Judge 同时审计环境——读数泄露隐藏真相、与前观察或物理矛盾、或拒绝真实实验室可运行的实验，均判定 run 无效。

![[method_fig_BC33_page1.png|600]]

> 图 3：单个任务的运行流程示意。展示一个示例任务的材料，以及一次包含 actor、TwinLab、judge 三个智能体的完整运行。

![[scorecard_v18_page1.png|600]]

> 图 4：科学基准的评分卡（scorecard）。每行是一个基准，每列按关键等级拆分其任务；橙色框标出各组基准的主要弱点。TwinLab-Bench 是唯一同时结合"广泛实验范围 + 真实问题 + 真实读数"的基准。

## 实验结果

### 数据集与设置

- **任务规模**：52 个任务，覆盖物理科学（20）、生命科学（20）、工程（12）。
- **动作类型**：超过 100 种实验动作类型（两名标注者独立标注得 105 和 107 种）。
- **被测模型**：13 个模型作为 actor 智能体，GPT 系列跑在 Codex，其余跑在 Claude Code；按推理 effort 计共 17 种配置。
- **环境与评分模型**：TwinLab 智能体与 Judge 智能体统一运行在 DeepSeek-flash 上（其成本约 55 USD，其中 Judge 16.9、TwinLab 37.7）。
- **指标**：报告分数重缩放到 0–100，报告所有任务的平均分，并同步报告成本（遵循 Kapoor et al. 的 "AI agents that matter"）。

### 主结果

![[table_main_v9_page1.png|600]]

> 图 5：主结果表。每个 actor 智能体及各推理 effort 按学科、按任务形式及总体的平均分（0–100），以及 actor 智能体在所有任务上的成本（USD）。

![[fig_score_cost_v10_page1.png|600]]

> 图 6：平均分 vs. actor 智能体成本（模型默认高 effort）。分数随成本的对数大致上升，更大模型与更高推理 effort 都能提高分数。

关键数字：

- **Claude Fable 5.2**：平均 **45.0**（最强）
- **Claude Opus 5.5**：**40.2**
- 其他 actor 智能体：**5.4 – 31.7**
- 物理科学是 17 种配置中 12 种的最低分学科；工程是 14 种的最高分学科。

统计显著性：在 136 对配置中，127 对"平均分更高的一方也在更多任务上得分更高"；对平均分相差至少 10 的 75 对，配对比较区分出 66 对。DeepSeek-flash 三次重复得 **19.7 ± 3.3**，MiMo-V2.6-Pro 三次重复得 **14.0 ± 3.6**，说明分数可复现。

**分数确实区分正确与错误报告**：在 21 个同时含正确与错误报告的固定任务中，19 个任务里所有正确报告的总分都高于所有错误报告，平均差距为满分的 0.46。

### 孪生的保真度（Fidelity）

- **读数来源**：在 831 次运行、7808 个请求中，**76%** 的实验结果来自记录或由同一实验室数据校准的计算器（53% 直接查记录、23% 由计算器计算）；99.5% 可追溯到记录、计算器输出或一步算术。
- **外推敏感性**：替换记录外的外推选择（改变 12%–44% 的计算器输出）后，大多数结论不变，说明建模选择对评测结果影响很小。
- **留出条件（held-out）**：隐藏记录的连续条件并重建计算器，隐藏读数中位误差为插值 **0.74%**、外推 **2.47%**；隐藏离散条件（kinesin 的一个 casein）预测误差 **0.6%**。
- **专家路线之外**：成功运行中超过三分之一请求是专家路线没有的新实验，说明 actor 能沿自己的路线解题。
- **抗攻击（red-team）**：告知 Opus 5.5 实验室是模拟的并允许其利用漏洞，9 次利用尝试全部被阻断；它找到的 13 个真实缺陷均不改变评分。

### 记忆控制（Memorization Control）

![[table_memorization_v4_page1.png|600]]

> 图 7：Opus 5.5 在所有任务上的闭卷记忆探测。条形给出答案为"是 / 部分 / 否"的任务数量。

三种方式验证分数来自智能体自己的实验而非记忆：

1. **改变实验室真实状态**：保持交接材料逐字不变、只改计算器与隐藏真相。在 10 个可判定的状态中，**8 个**状态下 actor 的结论跟随新状态。
2. **闭卷记忆探测**：无工具闭卷会话下，Opus 5.5 在 0 个任务上回忆起源结论、仅 2 个任务能无数据推导答案；Fable 5.2 对 1 个任务回忆起结论。命名了来源的任务在评测中得分并不更高。
3. **零实验基线**：预算为 0 时，Opus 5.5 与 DeepSeek-flash 在 33 个任务上**全部不通过**。

### 失败分析（Empirical Analysis of Model Failures）

![[findings_v7_page1.png|600]]

> 图 8：任务来源。四个来自期刊论文或实验 notebook 的示例任务，以及每个任务所针对的发现。

**知道—做到差距（knowing-doing gap）**：运行前模型读完交接文件写出完整计划。计划中"完整陈述"的决策在运行中得满分的 87%，但计划"未提及"的决策仍得 62%。计划完整陈述但运行仍丢分的 11 个决策中，9 个确实按计划执行，却在**解读自身读数或写报告**环节丢分——说明静态基准（只给计划打分）会误判这些决策。

**失败类型**：Opus 5.5 在需实验的 33 个任务上有 16 个得 0 分的运行，其中 **12 个来自实验决策错误**，最常见的是"**接受某个机制却不做判别性测试**"。典型例子：牡蛎糖测定读数偏低约 30%，Opus 5.5 在做水检查后排除移液器，却从未测试 1 μL 转移（该转移只输送约 70% 的样品）。

**更多预算很少有用**：21 个失败运行中仅 8 个花掉至少 90% 预算；把这些预算翻倍，只有 2 个转为通过。

## 深度分析

### 研究价值

1. **填补评测空白**：这是首个把"自主设计实验"这一 AI 科学家最难能力落到真实实验室记录上的公开基准，桥接了模拟器/回放与真实实验室之间的鸿沟。
2. **真实读数 + 自由组合实验**：同时具备两个此前被分散的属性——actor 自由组合实验（像 SciGym/DiscoveryWorld）+ 读数来自真实实验室记录（像 ASB/回放）。
3. **严谨的方法论工程**：隐藏答案、核心项硬门槛、双重复核、无效 run 审计、抗攻击设计、多角度记忆控制，整套评测管线质量很高。
4. **可操作的经验结论**：不仅给分数，还定位了前沿模型的具体失败模式（知道—做到差距、不测机制的判别性），对后续改进有直接指导意义。

### 方法优势

- **数字孪生的可靠性**：初步研究（41%→2% 偏离）与保真度链条（76% 读数来自真实数据、held-out 误差 <2.5%）共同支撑了"读数真实可信"这一核心主张。
- **记忆控制严密**：改变真实状态 + 闭卷探测 + 零实验基线三管齐下，是同类基准中少见的严谨做法。
- **成本可复现性**：把 actor 与环境的成本分离报告，环境成本仅 55 USD，对社区复现友好。

### 局限性

1. **计算器的局部性**：读数只有在记录附近才真实，远离记录的外推本质上仍是"基于记录拟合的模型"，无法覆盖真正全新的科学发现（这是论文自己承认的、由方法论本身决定的边界）。
2. **不评测物理操作**：基准刻意排除了仪器的物理操作（与 EmbodiedLab 相对），只测实验决策，无法反映动手能力。
3. **任务数量与多样性有限**：52 个任务、且多为 claim review 形式，工程领域仅 12 个；长期看需扩充以提升稳健性。
4. **评分模型自偏好风险**：Judge 与 TwinLab 都用 DeepSeek-flash，而 DeepSeek-flash 本身也是一个被测 actor，作者虽做了换模型消融（与 Opus 5.5 的 judge 在 19/20 运行一致），但这一隐患仍值得关注。
5. **少数 grounded synthesis 任务**：3 个合成任务的隐藏真相由构造固定，与"真实实验室记录"主张略有偏离（论文已明确说明）。

## 与相关论文对比

| 维度 | ScienceAgentBench / BixBench | SciGym / DiscoveryWorld / BoxingGym | BioDiscoveryAgent / ASB（回放） | **TwinLab-Bench** |
| --- | --- | --- | --- | --- |
| 环境类型 | 给定数据集分析 | 模拟器 / 方程模型 | 已发表测量的回放 | **真实实验室的数字孪生** |
| 读数来源 | 提供的静态数据 | 写进环境的规则 | 已记录的实验条件 | **真实记录 + 记录校准的计算器** |
| 能否自主设计新实验 | 否 | 部分（在设定世界内） | 从记录条件中选 | **是（自由组合，可超列表）** |
| 记忆控制 | 弱 | 弱 | 弱 | **强（三重控制）** |
| 隐藏答案打分 | 无 | 无 | 部分 | **有（对照真实记录）** |

相比 EmbodiedLab（测的是物理仪器操作的具身能力，基于教科书实验），TwinLab-Bench 明确排除物理操作、专注于"来自真实实验室问题"的实验决策。相比 AgentBeats（把基准打包成一个设置任务/环境/评分的 agent），TwinLab-Bench 将"环境"与"评分"拆分为 TwinLab 与 Judge 两个角色。

## 我的综合评价

**总体评分：8.7/10**

这是一篇工程实现严谨、方法立意清晰、实验验证充分的高质量基准论文。它精准命中了"AI 科学家自主设计实验"这一此前缺少可靠评测手段的能力，并用"真实实验室数字孪生"这一原创思路给出了可信的读数来源。最可贵的是对"分数是否真实"的自我拷问贯穿始终——隐藏答案、核心项硬门槛、三重记忆控制、抗攻击与无效审计，让结果具有很强的可信度。主要扣分点在于方法论内在的边界（远离记录的外推仍是拟合、不测物理操作、任务规模偏小），以及评分/环境模型与某被测模型同源带来的轻微自偏好风险。

**分项评分**

| 维度 | 评分 | 说明 |
| --- | --- | --- |
| 创新性 | 9.0 | 数字孪生环境 + 隐藏答案评分的组合思路原创且定位准确 |
| 技术质量 | 8.5 | 工程实现严谨（确定性计算器、泄漏守卫、回放脚本、无效审计） |
| 实验充分性 | 9.0 | 13 模型、三重记忆控制、保真度链条、red-team、换模型消融，验证全面 |
| 写作质量 | 8.5 | 结构清晰、论证链完整，方法细节充分 |
| 实用性 | 8.5 | 任务与评测代码公开发布，环境成本低、可复现 |

## 相关论文

- **SciGym**（Duan et al. 2025）— Measuring scientific capabilities of language models with a systems biology dry lab
- **DiscoveryWorld**（Jansen et al. 2024）— A virtual environment for developing automated scientific discovery agents
- **BoxingGym**（Gandhi et al. 2025）— Benchmarking automated experimental design and model discovery
- **BioDiscoveryAgent**（Roohani et al. 2025）— AI agent for designing genetic perturbation experiments
- **Autonomous Science Bench (ASB)**（Wen & Liu 2026）— Evaluating AI agents for autonomous scientific discovery
- **EmbodiedLab**（Gao et al. 2026）— Benchmarking embodied agents that act to know
- **ScienceAgentBench**（Chen et al. 2025）— Toward rigorous assessment of language agents for data-driven scientific discovery
- **BixBench**（Mitchener et al. 2025）— Benchmark for LLM-based agents in computational biology
- **LAB-Bench / LABBench2**（Laurent et al. 2024 / 2026）— Measuring capabilities of language models for biology research
- **MLE-bench / RE-Bench / PaperBench**（Chan 2025 / Wijk 2024 / Starace 2025）— 自动化 ML / R&D 研究评测
- **AgentClinic / τ²-bench**（Schmidgall 2024 / Barres 2025）— LLM 扮演病人/用户的 agent 评测
- **EnvSimBench / Sim2Real gap**（Liu 2026 / Zhou 2026）— LLM 环境模拟的偏差与真实世界差距
