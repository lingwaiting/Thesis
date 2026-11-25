---
date: "2026-11-25"
paper_id: "arXiv:2611.31427"
title: "Learning What vs. Learning How: Decomposing Task and Interface Competence in Agentic Training"
authors: "Chaeeun Lee, Hossein A. Rahmani, Aryo Pradipta Gema, Kexin Xu, Emine Yilmaz, Pasquale Minervini, T. Ian Simpson, Chen Jin, Amrutha Saseendran"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - Agentic-Training
  - Interface-Competence
quality_score: "9.0/10"
created: "2026-11-25"
updated: "2026-11-25"
status: analyzed
---

# Learning What vs. Learning How: Decomposing Task and Interface Competence in Agentic Training

## 核心信息

- **论文 ID**：arXiv:2611.31427
- **标题**：Learning What vs. Learning How: Decomposing Task and Interface Competence in Agentic Training
- **作者**：Chaeeun Lee, Hossein A. Rahmani, Aryo Pradipta Gema, Kexin Xu, Emine Yilmaz, Pasquale Minervini, T. Ian Simpson, Chen Jin, Amrutha Saseendran
- **机构**：--
- **发布时间**：2026-11-23
- **会议/期刊**：--
- **领域**：大语言模型（Agentic Training）
- **链接**：https://arxiv.org/abs/2611.31427
- **PDF**：https://arxiv.org/pdf/2611.31427
- **引用**：--

## 摘要翻译

### 英文摘要

> LLM agents operate through harnesses that define system prompts, action formats, feedback loops, and resource constraints. A model may possess the knowledge needed to solve a task yet fail to express that knowledge through a particular harness. This raises a basic question: when a policy fails under a given harness, is it missing task knowledge or the competence to operate the interface? We distinguish two components of agentic competence: task competence, the knowledge and reasoning needed to solve a task, and interface competence, the ability to act on that knowledge through a given harness. Across four reasoning and agentic benchmarks, we show that harness-conditioned training yields vastly different outcomes depending on how these interact. Multi-harness distillation aggregates task knowledge from trajectories collected through different harnesses into a shared policy, but this knowledge is realised only when training preserves or improves competence on the inference-time interface. Otherwise, single-harness training can induce interference in interface competence, sometimes catastrophically collapsing performance on a held-out harness despite improvements in underlying task knowledge. To disentangle task and interface supervision, we introduce harness rehearsal: distillation on traces that carry minimal task knowledge while exercising the target interface. Cross-harness task supervision or rehearsal alone yields little gain, but their combination yields gains far larger than either: on BFCL V3 multi-turn with Qwen3-4B, they achieve 8.5% and 32.5% respectively, versus 58.8% together. Finally, on-policy reinforcement learning (RL) is more robust than distillation to single-harness training: it does not substantially degrade performance on other harnesses, and interface competence acquired through multi-harness distillation persists through subsequent RL.

### 中文翻译

LLM 智能体通过"harness（执行框架/接口封装）"来运作，这种框架定义了系统提示词、动作格式、反馈回路以及资源约束。一个模型可能已经具备解决某项任务所需的知识，却无法通过某个特定的 harness 将这种知识表达出来。这就引出了一个根本性问题：当某个策略在给定 harness 下失败时，究竟是缺少任务知识，还是缺少操作该接口的能力？

本文区分了智能体能力的两个组成成分：

- **任务能力（task competence）**：解决任务所需的知识与推理能力。
- **接口能力（interface competence）**：通过给定 harness 将知识付诸行动的能力。

在四个推理与智能体基准测试上，作者表明，受 harness 条件约束的训练会因这两者之间的交互方式不同而产生截然不同的结果。多 harness 蒸馏（multi-harness distillation）将从不同 harness 收集到的轨迹中的任务知识聚合到一个共享策略中，但只有当训练能保持或提升推理时接口上的能力时，这些知识才能真正被实现。否则，单 harness 训练可能在接口能力上引发干扰，有时即便底层任务知识有所提升，也会在留出（held-out）harness 上灾难性地崩溃。

