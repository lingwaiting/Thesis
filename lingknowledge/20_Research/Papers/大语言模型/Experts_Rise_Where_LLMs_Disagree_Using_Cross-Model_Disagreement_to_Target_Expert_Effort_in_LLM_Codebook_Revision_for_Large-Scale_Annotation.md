---
date: "2026-09-24"
paper_id: "arXiv:2609.26926"
title: "Experts Rise Where LLMs Disagree: Using Cross-Model Disagreement to Target Expert Effort in LLM Codebook Revision for Large-Scale Annotation"
authors: "Zeyu He, Zhuqian Zhou, Kirk Vanacore, Rene F. Kizilcec, Ting-Hao 'Kenneth' Huang"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 数据标注
  - 人机协作
quality_score: "8.5/10"
related_papers: []
created: "2026-09-24"
updated: "2026-09-24"
status: analyzed
---

# Experts Rise Where LLMs Disagree: Using Cross-Model Disagreement to Target Expert Effort in LLM Codebook Revision for Large-Scale Annotation

## 核心信息
- **论文ID**：arXiv:2609.26926
- **作者**：Zeyu He, Zhuqian Zhou, Kirk Vanacore, Rene F. Kizilcec, Ting-Hao 'Kenneth' Huang
- **机构**：--
- **发布时间**：2026-09-22
- **会议/期刊**：cs.CL / cs.AI / cs.HC / cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2609.26926) | [PDF](https://arxiv.org/pdf/2609.26926)
- **引用**：--

## 摘要翻译

### 英文摘要
Large-scale text annotation brings expert insight to millions of documents, often through a codebook that AI annotators follow. Developing a robust codebook, however, takes months. Large language models (LLMs) could speed this process by applying an early codebook to the data, surfacing cases with strong LLM disagreement, and eliciting expert feedback to address them. We examined three ways experts can provide feedback for LLM codebook revision: (i) editing LLM-generated revisions driven by cross-LLM disagreement (Codebook Verifying), (ii) answering questions about LLM disagreements (Question Answering), and (iii) labeling disagreement cases with rationales (Rationale Labeling). Experiments on thousands of tutoring-session transcripts show that Rationale Labeling yielded the highest LLM-labeling accuracy (64.9%) against expert labels, outperforming the expert-revised codebook (57.8%). The best Question Answering setting also outperformed it (60.5%). Our work shows that LLMs can be used to strategically target expert attention, shortening months of codebook revision to days without sacrificing labeling performance.

### 中文翻译
大规模文本标注通过一套 AI 标注器遵循的 codebook（编码手册）将专家洞察扩展到数百万份文档上。然而，构建一套稳健的 codebook 往往需要数月时间。大语言模型（LLM）可以通过如下方式加速这一过程：先将一个早期 codebook 应用到数据上，标出 LLM 之间分歧强烈的案例，再引出专家反馈来针对性解决。本文考察了三种专家为 LLM codebook 修订提供反馈的方式：(i) 编辑由跨 LLM 分歧驱动的 LLM 生成修订（Codebook Verifying）；(ii) 回答关于 LLM 分歧的问题（Question Answering）；(iii) 用理由（rationale）标注分歧案例（Rationale Labeling）。在数千条辅导会话转写文本上的实验表明，Rationale Labeling 取得了最高的 LLM 标注准确率（64.9%，以专家标注为金标准），超过了专家修订后的 codebook（57.8%）。最佳 Question Answering 设置也超过了它（60.5%）。本工作表明，LLM 可以被用于策略性地定位专家注意力，在不牺牲标注性能的前提下，将数月的 codebook 修订缩短至数天。

### 核心要点提炼
- **研究背景**：大规模文本标注依赖 codebook，但其开发耗时长、成本高。
- **研究动机**：利用 LLM 间分歧定位真正需要专家判断的案例，把稀缺的专家精力花在刀刃上。
- **核心方法**：用跨模型分歧定位专家注意力，比较三种专家反馈范式。
- **主要结果**：Rationale Labeling 达 64.9% 标注准确率，超过专家修订 codebook 的 57.8%。
- **研究意义**：证明 LLM 可作为"注意力路由"工具，显著压缩 codebook 迭代周期。

## 研究背景与动机

### 领域现状
大规模文本标注（如教育、医疗、社会科学的质性编码）通常依赖一份 codebook，AI 标注器（规则或模型）依据它来对海量文档打标签。codebook 的质量直接决定标注质量，但其开发是一个高度依赖领域专家的迭代过程。

### 现有方法的局限性
传统 codebook 开发以"全量专家审阅"为主，专家需要浏览大量文档、反复修订规则，周期长达数月。即使引入 LLM 辅助，通常也只是让 LLM 直接生成修订草案，仍缺少"该把专家注意力投到哪里"的智能路由机制。

### 研究动机
核心洞察：当多个 LLM 对同一案例给出不一致判断时，这些"分歧案例"往往正是 codebook 模糊、规则缺失或边界不清的地方——即最需要专家介入之处。若能自动定位这些案例，就能把专家的稀缺时间从低信息量的浏览中解放出来。

## 研究问题

### 核心研究问题
在 LLM 辅助 codebook 修订的闭环中，**专家应以何种形式对"跨模型分歧案例"提供反馈，才能最高效地提升修订后 codebook 的标注性能？**

## 方法概述

### 核心思想
把 LLM 视作"分歧探测器"而非"修订器"。先用一个早期 codebook 让多个 LLM 对数据打分，凡是 LLM 之间分歧大的案例即被标记为"需要专家关注"；专家只针对这些案例提供反馈，反馈被用于修订 codebook，进入下一轮迭代。

![[codebook-workflow-v202609112356.png|800]]

> 图1：基于跨模型分歧的 codebook 修订工作流——早期 codebook + 多 LLM 打分 → 分歧定位 → 专家反馈 → codebook 修订的迭代闭环。

### 方法框架

#### 整体架构
工作流包含四个阶段：
1. **早期 codebook 应用**：用初始 codebook 让多个 LLM 对文档进行标注。
2. **分歧检测**：计算跨模型的一致性，标记分歧案例。
3. **专家反馈**：专家针对分歧案例，以下述三种方式之一提供反馈。
4. **codebook 修订**：将反馈整合回 codebook，进入下一轮。

#### 三种专家反馈范式
- **Codebook Verifying（修订校验）**：专家直接编辑由 LLM 生成的 codebook 修订草案（这些草案本身由跨 LLM 分歧驱动）。
- **Question Answering（问答）**：系统针对 LLM 分歧点向专家提问，专家作答。
- **Rationale Labeling（理由标注）**：专家为分歧案例打标签，并附上 rationale（判断依据）。

### 关键创新
1. **分歧即信号**：把"跨模型分歧"作为定位专家注意力的核心信号，而非单纯追求 LLM 间一致性。
2. **反馈范式对比**：系统比较三种专家反馈形式，发现"带理由的标注"最有效。
3. **闭环压缩**：证明 LLM 路由 + 专家反馈可将数月 codebook 迭代压缩到数天。

## 实验结果

### 数据集
- 数千条 tutoring-session（辅导会话）转写文本，涉及教育领域的复杂质性标注任务。

### 实验设置
- **评估指标**：LLM 标注准确率（以专家标注为金标准）。
- **关键对比**：三种专家反馈范式 vs. 专家直接修订 codebook 的基线。

### 主要结果
| 方法 | LLM 标注准确率 |
|------|----------------|
| 专家修订 codebook（基线） | 57.8% |
| Codebook Verifying | （低于 Rationale Labeling） |
| Question Answering（最佳设置） | 60.5% |
| **Rationale Labeling** | **64.9%** |

> 注：Rationale Labeling 相较专家修订 codebook 基线提升约 +7.1 个百分点。

#### 结果分析
- 让专家为分歧案例**标注理由**比直接修订规则更有效——理由承载了专家判断背后的隐含标准，而这些标准往往比显式规则更丰富、更可迁移。
- 问答范式次之，说明"针对性提问"也能有效引导专家注意力，但不如理由标注充分。

### 消融实验
论文比较了三种范式的相对收益，验证"分歧定位"这一前置环节的贡献；分歧定位越精准，专家反馈的杠杆效应越强。

## 深度分析

### 研究价值
- **理论贡献**：提出"分歧即注意力路由信号"的框架，将人机协作标注从"LLM 代劳"升级为"LLM 定位 + 专家裁决"。
- **实际应用**：可直接落地于教育、医疗、社会科学等需要高质量质性编码的场景。
- **领域影响**：为 Human-AI Collaboration 与 Data-Centric AI 提供了一个低成本、高杠杆的范式。

### 优势
- 稀缺专家精力的高效利用，显著缩短 codebook 开发周期。
- 方法轻量、无需额外训练，仅依赖现成 LLM 的推理能力。
- 结论有清晰的因果归因（分歧案例 → 专家反馈 → 性能提升）。

### 局限性
- 实验集中于单一领域（辅导会话转写），跨领域泛化性待验证。
- "跨模型分歧"的质量依赖所选 LLM 集合的多样性与能力。
- 64.9% 的绝对准确率仍不算高，复杂标注任务的瓶颈依旧存在。

### 适用场景
- 需要快速构建 codebook 的大规模质性标注项目。
- 专家资源稀缺、希望最大化专家投入产出比的任务。

## 技术路线定位

本文属于 **Human-AI Collaborative Annotation / Data-Centric AI** 路线，核心是"用模型分歧做主动学习的查询策略"。它与 [[RLAIF]]、[[Constitutional AI]] 等"模型辅助标注"工作同源，但把焦点从"生成偏好数据"转向"定位专家注意力"。

## 未来工作建议

1. 在更多领域（法律、医疗）验证分歧路由的泛化性。
2. 将 Rationale 结构化为可检索的知识库，实现跨项目的 codebook 复用。
3. 结合主动学习，动态选择"分歧最大 + 信息增益最高"的案例反馈给专家。

## 我的综合评价

### 价值评分
- **总体评分**：8.5/10
- **分项评分**：
  - 创新性：7/10（思路清晰，范式对比扎实，但非颠覆性突破）
  - 技术质量：8/10（实验设计严谨，因果链条清晰）
  - 实验充分性：7/10（单一领域，缺少跨域验证）
  - 写作质量：8/10（motivation 明确，叙述流畅）
  - 实用性：9/10（落地门槛低，价值直接）

### 突出亮点
- "分歧即信号"这一核心洞察简洁有力。
- Rationale Labeling 优于直接修订的结论反直觉且实用。
- 数月 → 数天的压缩比极具说服力。

### 重点关注
- 三种反馈范式的完整定量对比与消融。
- "分歧定位"环节本身如何影响最终增益。

### 可借鉴点
- 用跨模型分歧做主动学习查询策略的思路，可迁移到其他标注/评估任务。
- "让专家标注理由而非直接改规则"的交互设计原则。

### 批判性思考
- 64.9% 的绝对准确率对高 stakes 场景是否足够？
- 跨模型分歧是否可能由模型共性偏差导致，从而漏掉真正难例？
- 专家 rationale 的质量与一致性本身也需要成本控制。

## 我的笔记

%% 用户可在此补充个人阅读笔记 %%

## 相关论文
- [[RLAIF]] - 用 AI 反馈替代人类反馈做对齐，与本工作的"模型辅助标注"同源
- [[Constitutional AI]] - 通过原则驱动的模型自标注与反思，共享"模型辅助人类"思想

## 外部资源
- [arXiv](https://arxiv.org/abs/2609.26926)
- [PDF](https://arxiv.org/pdf/2609.26926)

> [!tip] 关键启示
> 把 LLM 当作"分歧探测器"而非"标注器"，用跨模型分歧精准路由专家注意力，是人机协作标注中低成本、高杠杆的关键思路。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读——对做数据标注、人机协作、Data-Centric AI 的研究者尤其有启发。
