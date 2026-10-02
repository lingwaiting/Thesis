---
date: "2026-10-02"
paper_id: "arXiv:2610.00562"
title: "Can LLMs Reason Over Long Horizons? An Empirical Evaluation of Context Strategies for Longitudinal Clinical Reasoning"
authors: "Taye Akinrele, Noorbakhsh Amiri Golilarz, Subash Neupane, Sudip Mittal, Shahram Rahimi"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - Long-Context
  - Clinical-Reasoning
  - RAG
quality_score: "8.2/10"
created: "2026-10-02"
updated: "2026-10-02"
status: analyzed
---

# Can LLMs Reason Over Long Horizons? An Empirical Evaluation of Context Strategies for Longitudinal Clinical Reasoning

## 核心信息
- **论文ID**：arXiv:2610.00562
- **作者**：Taye Akinrele, Noorbakhsh Amiri Golilarz, Subash Neupane, Sudip Mittal, Shahram Rahimi
- **机构**：Department of Computer Science, The University of Alabama
- **发布时间**：2026-09-30
- **会议/期刊**：cs.CL / cs.AI / cs.LG
- **链接**：[arXiv](http://arxiv.org/abs/2610.00562) | [PDF](https://arxiv.org/pdf/2610.00562)
- **引用**：--

## 摘要翻译

### 英文摘要
Longitudinal clinical reasoning requires large language models (LLMs) to identify and integrate relevant evidence distributed across extended patient histories. Although long-context models can process increasingly large amounts of information, providing more history does not necessarily make relevant evidence more accessible or improve reasoning. We compare five context strategies (Full, Recent, Episodic, Semantic, and Hybrid) on MedLoCoMo across four open-weight LLMs, examining answer correctness, robustness to query-evidence distance, and abstention on questions with unsupported premises. Episodic and Hybrid generally achieve the strongest overall accuracy, while Recent Context degrades most as supporting evidence becomes more distant; Episodic and Hybrid maintain the highest accuracy at long distances. Analysis of adversarial questions further shows that strong performance on answerable questions does not necessarily translate to successful abstention when the available history does not support the requested conclusion. These findings show that reliable longitudinal reasoning depends not only on how much history an LLM can access, but critically on how relevant evidence is selected and presented for reasoning.

### 中文翻译
纵向（时间跨度的）临床推理要求大语言模型（LLM）识别并整合分布在超长患者病史中的相关证据。尽管长上下文模型能处理越来越多的信息，但"喂给模型更多病史"并不必然让相关证据更易获取，也不必然提升推理能力。本文在 MedLoCoMo 数据集上、跨四个开源权重 LLM，比较了五种上下文策略（Full 全量、Recent 近期、Episodic 情景、Semantic 语义、Hybrid 混合），考察答案正确性、对"查询—证据距离"的鲁棒性，以及在前提不受支持问题上的拒绝回答（abstention）能力。结果表明，Episodic 与 Hybrid 总体准确率最强；当支持证据距离变远时 Recent Context 退化最严重，而 Episodic 与 Hybrid 在长距离下仍保持最高准确率。对对抗性问题的分析进一步揭示：在"可回答问题"上的强表现，并不必然转化为"历史信息不足以支持所求结论"时的成功拒绝。这些发现说明，可靠的纵向推理不仅取决于 LLM 能访问多少病史，更关键地取决于相关证据如何被选择与呈现给推理过程。

### 核心要点提炼
- **研究背景**：纵向临床推理需要从超长病史中定位并整合稀疏分布的相关证据。
- **研究动机**：长上下文模型"能装下更多历史"≠"相关证据更易获取"，上下文组织策略被忽视。
- **核心方法**：系统对比 Full / Recent / Episodic / Semantic / Hybrid 五种上下文策略。
- **主要结果**：Episodic 与 Hybrid 最强；"证据选择与呈现方式"比"喂入多少历史"更关键。
- **研究意义**：为长上下文医疗推理的上下文工程提供了可操作的经验指导。

## 研究背景与动机

### 领域现状
长上下文 LLM 的能力边界不断扩展，医疗领域开始尝试用 LLM 进行纵向（跨时间）临床推理——从跨越数月甚至数年的病历中整合证据以辅助诊断与决策。MedLoCoMo 等数据集为此提供了评测基准。

### 现有方法的局限性
- 主流做法是**把尽可能多的历史塞进上下文**，默认"更多上下文 = 更好推理"。
- 但长上下文并不自动保证**相关证据的可达性**：关键证据可能被淹没在海量无关历史中。
- 缺乏对"如何组织、选择、呈现历史证据"这一上下文策略的系统研究。

### 研究动机
通过系统对比多种上下文组织策略，回答一个关键问题：在纵向临床推理中，真正决定推理可靠性的是"能访问多少历史"，还是"如何选择与呈现相关证据"。

## 研究问题

### 核心研究问题
1. 不同的上下文策略（Full / Recent / Episodic / Semantic / Hybrid）对纵向临床推理的准确率有何影响？
2. 各策略对"查询—证据距离"的鲁棒性如何？
3. 在前提不受支持的问题上，各策略的拒绝回答（abstention）能力如何？

## 方法概述

### 核心思想
将"如何构造喂给 LLM 的上下文"显式化为五种可对比的策略，从准确率、距离鲁棒性、拒绝回答三个维度进行评测，从而揭示"证据选择与呈现"比"历史长度"更重要的结论。

### 方法框架

#### 整体架构

![[overview_context_page1.png|800]]

> 图1：五种上下文策略的整体示意，展示不同策略如何从完整病史中选择与组织证据。

#### 各模块详细说明

**模块1：五种上下文策略**
- **Full**：将完整患者病史全部喂入。
- **Recent**：只保留最近时间窗口内的记录（支持证据越远，退化越严重）。
- **Episodic**：按"事件/情景"切分与组织病史，聚焦与查询相关的事件片段。
- **Semantic**：基于语义相关性检索与组织证据。
- **Hybrid**：结合 Episodic 与 Semantic 的混合策略。

**模块2：评测维度**
- **答案正确性**：在 MedLoCoMo 上的回答准确率。
- **距离鲁棒性**：当支持证据与查询的距离增大时，准确率的保持程度。
- **拒绝回答（abstention）**：对"历史不足以支持结论"的对抗性问题的拒绝能力。

**模块3：多模型验证**
- 在四个开源权重 LLM 上验证结论的跨模型一致性。

## 实验结果

### 主要结果

#### 主实验结果
- **Episodic 与 Hybrid 总体准确率最强**，且在不同模型上表现一致。
- **Recent Context 退化最严重**：当支持证据距离变远时，准确率下降最快。
- **Episodic 与 Hybrid 在长距离下仍保持最高准确率**。

#### 结果分析
- "可回答性"与"拒绝能力"出现**分离**：在可回答问题上表现好的策略，未必能在证据不足时正确拒绝。这提示评测不能只看准确率。

### 实验结果图

![[rq2_distance_pointplot_models_page1.png|800]]

> 图2：不同策略随"查询—证据距离"变化的准确率点图，展示 Episodic/Hybrid 在长距离下的优势与 Recent 的退化。

![[judge_agreement_radar_page1.png|800]]

> 图3：判断一致性（judge agreement）雷达图，展示多模型间的评测一致性。

## 深度分析

### 研究价值评估

#### 理论贡献
- 澄清了"长上下文 ≠ 更好的纵向推理"这一关键认知，将研究焦点从"上下文长度"转向"证据组织"。
  - 创新点：系统化对比五种上下文策略的多维度评测。
  - 学术价值：为长上下文推理的上下文工程提供实证基础。
  - 影响范围：医疗 AI、长上下文 LLM、RAG 系统设计。

#### 实际应用价值
- **应用场景1：临床决策支持系统**
  - 适用性：指导如何组织患者病史以提升 LLM 推理可靠性。
  - 优势：Episodic/Hybrid 策略可直接落地。
  - 潜在影响：提升医疗 LLM 的安全性与可信度。

#### 领域影响
- **短期影响**：为医疗长上下文评测与系统设计提供可复现的策略对比。
- **中期影响**：推动"上下文工程"成为长上下文研究的标准议题。
- **长期影响**：启发通用的证据选择与呈现机制（不限于医疗）。

### 方法优势详解

#### 优势1：多维评测
- **描述**：同时考察正确性、距离鲁棒性、拒绝回答三个维度，比单一准确率更全面。

#### 优势2：跨模型一致性
- **描述**：在四个开源模型上验证，结论稳健。

### 局限性分析

#### 局限1：领域单一
- **描述**：集中在临床医疗领域，结论向其他长上下文任务的泛化性待验证。

#### 局限2：策略粒度较粗
- **描述**：五种策略的实现细节（如 Episodic 的切分粒度）存在调参空间。

### 适用性与场景分析

#### 适用场景
- 医疗/法律等需要从超长时序文档中整合证据的推理任务。
- RAG 系统中历史证据的组织与检索策略设计。

#### 不适用场景
- 证据集中在短上下文、无需跨时序整合的简单问答。

## 与相关论文对比

### 对比论文选择依据
选择长上下文推理与医疗 LLM 评测的代表性工作作为参照。

### [[MedLoCoMo|MedLoCoMo: Benchmarking LLMs for Longitudinal Clinical Reasoning]]
- **关系**：本文使用的评测基准，是其直接延续。
- **本文改进**：在该基准上系统化研究上下文策略的影响。

### [[Long-Context-LLM|Long-Context LLM]]
- **关系**：长上下文能力是本文研究的前提。
- **本文贡献**：指出"长度之外"，证据组织才是纵向推理的关键。

### 对比总结
本文的独特价值在于把"上下文策略"从隐式选择变成显式变量，从而得出可操作、可迁移的结论。

## 技术路线定位

### 所属技术路线
本文属于**长上下文推理 + 上下文工程**技术路线，核心特点：
- 关注"如何喂给模型"与"喂什么"，而非单纯扩大上下文窗口。

### 本文在技术路线中的位置
- **承上**：建立在 MedLoCoMo 与长上下文能力之上。
- **启下**：为"证据选择与呈现"机制（如更好的 episodic 检索）提供实证指引。

## 未来工作建议

### 基于分析的未来方向
1. **方向1：跨领域泛化**
   - 动机：在医疗之外的法律、金融等长时序领域验证结论。
2. **方向2：自适应策略选择**
   - 动机：根据查询类型动态选择最优上下文策略。
3. **方向3：拒绝回答能力的专项优化**
   - 动机：解决"可回答强但拒绝弱"的分离问题，提升安全性。

## 我的综合评价

### 价值评分

#### 总体评分
**8.2/10** - 实证扎实、结论清晰且具有可操作性，为长上下文推理的上下文工程提供了有价值的经验指导。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 系统化对比而非全新方法，但视角清晰 |
| 技术质量 | 8/10 | 评测设计多维、跨模型验证充分 |
| 实验充分性 | 8/10 | 五策略 × 四模型 × 三维度覆盖完整 |
| 写作质量 | 8/10 | 结论明确、结构清晰 |
| 实用性 | 9/10 | 对医疗 LLM 落地有直接指导意义 |

## 相关论文

### 直接相关
- [[MedLoCoMo]] - 纵向临床推理基准
- [[Long-Context-LLM]] - 长上下文模型能力

> [!tip] 关键启示
> 在纵向（跨时间）推理中，真正决定可靠性的是"证据如何被选择与呈现"，而非"能访问多少历史"；Episodic/Hybrid 这类按事件组织证据的策略显著优于"全量堆砌"或"只看近期"。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐。对做医疗/长上下文 RAG 的读者有直接参考价值，结论可操作。
