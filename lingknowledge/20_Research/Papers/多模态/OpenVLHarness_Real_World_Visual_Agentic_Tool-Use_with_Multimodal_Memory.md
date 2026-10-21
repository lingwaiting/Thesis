---
date: "2026-10-21"
paper_id: "arXiv:2610.26860"
title: "OpenVLHarness: Real World Visual Agentic Tool-Use with Multimodal Memory"
authors: "Yu Zhou, Cheng-Fu Yang, Rui Sun, Di Wu, Sibo Peng, Mingyang Zhang, Zhen Yang, Ruiyi Zhang, Nanyun Peng, Zhe Gan, Kai-Wei Chang"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - MLLM
  - 视觉智能体
  - Tool-Use
  - Agentic
  - Multimodal-Memory
quality_score: "8.4/10"
related_papers: []
created: "2026-10-21"
updated: "2026-10-21"
status: analyzed
---

# OpenVLHarness: Real World Visual Agentic Tool-Use with Multimodal Memory

## 核心信息
- **论文ID**：arXiv:2610.26860
- **作者**：Yu Zhou, Cheng-Fu Yang, Rui Sun, Di Wu, Sibo Peng, Mingyang Zhang, Zhen Yang, Ruiyi Zhang, Nanyun Peng, Zhe Gan, Kai-Wei Chang
- **机构**：--
- **发布时间**：2026-10-19
- **会议/期刊**：arXiv（cs.CL / cs.AI / cs.CV / cs.LG / cs.MM）
- **链接**：[arXiv](https://arxiv.org/abs/2610.26860) | [PDF](https://arxiv.org/pdf/2610.26860)
- **引用**：--

## 摘要翻译

### 英文摘要
Multimodal large language models (MLLMs) have made rapid progress on visual reasoning, yet many tasks require evidence that is difficult to obtain through internal reasoning alone: our controlled study on 22 benchmarks shows that simply increasing the inference budget of the base model yields only modest gains. Such tasks demand grounded perception, external knowledge, and numerical measurements obtained through heterogeneous tools, whose outputs must be made interpretable to the model and retained across reasoning steps. We introduce OpenVLHarness, a multimodal reasoning harness organized around a capability layer and persistent multimodal memory. The capability layer combines task-level tool interfaces with evidence rendering, providing consistent access to heterogeneous backends and converting their outputs into model-readable observations. Persistent multimodal memory retains the corresponding images, structured data, and web references as addressable artifacts throughout each query. A coding interface executes generated programs over stored data, enabling computations that combine results from multiple tools. Together, these mechanisms support an iterative process of acquiring, inspecting, and composing multimodal evidence. Evaluated on 22 benchmarks with eight open and proprietary backbones, OpenVLHarness improves average scores by 9.7-12.6 points over matched no-tool baselines, including +11.2 with Qwen3-VL-8B, +9.7 with Kimi K3, and +9.9 with GPT-6 Astra. With GPT-6 Luna at medium reasoning effort, OpenVLHarness outperforms the base model, Codex, and Visual Sketchpad at maximum effort by 7.7-8.5 points while incurring at most 37% of their model-token cost.

### 中文翻译
多模态大语言模型（MLLM）在视觉推理上进展迅速，但许多任务需要仅靠内部推理难以获得的证据：本文在 22 个基准上的受控研究表明，单纯增加基座模型的推理预算只带来有限的提升。这类任务需要基于真实世界的感知、外部知识，以及通过异构工具获得的数值测量，而这些工具的输出必须能被模型理解，并在多步推理中被保留。作者提出 OpenVLHarness，一个围绕"能力层"与"持久多模态记忆"组织的多模态推理 harness。能力层把任务级工具接口与证据渲染相结合，提供对异构后端的统一访问，并把它们的输出转化为模型可读的观测。持久多模态记忆把相应的图像、结构化数据和网页引用作为可寻址的工件贯穿整个查询保留下来。一个编程接口在存储数据上执行生成的程序，从而支持跨多个工具结果的组合计算。这些机制共同支撑起"获取—检视—组合多模态证据"的迭代过程。在 22 个基准、8 个开源与闭源骨干上，OpenVLHarness 相比匹配的无工具基线平均提升 9.7–12.6 分，其中 Qwen3-VL-8B +11.2、Kimi K3 +9.7、GPT-6 Astra +9.9。以中等推理 effort 使用 GPT-6 Luna 时，OpenVLHarness 比全 effort 的基座模型、Codex 和 Visual Sketchpad 高出 7.7–8.5 分，而模型 token 成本至多只有它们的 37%。

### 核心要点提炼
- **研究背景**：MLLM 视觉推理已很强大，但"真实世界工具使用"任务需要外部证据（感知、知识、数值测量），仅靠内部推理不足。
- **研究动机**：增加推理预算收益有限（受控研究证实），关键在于"如何把异构工具的输出变成模型可理解、可跨步保留的证据"。
- **核心方法**：OpenVLHarness = 能力层（工具接口 + 证据渲染）+ 持久多模态记忆 + 编程接口，支撑证据的迭代获取/检视/组合。
- **主要结果**：22 基准上平均 +9.7–12.6 分，且用 GPT-6 Luna 中等 effort 即以 37% 成本超越全 effort 的基座/Codex/Visual Sketchpad。
- **研究意义**：把"视觉智能体工具使用"从零散 prompt 工程提升为系统化的 harness 架构，兼顾效果与推理成本。

## 研究背景与动机

### 领域现状
多模态大模型在图像问答、视觉定位等静态基准上已接近饱和。真实的视觉智能体任务（如"数一数这张图里有多少个 X，并查证它的市场价"）则要求模型主动获取证据：调用检测/分割工具、查询外部知识库、执行数值计算。这类"工具使用 + 视觉推理"的复合能力，是当前 MLLM 走向落地（GUI 操作、文档理解、数据可视化分析等）的关键。

### 现有方法的局限性
- **纯内部推理的收益天花板**：作者在 22 个基准上的受控研究显示，仅提高基座模型的推理 effort，提升很有限——说明瓶颈不在"想得更久"，而在"拿不到对的外部证据"。
- **工具输出的可读性**：异构工具（检测器、OCR、搜索引擎、计算器）的输出格式各异，直接拼接进上下文既占空间又难对齐。
- **跨步记忆缺失**：多步推理中，早期获取的图像、结构化数据、引用在后续步骤容易丢失或不可寻址。

### 研究动机
作者认为需要一套系统架构，把"工具调用"从一次性 prompt 提升为"证据生命周期管理"：统一接入工具 → 把输出渲染成模型可读的观测 → 以可寻址工件持久保存 → 通过代码在多工具结果上做组合计算。

## 研究问题

### 核心研究问题
如何系统性地让多模态大模型在真实世界视觉任务中，可靠地获取、理解、保留并组合来自异构工具的多模态证据，从而以更低的推理成本取得更强的效果？

## 方法概述

### 核心思想
OpenVLHarness 以"能力层 + 持久多模态记忆"为骨架：能力层解决"工具怎么接、输出怎么读"，持久记忆解决"证据怎么存、怎么查"，编程接口解决"多个证据怎么算"。三者让模型能像人类分析师一样"找证据—看证据—算证据"，而不是一次性硬推理。

### 方法框架

#### 整体架构
OpenVLHarness 包含三个核心组件：

1. **能力层（Capability Layer）**：任务级工具接口 + 证据渲染，统一访问检测、OCR、搜索等异构后端；
2. **持久多模态记忆（Persistent Multimodal Memory）**：把图像、结构化数据、网页引用作为可寻址工件跨步保存；
3. **编程接口（Coding Interface）**：在存储数据上执行生成程序，组合多个工具结果做计算。

![[2610.26860_fig2.png|600]]

> 图1：OpenVLHarness 架构示意——能力层接入异构工具并渲染证据，持久记忆保存工件，编程接口组合计算。

#### 各模块详细说明

**模块1：能力层**
- **功能**：提供任务级工具接口，把异构后端的输出转化为模型可读的观测（observation）
- **输入**：任务需求、待处理的多模态输入
- **输出**：模型可读的结构化观测
- **关键技术**：证据渲染（evidence rendering），使工具输出与语言模型的输入空间对齐

**模块2：持久多模态记忆**
- **功能**：把证据（图像、结构化数据、网页引用）作为可寻址工件贯穿查询全程保存
- **输入**：能力层产出的观测
- **输出**：可被后续步骤检索/引用的工件
- **关键技术**：地址化存储，支持跨推理步的证据引用

**模块3：编程接口**
- **功能**：在已存储的数据上执行生成的程序，做跨工具的组合计算
- **输入**：生成的程序、存储的证据
- **输出**：组合计算的结果
- **关键技术**：代码执行使"多工具结果融合"从语言拼接升级为确定性计算

## 实验结果

### 实验目标
验证 OpenVLHarness 相比无工具基线、以及相对其他工具使用范式（Codex、Visual Sketchpad）在效果与成本上的优势。

### 实验设置
- **基准**：22 个多模态基准
- **骨干**：8 个开源与闭源骨干（Qwen3-VL-8B、Kimi K3、GPT-6 Astra、GPT-6 Luna 等）
- **对比**：匹配的无工具基线、基座模型、Codex、Visual Sketchpad

### 主要结果
- 相比无工具基线，平均提升 **9.7–12.6 分**（Qwen3-VL-8B +11.2、Kimi K3 +9.7、GPT-6 Astra +9.9）；
- GPT-6 Luna 中等 effort 下，超越全 effort 的基座/Codex/Visual Sketchpad **7.7–8.5 分**，模型 token 成本仅为其 **37%**。

## 深度分析

### 研究价值评估

#### 理论贡献
- 提出"能力层 + 持久记忆 + 编程接口"的三件套架构，把视觉智能体工具使用系统化；
- 通过受控研究给出"增加推理预算收益有限"的经验证据，明确问题定位。

#### 实际应用价值
- 对 GUI 操作、文档理解、数据分析、科研图表解读等真实视觉任务有直接落地价值；
- "低成本高效果"的结论对推理成本敏感的生产环境尤其有吸引力。

### 方法优势详解
- **系统化而非散点**：统一证据生命周期管理，优于一次性 prompt 拼接；
- **成本优势**：中等 effort 即超过对手全 effort，token 成本降至 37%；
- **可组合计算**：编程接口支持确定性跨工具计算，避免语言拼接的模糊性。

### 局限性分析
- 依赖工具集的质量与覆盖，工具本身的能力瓶颈会传导到 harness；
- "22 个基准"的具体构成与任务类型需读正文确认；
- 复杂工具编排的可靠性与错误恢复机制在摘要中未展开。

### 适用性与场景分析
- **适用**：需要外部感知/知识/数值证据的视觉任务（计数、测量、检索、分析）；
- **不适用**：纯静态感知或无需外部证据的简单视觉问答。

## 技术路线定位

本文属于 **视觉智能体 / MLLM 工具使用（Visual Agentic Tool-Use）** 技术路线，核心是"让 MLLM 主动调用工具获取外部证据"。相比 Visual Sketchpad、Codex 等前期工作，本文的贡献在于把证据的获取、存储、组合做成一个统一的持久化架构。

```
静态视觉问答 → 视觉工具使用（prompt 拼接） → 系统化 harness（OpenVLHarness：能力层+持久记忆+编程） → 未来方向
```

### 具体子方向
多模态证据的"生命周期管理"子方向：获取、渲染、持久化、组合计算。

## 未来工作建议

1. **扩展工具生态**：接入更多视觉/知识/计算后端，并研究工具选择的自动路由；
2. **错误恢复与鲁棒性**：工具失败、证据冲突时的自动重试与消解；
3. **长期记忆跨查询**：把"查询内持久记忆"扩展到"跨查询长期记忆"。

## 我的综合评价

### 总体评分
**8.4/10** —— 问题真实、架构系统、实验扎实（22 基准 8 骨干），且兼顾效果与成本，实用价值突出。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 三件套架构系统化工具使用，非零散改进 |
| 技术质量 | 8/10 | 架构清晰，证据生命周期管理有设计 |
| 实验充分性 | 9/10 | 22 基准 8 骨干 + 成本对比，充分 |
| 写作质量 | 8/10 | 动机（受控研究）+ 方案 + 结果链条完整 |
| 实用性 | 9/10 | 落地价值高，成本优势明显 |

## 相关论文

### 直接相关
- Visual Sketchpad（多模态中间画布/工具使用）
- Codex 等编程式推理范式

### 背景相关
- 视觉智能体、GUI agent（WebArena、OSWorld 等）
- MLLM 工具学习（ToolLLM 系列）

## 外部资源
- [arXiv 页面](https://arxiv.org/abs/2610.26860)

> [!tip] 关键启示
> 视觉智能体的瓶颈不在"想得更久"，而在"拿得到、留得住、算得清外部证据"——系统化的证据生命周期管理，比单纯堆推理预算更有效也更便宜。

> [!warning] 注意事项
> - 效果依赖工具集质量
> - "22 基准"构成需读正文确认
> - 工具编排的错误恢复机制在摘要中未展开