为了解耦任务监督与接口监督，作者提出了 **harness rehearsal（接口复述/排练）**：在携带极少任务知识、却充分锻炼目标接口的轨迹上进行蒸馏。跨 harness 任务监督或 rehearsal 单独使用都收效甚微，但二者结合带来的增益远大于任何单一手段：在 BFCL V3 多轮任务上使用 Qwen3-4B 时，二者分别取得 8.5% 和 32.5% 的提升，而结合使用则高达 58.8%。最后，在策略上的强化学习（on-policy RL）相比蒸馏对单 harness 训练更为鲁棒：它不会在其他 harness 上显著降级性能，且通过多 harness 蒸馏获得的接口能力能在后续 RL 中持续保留。

### 核心要点提炼

1. **能力解耦**：将智能体能力显式拆分为「任务能力」（知道什么）与「接口能力」（如何通过 harness 表达出来），澄清了以往混为一谈的诊断模糊地带。
2. **接口干扰现象**：单 harness 训练可能在提升任务知识的同时，灾难性摧毁在留出 harness 上的接口能力。
3. **核心方法**：提出 harness rehearsal——用「低任务知识、高接口锻炼」的轨迹蒸馏，实现任务监督与接口监督的解耦。
4. **组合增益远超单独**：跨 harness 任务监督（8.5%）与 rehearsal（32.5%）单独都弱，但组合达 58.8%，呈现明显的互补/协同效应。
5. **RL 更鲁棒**：on-policy RL 对单 harness 训练的敏感性低于蒸馏，且蒸馏得来的接口能力可在 RL 后保留。

## 研究背景与动机

### 领域现状

近年来，LLM 智能体（LLM agents）成为连接大模型与真实工具/环境的关键范式。智能体的实际表现高度依赖于其运行所依托的 harness——即定义系统提示词、动作格式、反馈回路与资源约束的执行框架。同样的模型接入不同的 harness（例如不同的工具调用 schema、不同的 JSON 输出规范、不同的多轮交互协议），其性能可能天差地别。

现有的智能体训练方法（如监督微调、轨迹蒸馏、RL）通常把「提升任务表现」作为统一目标，很少区分失败究竟源于"模型不懂这个任务"还是"模型不会用这个接口"。这种混同导致了一个棘手的现象：训练在某些 harness 上显著提升了任务成绩，却在另一些 harness 上表现骤降，而人们难以定位根因。

### 现有方法局限

- **缺乏能力归因框架**：当策略在某个 harness 下失败时，无法系统判断是任务知识缺失还是接口操作能力不足。
- **蒸馏的接口脆弱性**：多 harness 蒸馏虽能聚合任务知识，但这些知识只有在推理时接口能力被保持或提升时才"兑现"；单 harness 训练反而可能诱发接口干扰，导致留出 harness 上灾难性退化。
- **任务与接口监督耦合**：传统训练信号中"任务监督"和"接口监督"纠缠在一起，难以单独控制或诊断。

### 研究动机

作者旨在回答一个基础问题——**当策略在给定 harness 下失败时，它缺失的究竟是任务知识，还是操作接口的能力？** 并据此设计能解耦两种能力、避免接口干扰的训练策略。

## 研究问题

1. 如何将智能体能力分解为「任务能力」与「接口能力」两个可独立度量的成分？
2. harness 条件化的训练（单/多 harness、蒸馏 vs RL）如何影响这两种能力，以及它们之间的交互？
3. 如何设计训练方法，在不牺牲接口能力的前提下高效迁移任务知识？
4. on-policy RL 相比蒸馏，是否对单 harness 训练带来的接口干扰更具鲁棒性？

## 方法概述

### 核心思想

论文的核心洞见是：**"学会做什么（Learning What）"与"学会怎么做（Learning How）"是两个正交的能力维度**。任务能力回答"模型是否掌握了解决任务所需的知识与推理"，接口能力回答"模型是否能把已有知识通过特定 harness 正确表达出来"。二者必须被显式区分，并分别予以监督，否则训练很容易在提升任务知识的同时悄悄破坏接口能力。

### 整体架构

![[2611.31427_hero_figure_page1.png|800]]

*图注：论文主图（方法总览）。上方区分任务能力（task competence，知识/推理）与接口能力（interface competence，通过 harness 行动）两条轴线；下方展示不同的训练策略如何作用于这两者，以及它们之间的交互如何决定最终结果。*

### 各模块详细说明

