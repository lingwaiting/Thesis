---
date: "2026-10-03"
paper_id: "arXiv:2610.02150"
title: "From Knowledge Access to Source Learning: Developing Source-Specific Competence"
authors: "Lucheng Fu, Kejing Xia, Yiyang Wang, Yiqiao Jin, Jinjin He, Xiyuan Yang, Haoxin Liu, Ye Yu, Haibo Jin, Yijia Xiao, Wenke Lee, B. Aditya Prakash, Haohan Wang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大模型
  - LLM
  - RAG
  - Agent
  - Source-Learning
  - Agent-Memory
quality_score: "8.5/10"
related_papers: []
created: "2026-10-03"
updated: "2026-10-03"
status: analyzed
---

# From Knowledge Access to Source Learning: Developing Source-Specific Competence

## 核心信息
- **论文ID**：arXiv:2610.02150
- **作者**：Lucheng Fu, Kejing Xia, Yiyang Wang, Yiqiao Jin, Jinjin He, Xiyuan Yang, Haoxin Liu, Ye Yu, Haibo Jin, Yijia Xiao, Wenke Lee, B. Aditya Prakash, Haohan Wang
- **机构**：Georgia Institute of Technology / University of Illinois at Urbana-Champaign / University of California, Los Angeles
- **发布时间**：2026-10-01
- **会议/期刊**：（分类 cs.CL, cs.AI, cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2610.02150) | [PDF](https://arxiv.org/pdf/2610.02150)
- **项目页**：https://sourcelearn.github.io/ | GitHub: https://github.com/luchengfu6/SourceLearn

## 摘要翻译

### 英文摘要
Large language model (LLM) agents increasingly rely on persistent external sources to solve sequences of knowledge-intensive tasks. Existing methods improve how source content is accessed and organized, while agent-memory systems preserve reusable knowledge from prior interactions, but repeated use of the same source is still largely treated as repeated access rather than an opportunity to progressively improve understanding of that source. We study source learning: developing reusable source-specific competence over a persistent authoritative source. We represent this competence with a persistent source model that captures reusable understanding of the source, including how its knowledge is structured, interpreted, and applied. To construct and progressively refine such models, we propose SourceLearn, which combines two complementary learning mechanisms. Self-Directed Source Learning identifies what remains incompletely understood and adaptively revisits the source, while Task-Guided Source Learning uses downstream experience to reveal local representational gaps and recurring needs in how source knowledge should be organized.

### 中文翻译
大语言模型（LLM）智能体越来越依赖持久的外部信息源来解决一系列知识密集型任务。现有方法改进的是"如何访问与组织源内容"，agent-memory 系统则保存来自先前交互的可复用知识；但对同一源的反复使用仍主要被视为"反复访问"，而非"渐进式提升对源的理解"的机会。本文研究 **source learning（源学习）**：在持久、权威的信息源之上发展可复用的、源专属的能力（source-specific competence）。作者用一个持久的 **source model** 来表示这种能力，它捕获对源的可复用理解，包括其知识如何被结构化、解释与应用。为构建并渐进式地精炼这种模型，作者提出 **SourceLearn**，它结合两种互补的学习机制：**Self-Directed Source Learning** 识别尚未被充分理解的内容并自适应地回访源；**Task-Guided Source Learning** 则利用下游经验揭示局部的表征缺口，以及源知识组织方式上的反复需求。

### 核心要点提炼
- **研究背景**：LLM agent 依赖持久外部源，但现有方法要么改进"访问"，要么保存"记忆"，都未把反复用源当作学习机会。
- **研究动机**：把"访问知识"升级为"学习源"——为持久权威源发展可复用的源专属能力。
- **核心方法**：SourceLearn，用持久 source model + 两种互补机制（自定向学习 + 任务引导学习）渐进构建能力。
- **主要结果**：5 个基准、3 个 LLM 后端，15 个设置中 **13 个最优**，相对 Hybrid RAG 最高提升 **22.6 分**。
- **研究意义**：为 LLM agent 的持续学习提供新范式，将"反复用源"转化为"渐进式理解源"的累积过程。

## 研究背景与动机

### 领域现状
- RAG 类方法改进源内容的访问与组织（GraphRAG、LightRAG、HippoRAG、StructRAG 等）。
- agent-memory 系统（MemGPT、Reflexion、A-MEM、ReMe 等）保存并复用先前交互的可复用知识。

### 现有方法的局限性
- 两者都把"反复使用同一源"当作**反复访问**，而非**渐进式提升对源理解**的机会。
- 静态源表征与经验记忆基线都未能把"对源的理解"本身作为学习的对象。

### 研究动机
能否将源视为一个**持续学习的对象**，让 agent 在反复使用同一权威源的过程中，渐进式积累对源的、可复用的专属能力？

## 研究问题

### 核心研究问题
两个关键挑战：
1. **在未知未来任务前该学什么源知识？** —— 源信息远多于应保留的量，单遍表征难以连接分布式信息或捕获隐含条件。
2. **任务级反馈如何超越产生它的任务、提升源专属能力？** —— 直接保留观察到的答案会"学到任务而非学到源"，必须用任务级证据定位源理解的缺陷，并用可复用、源 grounded 的知识加以修正。

## 方法概述

### 核心思想
SourceLearn 维护一个显式、可修订的 **source model** $M$：它不复制源的全部内容，而是保存对源的紧凑、可复用理解（知识如何结构化、解释、应用），把易检索的低层细节留在原源中——源始终是事实的权威来源。两种机制共同渐进式地精炼 $M$。

### 方法框架

#### 整体架构
![[2610.02150_page1.png|600]]

> 图1：SourceLearn 框架示意。持久 source model $M$ 在反复使用同一权威源的过程中，通过 Self-Directed 与 Task-Guided 两种机制渐进式构建源专属能力。

- **Self-Directed Source Learning**：识别尚未被充分理解的内容，自适应回访源，跨实体连接知识，主动加深不完整的理解。
- **Task-Guided Source Learning**：利用下游任务中的失败与反复需求，定位局部表征缺口，改进源知识的组织方式。
- **共同原则**：学习信号决定"该重新考虑什么"，而持久更新始终**从权威源重构**，避免直接存储交互经验（防止"学到任务而非学到源"）。

### 关键创新
1. **Source Learning 范式** —— 将"源专属能力"作为学习的对象本身，而非仅改进访问或保存记忆。
2. **持久 source model** —— 显式、可修订地表征对源的理解，与直接访问源互补、又不替代源的事实权威性。
3. **双机制互补学习** —— 自定向（主动补全）+ 任务引导（被动修正）共同驱动渐进式能力构建。

## 实验结果

### 数据集
- 覆盖 **document QA、code QA、tool use、interactive environments** 四类，共 5 个基准。
- 3 个 LLM 后端。

### 主要结果
- 15 个设置中 **13 个最优**。
- 相对 Hybrid RAG 最高提升 **22.6 分**，整体显著优于静态源表征与经验记忆基线。
- 自定向与任务引导两种机制均有贡献。

## 深度分析

### 研究价值
- **理论贡献**：明确提出 source learning 概念，将 agent 对持久源的使用从"访问"提升为"学习"。
- **实际应用**：对 API 文档、代码库、知识库等长期反复使用的权威源，能显著提升 agent 的持续学习能力。
- **领域影响**：为 LLM agent 的持续学习、memory 系统与 RAG 的融合提供了新视角。

### 优势
- 概念清晰、问题定位准确（两问切中要害）。
- 强调"从权威源重构更新"，避免污染，设计原则扎实。
- 实验覆盖广（5 基准 × 3 后端），结论稳健。

### 局限性
- 源模型的表征形式与更新成本对超大规模源的可扩展性待验证。
- 两类机制的触发与调度策略细节可能影响实际效率。

### 未来工作
- 探索 source model 的表征与压缩，扩展到更大规模、动态变化的源。
- 将 source learning 与具身/多模态 agent 结合。
