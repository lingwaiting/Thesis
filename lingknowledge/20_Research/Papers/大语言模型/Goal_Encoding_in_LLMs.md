---
date: "2026-11-14"
paper_id: "arXiv:2611.17297"
title: "Goal Encoding in LLMs"
authors: "Arnab Sen Sharma, Eric Todd, Sheridan Feucht, Andy Arditi, Rohit Gandikota, Natalie Shapira, Gabriele Sarti, Constanza Fierro, Lisa Schut, David Bau"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 机制可解释性
  - 目标表征
  - Goal-Encoding
  - 因果干预
quality_score: "8.5/10"
related_papers: []
created: "2026-11-14"
updated: "2026-11-14"
status: analyzed
---

# Goal Encoding in LLMs

## 核心信息
- **论文ID**：arXiv:2611.17297
- **作者**：Arnab Sen Sharma, Eric Todd, Sheridan Feucht, Andy Arditi, Rohit Gandikota, Natalie Shapira, Gabriele Sarti, Constanza Fierro, Lisa Schut, David Bau
- **机构**：--
- **发布时间**：2026-11-12
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.17297) | [PDF](https://arxiv.org/pdf/2611.17297)
- **引用**：--

## 摘要翻译

### 英文摘要
We investigate whether and how LLMs encode goals: internal neural representations that precede and guide action, and that are flexible enough to be realized through many different execution paths. We construct a coding setting with multiple sub-tasks that allows us to evaluate goal-seeking behavior and the selection of sub-goals. In this setting, we localize an encoding of the next sub-goal in the outputs of a sparse set of attention heads. The same heads can be used to decode the LM's future actions in natural language. We verify that this encoding is causal for next action and is plastic: transplanting it into a different environment triggers the same operation realized with a different sequence of tokens. The LM also sustains the encoding throughout the action's execution, which is puzzling because a transformer LM without recurrence cannot carry a state forward for more tokens than it has layers left. We propose that it does not need to. Under a non-recurrent goal-seeking (NRGS) account, the LM frequently re-reads the goal from the task specification, compares it with the progress visible in the growing context, and pursues what remains. We test the predictions of this account with targeted causal interventions.

### 中文翻译
本文研究大语言模型是否以及如何编码"目标"——即先于并引导行动、且灵活到能够通过多种不同执行路径实现的内部神经表征。我们构建了一个包含多个子任务的编程场景，用于评估目标寻求行为与子目标的选择。在该场景中，我们将"下一个子目标"的编码定位到一组稀疏注意力头的输出上；同一组注意力头还能被用于以自然语言解码模型未来的动作（例如"我正在检查一个词是否为回文"）。我们验证该编码对下一动作具有因果性，并且是可塑的：将其移植到另一个环境（如另一种编程语言）会触发相同的操作，只是通过不同的 token 序列实现。模型还会在整个动作执行过程中持续维持该编码，这令人困惑，因为无循环的 transformer LM 无法将状态向前携带超过其剩余层数所能覆盖的 token 数。我们提出它其实不需要这样做：在"无循环目标寻求（NRGS）"的解释下，LM 频繁地从任务规范中重新读取目标，将之与不断增长的上下文中可见的进展进行比较，并追求尚未完成的部分。我们用定向因果干预检验了这一解释的预测。

### 核心要点提炼
- **研究背景**：目标导向行为是智能的核心，但 LLM 内部如何表征"目标"尚不清楚。
- **研究动机**：LLM 是前馈、无循环的 transformer，却能展现跨多步的目标驱动行为，机制上如何可能？
- **核心方法**：在编程子任务场景中，用探针 + 因果干预定位并验证"子目标"的神经编码。
- **主要结果**：稀疏注意力头编码下一子目标，可解码未来动作、具有因果性与可塑性；并提出 NRGS（无循环目标寻求）机制解释。

## 研究问题

核心研究问题：**LLM 是否在内部显式编码"目标"？若是，该编码位于何处、是否因果驱动行为、如何跨步维持？以及无循环的 transformer 如何实现目标导向的多步行为？**

## 方法概述

### 核心方法

1. **目标寻求编程场景构建**
   - 设计包含多个子任务的编码任务，能评估目标寻求行为与子目标的选择。

2. **子目标编码定位**
   - 用探针在注意力头输出中定位"下一个子目标"的编码，识别出一组稀疏的注意力头。

3. **因果性与可塑性验证**
   - 验证该编码对下一动作的因果作用；将其移植到其他环境（另一种编程语言）触发相同操作。

4. **NRGS 机制检验**
   - 提出"无循环目标寻求"假说：LM 反复重读目标、比较进展、追求剩余；用定向因果干预检验其预测。

### 方法架构

![[figure1_page1.png|600]]

> 图：论文核心示意图，展示 LLM 中目标编码的定位、解码与因果干预框架。

### 关键创新

1. **首次定位"目标"编码** - 在注意力头层面定位到子目标的显式神经表征。
2. **证明编码的三重性质** - 可解码（未来动作）、因果（驱动下一动作）、可塑（可移植跨环境）。
3. **提出 NRGS 机制** - 用"反复重读 + 比较进展"解释无循环 transformer 的目标导向行为，并以因果干预验证。

## 实验结果

### 实验设置
- **场景**：含多子任务的编程任务（如回文检测、稳定匹配等）。
- **对象**：多个 LLM（含 code 模型）。
- **手段**：探针定位、因果干预（patch/移植）、自然语言解码。

### 主要结果
- **编码定位**：下一子目标编码集中于一组稀疏注意力头的输出。
- **可解码性**：同一组头可解码模型未来的自然语言动作。
- **因果性**：干预该编码会改变下一动作，证明其因果驱动行为。
- **可塑性**：移植到另一编程语言环境仍触发相同操作（以不同 token 序列实现）。
- **NRGS 支持**：定向因果干预符合"LM 反复重读目标并比较进展"的预测。

## 深度分析

### 研究价值评估
- **理论贡献**：为 LLM 的目标导向行为提供机制层面的解释，填补"目标表征"与"前馈架构"之间的空白。
- **实际应用**：为可解释性、可控性与安全对齐（理解并引导模型目标）提供新工具与视角。

### 方法优势
1. **因果严谨**：不止于相关性，通过移植与干预确立因果。
2. **机制新颖**：NRGS 假说巧妙化解了"无循环如何维持状态"的悖论。
3. **可解释性强**：定位到具体注意力头，具可操作性。

### 局限性
- 结论主要基于编程（coding）场景，向更开放域的目标表征泛化需验证。
- "反复重读"的假说依赖上下文可见的进展，对需要隐藏状态的任务可能不成立。
- 探针定位的注意力头是否唯一/稳定仍有待更系统的考察。

## 与相关论文对比
- **Transformer 可解释性 / 注意力头角色分析**：本文延续"定位特定功能到注意力头"的研究传统，但聚焦于"目标"这一更抽象、更高层的表征。
- **World Model / 规划相关研究**：与将模型视为隐式规划器的研究相呼应，但给出了更直接的神经证据。

## 我的综合评价

### 总体评分
**8.5/10** - 一个机制可解释性的漂亮案例：问题聚焦、方法严谨、结论新颖（NRGS 假说），对理解 LLM 目标导向行为有重要价值。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 首次定位并因果验证"目标编码"，提出 NRGS 机制 |
| 技术质量 | 8/10 | 探针 + 因果干预严谨 |
| 实验充分性 | 8/10 | 多模型、多任务、移植与干预齐备 |
| 写作质量 | 8/10 | 逻辑清晰 |
| 实用性 | 8/10 | 对对齐与可控性有启发 |

> [!tip] 关键启示
> LLM 无需隐藏的循环状态也能实现目标导向行为——它通过"反复重读目标 + 比较进展"来维持方向感；这一"目标编码"可被定位、解码甚至移植，为理解与引导模型行为打开了新窗口。

## 相关论文
- [[Function_Vectors_in_Large_Language_Models|Function Vectors]] - 同属可解释性中的"任务/功能向量"研究脉络