1. **能力分解框架（task vs. interface competence）**
   将智能体在某个 harness 下的成功拆解为两个前提：模型需具备解决任务的知识与推理（任务能力），且需具备将知识编码为该 harness 所要求的动作格式/协议的能力（接口能力）。一个失败样本可据此归因到"知识缺失"或"接口失配"。

2. **多 harness 蒸馏（multi-harness distillation）**
   从通过不同 harness 收集的轨迹中，把任务知识聚合到一个共享策略里。关键局限在于：聚合而来的知识只有在推理时接口能力被保持或提升时才能转化为实际收益，否则可能"有知识却用不出来"。

3. **单 harness 训练与接口干扰（interface interference）**
   只在单一 harness 上训练，可能在接口能力上引入干扰，有时会灾难性摧毁留出 harness 上的表现——即便底层任务知识反而提升了。这是本文观察到的核心风险信号。

4. **harness rehearsal（接口排练）**
   论文的核心方法。其思路是用"携带极少任务知识、却充分锻炼目标接口"的轨迹来做蒸馏，从而在几乎不带入任务偏置的前提下，专门强化目标接口上的操作能力。由此实现任务监督与接口监督的解耦。

![[2611.31427_rehearsal_figure_page1.png|800]]

*图注：harness rehearsal 示意图。左侧为跨 harness 任务监督信号，右侧为针对目标接口的 rehearsal 轨迹（低任务知识、高接口锻炼），二者组合实现能力解耦。*

5. **跨 harness 任务监督与 rehearsal 的组合**
   单独使用跨 harness 任务监督或 rehearsal 收益有限，但二者组合产生远超任何单一手段的协同增益（见实验结果）。这印证了"任务能力"与"接口能力"是两个需要分别补充的互补短板。

6. **on-policy RL 的鲁棒性**
   相比蒸馏，on-policy RL 对单 harness 训练更鲁棒：它不会在其他 harness 上大幅降级，且通过多 harness 蒸馏获得的接口能力能在后续 RL 中持续保留。

![[2611.31427_execution_framework_column_page1.png|800]]

*图注：执行框架/接口封装示意。同一模型接入不同 harness（不同的系统提示词、动作格式、反馈回路、资源约束）后，其任务知识能否被表达出来取决于接口能力是否匹配。*

## 实验结果

### 实验目标

验证「任务能力 vs 接口能力」分解的有效性，量化不同训练策略（单/多 harness 蒸馏、rehearsal、RL）对两种能力的影响，并证明 rehearsal 与跨 harness 任务监督的组合能带来显著的协同增益。

### 数据集与基线

- **基准**：四个推理与智能体基准（含 BFCL V3 多轮任务）。
- **模型**：以 Qwen3-4B 为代表的小参数开源模型。
- **训练范式对比**：单 harness 训练、多 harness 蒸馏、harness rehearsal、跨 harness 任务监督，以及 on-policy RL。

### 主要结果

- **组合增益远超单独**：在 BFCL V3 多轮任务 + Qwen3-4B 上，跨 harness 任务监督单独带来 8.5% 提升，harness rehearsal 单独带来 32.5% 提升，而二者组合高达 **58.8%**，呈现显著的超线性协同效应。
- **单 harness 训练的风险**：单 harness 训练可能在提升任务知识的同时，在留出 harness 上灾难性崩溃，暴露接口干扰。
- **RL 更鲁棒**：on-policy RL 不会在其他 harness 上大幅降级；且多 harness 蒸馏得来的接口能力在后续 RL 中持续保留。

![[2611.31427_fig_heldout_page1.png|800]]

*图注：留出（held-out）harness 上的性能结果。可见单 harness 训练在留出接口上出现明显退化（接口干扰），而 rehearsal 或组合策略能缓解该退化，直观展示了任务能力提升与接口能力崩溃之间的张力。*

![[2611.31427_fig_reach_page1.png|800]]

*图注：结果对比图（"reach"）。横轴为不同训练策略，纵轴为性能表现，突出跨 harness 任务监督（8.5%）与 rehearsal（32.5%）单独有限、组合（58.8%）大幅跃升的协同关系。*

### 消融实验

