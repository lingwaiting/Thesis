---
date: "2026-10-31"
paper_id: "arXiv:2610.40565"
title: "Gym-Anything for Robotics: Scaling physical simulations with digital agents"
authors: "Pranjal Aggarwal, Sean Welleck"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - 具身智能
  - 机器人仿真
  - 智能体
quality_score: "8.0/10"
created: "2026-10-31"
updated: "2026-10-31"
status: analyzed
---

# Gym-Anything for Robotics: Scaling physical simulations with digital agents

## 核心信息
- **论文ID**：arXiv:2610.40565
- **作者**：Pranjal Aggarwal, Sean Welleck
- **机构**：卡内基梅隆大学（Carnegie Mellon University）
- **发布时间**：2026-10-29
- **类别**：cs.RO, cs.AI, cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.40565) | [PDF](https://arxiv.org/pdf/2610.40565)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Realistic, fast robotics simulations could enable large-scale training of embodied AI models that transfer to the real world. However, despite decades of progress, available simulations cover only a narrow range of real-world tasks and physical phenomena. The core bottleneck is that creating better simulations requires significant expert effort and therefore does not scale. To address this, we introduce Gym-Anything for Robotics, a framework in which coding agents create realistic robotics simulations. Our core insight is that simulation creation is a coding agent task, so improvements in coding agents translate directly into richer and more faithful simulations. We select simulations based on U.S. GDP data to cover economically valuable physical work, define what makes a good simulation and a good task, and develop an adversarial creation-audit loop that checks each simulation and task against these definitions. Using this pipeline, we build RoboWorld, a set of 25 simulations with 25 test and 175 training RL environments of varying difficulty. Even the best frontier models fail on every RoboWorld test task, achieving an average reward of only 0.06. Building on RoboWorld, we introduce several new ways for frontier models to improve, including in-context learning across repeated attempts, a privileged teacher that reads the simulator state and teaches a student in context, self-assisted learning from the model's own privileged experience, and code policies that generate demonstrations across varied scenes to train vision-language-action models (VLAs) such as π₀.₅. Each of these substantially improves performance. We release our pipeline, RL environments, and benchmark to facilitate future research.

### 中文翻译
逼真且快速的机器人仿真有望实现可迁移到真实世界的大规模具身 AI 训练。然而，尽管已有数十年进展，现有仿真仅覆盖了真实世界任务与物理现象中的很窄范围。核心瓶颈在于：构建更好的仿真需要大量专家投入，因此无法扩展。为此，本文提出 Gym-Anything for Robotics，一个让编程智能体（coding agents）创建逼真机器人仿真的框架。其核心洞见是：**仿真创建本身就是一个编程智能体任务**，因此编程智能体的改进会直接转化为更丰富、更逼真的仿真。作者基于美国 GDP 数据选取仿真以覆盖经济上有价值的体力劳动，定义了"什么是好的仿真、什么是好的任务"，并开发了一个对抗式的"创建-审计"循环，逐项检查每个仿真与任务是否满足这些定义。利用该流水线，作者构建了 RoboWorld——25 个仿真、25 个测试与 175 个训练 RL 环境、难度各异。即使最先进的前沿模型也在所有 RoboWorld 测试任务上失败，平均奖励仅 0.06。基于 RoboWorld，作者引入若干提升前沿模型的新途径：跨多次尝试的上下文学习、读取仿真器状态并上下文教学学生的"特权教师"、从模型自身特权经验中自辅助学习，以及跨多样场景生成演示以训练视觉-语言-动作模型（VLA，如 π₀.₅）的代码策略。这些均显著提升性能。作者公开了流水线、RL 环境与基准。

### 核心要点提炼
1. **问题**：机器人仿真构建依赖专家、难以扩展，覆盖范围窄。
2. **方法**：把"仿真创建"重构为"编程智能体任务"，用对抗式创建-审计循环规模化生成仿真。
3. **贡献**：发布 RoboWorld（25 仿真/200 环境）与多种提升前沿模型的新训练途径。

## 方法概述

### 方法架构
![[pipeline_page1.jpg|800]]

框架核心是一条可扩展的"创建-审计"闭环：
1. **任务选取**：基于美国 GDP 数据筛选经济上有价值的体力劳动，确定覆盖范围。
2. **仿真定义**：明确定义"好的仿真"与"好的任务"的判定标准。
3. **对抗式创建-审计循环**：编程智能体创建仿真/任务，审计智能体按定义逐项检查，不合格则打回重建。
4. **RoboWorld 产物**：25 个仿真、25 测试 + 175 训练 RL 环境。

### 关键创新
1. **仿真创建 = 编程智能体任务**：将仿真扩展问题转化为代码生成问题，使其能随编程智能体能力提升而自动改善。
2. **对抗式创建-审计**：用"创建者 vs 审计者"的对抗闭环保证仿真质量。
3. **多种新训练途径**：特权教师、自辅助学习、代码策略生成演示等，为前沿模型在具身任务上提供提升手段。

## 实验结果

- **RoboWorld**：25 仿真、25 测试 + 175 训练 RL 环境，难度各异。
- **前沿模型基线**：最先进前沿模型在所有 RoboWorld 测试任务上均失败，平均奖励仅 **0.06**（说明任务极具挑战、现有模型远未达标）。
- **提升途径**：上下文学习（跨尝试）、特权教师、自辅助学习、代码策略（训练 VLA 如 π₀.₅）均显著提升性能。

## 深度分析

### 优势
- 用编程智能体解决仿真扩展瓶颈，是一条有前瞻性的"用智能体造数据"路线，与 LLM/编程智能体能力增长形成正反馈。
- RoboWorld 作为高难度具身基准，为领域提供了清晰的评测标尺。

### 局限与开放问题
- "仿真的逼真度"与"sim-to-real 迁移"之间仍有鸿沟，RoboWorld 中表现好不等于真实世界可用。
- 平均奖励 0.06 的基线说明任务可能过难，短期实用价值有限，更多是长线 benchmark 意义。
- 仿真由 LLM 生成，其物理正确性（动力学、接触、刚体）的保障依赖审计质量，仍需专家校准。

### 与相关工作的关系
- 相比人工构建的仿真（如 MuJoCo、Isaac 系列），本文强调"智能体自动化生成"，探索规模化的上限。
- 与"世界模型""具身数据生成"等工作形成互补，可为下游 VLA/策略训练提供源源不断的多样化环境。
