---
date: "2026-09-18"
paper_id: "arXiv:2609.19445"
title: "From Models to Systems: A Comprehensive Survey of Efficient Multimodal Learning"
authors: "Pan Wang, Siwei Song, Hui Ji, Siqi Cao, Heng Yu, Zhijian Liu, Huanrui Yang, Yingyan (Celine) Lin, Beidi Chen, Mohit Bansal, Xiaoming Liu, Pengfei Zhou, Ming-Hsuan Yang, Tianlong Chen, Jingtong Hu"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - 高效多模态学习
  - 模型压缩
  - 综述
quality_score: "8.0/10"
related_papers: []
created: "2026-09-18"
updated: "2026-09-18"
status: analyzed
---

# From Models to Systems: A Comprehensive Survey of Efficient Multimodal Learning

## 核心信息
- **论文ID**：arXiv:2609.19445
- **作者**：Pan Wang, Siwei Song, Hui Ji, Siqi Cao, Heng Yu, Zhijian Liu, Huanrui Yang, Yingyan (Celine) Lin, Beidi Chen, Mohit Bansal, Xiaoming Liu, Pengfei Zhou, Ming-Hsuan Yang, Tianlong Chen, Jingtong Hu
- **机构**：University of Pittsburgh、UNC Chapel Hill、Carnegie Mellon University、UC Merced、Georgia Tech、UCSD、University of Arizona、New York University、Stanford University
- **发布时间**：2026-09-16
- **会议/期刊**：--（cs.MM / cs.AI / cs.CL / cs.CV / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2609.19445) | [PDF](https://arxiv.org/pdf/2609.19445)
- **引用**：--

## 摘要翻译

### 英文摘要
The rapid expansion of multimodal models has surfaced formidable bottlenecks in computation, memory, and deployment, catalyzing the rise of Efficient Multimodal Learning (EML) as a pivotal research frontier. Despite intensive progress, a cohesive understanding of what, how, and where efficiency is manifested across the learning stack remains fragmented. This survey systematizes the EML landscape by introducing the first structured, model-to-system taxonomy. We distill insights from over 300 seminal works into three hierarchical levels—model, algorithm, and system—addressing architectural parsimony, execution refinement, and hardware-aware orchestration, respectively. Moving beyond a purely categorical review, we offer a methodological synthesis of the vertical synergies between these layers, elucidating how cross-layer co-design contributes to the fundamental "Efficiency-Utility-Privacy" trade-off. Through an integrative case study of Multimodal Large Language Models (MLLMs), we trace the field's evolutionary trajectory from initial structural adjustments to modern full-stack resource orchestration. Furthermore, we provide a holistic discussion and application-specific optimization blueprints for diverse domains and posit a paradigm shift toward self-regulating intelligence, where efficiency is an intrinsic, emergent property of the model's fundamental design rather than a post-hoc constraint. Finally, we present open challenges and future directions that will define the trajectory of EML research.

### 中文翻译
多模态模型的快速扩张带来了计算、内存与部署方面的严峻瓶颈，催生了「高效多模态学习」（Efficient Multimodal Learning, EML）这一关键研究前沿。尽管进展密集，学界对「效率在学习栈的什么位置、以何种方式、在哪里体现」仍缺乏统一认识。本综述首次提出结构化的「从模型到系统」分类体系，系统梳理 EML 版图：从 300 余篇代表性工作中提炼出**模型、算法、系统**三个层级，分别对应架构精简、执行精化与硬件感知的编排。除分类性综述外，作者还对层级间的「纵向协同」进行了方法论综合，阐明跨层协同设计如何作用于根本的「效率—效用—隐私」权衡。通过对多模态大语言模型（MLLM）的整合案例研究，作者追踪了该领域从早期结构调整到现代全栈资源编排的演进轨迹。此外，作者还提供了针对不同领域的整体性讨论与应用专属优化蓝图，并提出了向「自调节智能」的范式转变——效率应是模型基础设计中内生的涌现属性，而非事后施加的约束。最后，作者给出将定义 EML 研究走向的开放挑战与未来方向。

### 核心要点提炼
- **研究背景**：多模态模型规模膨胀，计算、内存、部署三重瓶颈凸显。
- **研究动机**：EML 研究零散，缺乏统一、结构化的理解框架。
- **核心方法**：提出「模型—算法—系统」三层分类体系，综合 300+ 工作，并强调跨层协同设计。
- **主要结果**：通过 MLLM 案例刻画 EML 从结构调整到全栈编排的演进，给出应用蓝图与未来挑战。
- **研究意义**：为高效多模态系统建立统一框架，推动效率成为模型「内生属性」。

## 研究背景与动机

### 领域现状
多模态模型（VLM、MLLM）在多模态理解与生成上取得巨大成功，但训练与推理成本随规模急剧上升。压缩、蒸馏、量化、剪枝、参数高效微调（PEFT）、系统级调度等技术各自为战，形成庞杂的 EML 研究版图。

### 现有方法的局限性
- **碎片化**：模型层（架构精简）、算法层（执行精化）、系统层（硬件编排）的研究彼此割裂，缺乏统一框架。
- **瓶颈错位**：研究重心偏向架构设计与权重压缩，而实际部署瓶颈已转移到 KV cache、I/O 带宽、异构调度等物理约束。
- **权衡不清晰**：「效率—效用—隐私」三重权衡缺乏系统性阐明。

### 研究动机
需要一个「从模型到系统」的统一分类体系，把 300+ 工作的经验归纳为可操作的结构，揭示跨层协同如何真正落地效率，并指出未来「自调节智能」的范式方向。

## 研究问题

### 核心研究问题
1. 如何用统一的分类框架组织 EML 的「模型—算法—系统」三层研究？
2. 跨层协同设计如何作用于「效率—效用—隐私」的根本权衡？
3. EML 的演进轨迹（尤其 MLLM）与未来关键挑战是什么？

## 方法概述

### 核心思想
将 EML 从「单点技术罗列」升级为「结构化分层 + 纵向协同」的系统化理解：模型层负责架构精简（architectural parsimony），算法层负责执行精化（execution refinement），系统层负责硬件感知编排（hardware-aware orchestration），并强调三层之间的垂直协同（cross-layer co-design）。

### 方法框架

#### 整体架构
三层分层 + 协同分析 + MLLM 案例 + 应用蓝图：

![[Fig1_overall_page1.png|600]]

> 图1：EML 的「模型—算法—系统」三层分类体系总览（论文 Fig 1）。

#### 各模块详细说明

**模型层（Model）——架构精简**
- 覆盖轻量化骨干、多模态融合的稀疏化、结构重参数化、紧凑型 VLM 架构等。
- 目标：在架构层面从源头减少参数与计算。

**算法层（Algorithm）——执行精化**
- 覆盖量化、剪枝、蒸馏、PEFT、知识复用、训练效率优化等。
- 目标：在不改架构的前提下精化执行，压缩权重/激活/通信。

**系统层（System）——硬件感知编排**
- 覆盖 KV cache 管理、I/O 带宽优化、异构调度、边缘—云协同、内存层级利用等。
- 目标：面向真实物理约束（而非仅 FLOPs）做端到端编排。

**跨层协同（Cross-layer co-design）**
- 阐明模型/算法/系统如何联合设计，才能触及「效率—效用—隐私」的 Pareto 前沿。

### 关键创新
1. **首个「模型到系统」结构化分类**——统一 EML 零散研究。
2. **纵向协同方法论**——超越平面罗列，揭示跨层协同对「效率—效用—隐私」权衡的作用。
3. **范式判断**——提出「自调节智能」（效率内生化）这一未来方向。

## 实验结果

### 数据集
- 综述性工作，无独立实验；基于 300+ 论文的经验归纳。

### 主要结果
- 归纳出三层分类体系与跨层协同关系。
- 以 MLLM 为例刻画「结构调整 → 全栈编排」的演进轨迹。
- 给出多领域（端侧、边缘—云、具身等）应用专属优化蓝图。

## 深度分析

### 研究价值
- **理论贡献**：提供统一的概念框架与术语，帮助定位与串联 EML 研究。
- **实际应用**：为系统设计者提供跨层协同的优化蓝图，指导工程落地。
- **领域影响**：可作为 EML 领域的「地图」，影响后续研究的问题设定与基准设计。

### 优势
1. 覆盖全面（300+ 工作）、结构清晰。
2. 强调「系统瓶颈」与「跨层协同」，视角比单纯算法综述更贴近真实部署。
3. 提供持续更新的在线版本（GitHub）。

### 局限性
1. 综述本身无定量结论，读者需自行判断各技术的相对优劣。
2. 「自调节智能」的范式判断仍属前瞻性设想，缺乏实证支撑。
3. 300+ 工作的归类存在不可避免的主观性。

### 适用场景
- 入门 EML / 高效多模态系统的研究者建立全局认知。
- 系统工程师定位部署瓶颈并查找跨层优化方案。

## 与相关论文对比

### [[LLaVA]] / 多模态大模型相关综述
- **差异**：多数综述聚焦架构或能力，本文聚焦「效率」并扩展到系统层。
- **改进**：把效率当作贯穿模型—算法—系统的第一性主线。
- **关系类型**：补充 / 扩展。

## 技术路线定位

本文属于**高效多模态学习（EML）**综述路线，核心贡献是把碎片化研究组织为「模型—算法—系统」框架，并锚定「效率内生化」这一长期目标。

## 未来工作建议

1. 建立 EML 的统一评测基准（含真实硬件指标，而非仅 FLOPs/参数量）。
2. 实证验证「跨层协同」相对单点优化的增益幅度。
3. 探索「自调节智能」——效率作为模型设计内生属性的具体实现路径。

## 我的综合评价

### 价值评分
- **总体评分**：**8.0/10**——作为领域地图价值高，适合建立系统观。
- **分项评分**：
  - 创新性：7/10（框架组织有贡献，非全新方法）
  - 技术质量：8/10（结构严谨、覆盖广）
  - 实验充分性：6/10（综述无实验）
  - 写作质量：8/10（清晰）
  - 实用性：8/10（工程指导性强）

### 突出亮点
1. 「模型—算法—系统」三层 + 跨层协同的统一视角。
2. 对「部署瓶颈已从 FLOPs 转移到 KV cache/带宽/调度」的判断很具启发性。
3. 持续更新的在线版本便于跟踪。

### 重点关注
- 「效率—效用—隐私」三元权衡的表述与示例。
- MLLM 案例中的全栈编排实践。

### 可借鉴点
- 用「分层 + 协同」框架组织任一复杂技术领域的综述。
- 部署优先的思维：从真实物理约束反推算法设计。

### 批判性思考
- 综述的价值在于「地图」而非「结论」，需结合具体论文深入阅读。
- 「自调节智能」概念仍较模糊，需警惕口号化。

## 我的笔记

%% 用户阅读后补充 %%

## 相关论文
- [[LLaVA]] - 多模态大模型的代表性架构之一
- 其他 EML 相关综述与压缩方法（见在线仓库）

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.19445)
- [PDF](https://arxiv.org/pdf/2609.19445)
- [GitHub 持续更新版](https://github.com/pwang322/Efficient-Multimodal-Learning-Survey)

> [!tip] 关键启示
> 多模态模型的效率瓶颈已从「理论 FLOPs」转移到「KV cache、I/O 带宽、异构调度」等物理约束——效率优化必须跨模型、算法、系统三层协同。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐给需要建立高效多模态系统全局观的研究者与工程师。