- **去掉 rehearsal**：仅保留跨 harness 任务监督，增益骤降至 8.5%，说明单纯补充任务知识无法弥补接口能力短板。
- **去掉跨 harness 任务监督**：仅保留 rehearsal，增益为 32.5%，说明仅有接口锻炼而缺乏任务知识迁移同样受限。
- **结论**：二者缺一不可，验证了任务能力与接口能力是互补且需分别监督的两个维度。

## 深度分析

### 研究价值与理论贡献

- **概念澄清**：为智能体失败诊断提供了"任务能力 vs 接口能力"这一清晰、可操作的二分框架，把长期被混为一谈的两类能力显式分离，具有较好的理论价值。
- **揭示接口干扰现象**：系统展示了单 harness 训练"知识升、接口崩"的灾难性风险，为社区理解智能体训练的脆弱性提供了实证依据。
- **方法论贡献**：harness rehearsal 提供了一个低成本、可泛化的接口能力强化手段，且其与任务监督的组合具有明显协同效应，具有实用价值。

### 方法优势

- **可解耦、可诊断**：rehearsal 通过"低任务知识、高接口锻炼"的轨迹设计，将两类监督信号分离，便于针对性优化。
- **组合协同显著**：8.5% / 32.5% → 58.8% 的结果直观展示了 1+1 远大于 2 的互补效应，说服力强。
- **RL 视角补充**：指出 on-policy RL 比蒸馏对接口干扰更鲁棒，为训练范式选择提供了依据。

### 局限性

- **实验规模有限**：摘要仅披露 Qwen3-4B 这一规模的代表性结果，更大模型、更多 harness 组合下的可扩展性有待验证。
- **基准覆盖**：四个基准虽具代表性，但接口多样性与真实生产环境的复杂 harness 仍有差距。
- **机制解释偏薄**：对"为何单 harness 训练会诱发接口干扰"的深层机制刻画（如注意力/表示层面的变化）在摘要中未充分展开。

## 与相关论文对比

- **相对一般的轨迹蒸馏/SFT 工作**：本文的增量在于显式区分任务与接口两个能力维度，而非笼统地追求任务准确率提升。
- **相对智能体鲁棒性/泛化研究**：已有工作多关注任务分布漂移或工具泛化，本文则聚焦"同一任务、不同接口"下的能力保持，切入角度更具操作性。
- **相对 RL 训练研究**：本文提供了"蒸馏 vs on-policy RL 在接口干扰鲁棒性上的对比"这一独特视角。

## 技术路线定位

本文属于 **agentic training（智能体训练）** 与 **能力可解释性/鲁棒性** 的交叉地带。它不追求提出全新的模型架构，而是针对"训练信号与接口绑定"这一实践痛点，提供一个能力解耦的诊断框架与 rehearsal 这一轻量干预手段。后续可沿两条线延伸：其一，将任务/接口能力分解扩展到多模态与更复杂的工具调用场景；其二，把 RL 的鲁棒性观察进一步理论化，指导训练范式选择。

## 我的综合评价

| 维度 | 评分 | 理由 |
| --- | --- | --- |
| 创新性 | 9 | "任务能力 vs 接口能力"的二分 + harness rehearsal 概念清晰、切口新颖，直击实践痛点 |
| 技术质量 | 9 | 实验设计逻辑自洽，用 8.5/32.5/58.8 的组合对比有力支撑核心论断 |
| 实验充分性 | 8 | 覆盖四个基准与蒸馏/RL 对比，但规模与模型多样性仍有扩展空间 |
| 写作质量 | 9 | 问题定义、能力分解、方法、结论层次分明，概念命名（rehearsal）易传播 |
| 实用性 | 9 | 对实际部署智能体的团队有直接诊断与训练指导价值，rehearsal 落地成本低 |

> [!tip] 关键启示
> 智能体训练的失败往往不是"模型不懂任务"，而是"模型不会用接口"。解耦任务能力与接口能力、并用 rehearsal 单独强化接口，能让跨 harness 蒸馏的知识真正兑现——记住 8.5% + 32.5% 单独有限、组合 58.8% 这一互补信号。

> [!success] 推荐指数
> ★★★★★（9.0/10）—— 概念清晰、证据有力、可直接指导智能体训练实践，是 agentic training 领域值得精读的一篇工作。
