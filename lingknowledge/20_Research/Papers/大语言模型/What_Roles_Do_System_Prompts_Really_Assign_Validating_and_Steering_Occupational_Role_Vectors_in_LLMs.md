---
date: "2026-11-13"
paper_id: "arXiv:2611.15989"
title: "What Roles Do System Prompts Really Assign? Validating and Steering Occupational Role Vectors in LLMs"
authors: "Jose L. Sampedro Mazon, Ilmi Yoon, Nadim Saad"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - Activation-Steering
  - System-Prompt
  - Interpretability
quality_score: "7.8/10"
related_papers: []
created: "2026-11-13"
updated: "2026-11-13"
status: analyzed
---

# What Roles Do System Prompts Really Assign? Validating and Steering Occupational Role Vectors in LLMs

## 核心信息
- **论文ID**：arXiv:2611.15989
- **作者**：Jose L. Sampedro Mazon, Ilmi Yoon, Nadim Saad
- **机构**：--
- **发布时间**：2026-11-11
- **会议/期刊**：arXiv 预印本（cs.CL / cs.AI / cs.LG）
- **链接**：[arXiv](https://arxiv.org/abs/2611.15989) | [PDF](https://arxiv.org/pdf/2611.15989)
- **引用**：--

## 摘要翻译

### 英文摘要
Applications built on LLMs assign the model a role through its system prompt, intending to shape how it responds. This work introduces a validation framework for extracting and injecting occupational role vectors — activation-level directions capturing how someone in a given occupational role would distinctively respond. The framework validates these vectors through two complementary lenses (interpretability and control) on Qwen2.5-7B-Instruct and Llama-3.1-8B-Instruct.

### 中文翻译
基于 LLM 的应用通过 system prompt 为模型赋予角色，意图塑造其响应方式。本文提出一个验证框架，用于提取和注入"职业角色向量"——一种激活层面的方向，刻画某个职业角色的人在面对任务或问题时的独特响应方式。该框架在 Qwen2.5-7B-Instruct 与 Llama-3.1-8B-Instruct 上，通过"可解释性"与"控制"两个互补视角对角色向量进行验证。

### 核心要点提炼
- **研究背景**：system prompt 的角色赋予机制在激活层面尚不清晰。
- **研究动机**：角色向量是否稳定、可分离、可注入并持续生效？
- **核心方法**：提取并注入"职业角色向量"，用可解释性与控制双视角验证。
- **主要结果**：角色向量高度可复现、可分离；注入向量能显著提升角色表达，但难以在 30 轮对话中延缓角色衰退。

## 研究问题

核心研究问题：**system prompt 在激活层面真正赋予了什么角色？这些"职业角色向量"是否稳定、可分离，并能通过注入实现行为控制？**

## 方法概述

### 核心方法

1. **职业角色向量提取**
   - 提取刻画特定职业角色独特响应的激活方向。

2. **双视角验证框架**
   - **可解释性视角**：检验向量是否稳定、可分离、域一致。
     - 跨重复运行与采样温度高度可复现；
     - 相似角色间仍可分离；
     - 域内/域外问题上自洽性强于他角色信号。
   - **控制视角**：检验注入向量是否能让模型表达并维持该角色。
     - Qwen 上注入向量显著提升目标角色表达，最多与 role prompt 持平；
     - 单轮回答中向量叠加可强化角色，但未能延缓 30 轮对话中的角色衰退。

3. **双视角的必要性**
   - 域外角色仅被微妙表达，但激活仍编码该角色；
   - 稳定且可分离的向量，行为控制力可能仍有限。

### 方法架构

![[ext_inj_domain_cosine_injection_site_main_page1.png|600]]

> 图：角色向量的注入位置与域内余弦相似度（主图），展示向量在不同层的注入效果。

### 关键创新

1. **角色向量化** - 将 system prompt 的角色赋予机制映射到可操作的激活方向。
2. **双视角验证框架** - 可解释性与控制并重，揭示"编码"与"行为"之间的差距。
3. **揭示控制边界** - 稳定可分离的向量，其长程行为控制力仍有限，为激活引导提供现实校准。

## 实验结果

### 实验设置
- **模型**：Qwen2.5-7B-Instruct、Llama-3.1-8B-Instruct。
- **验证维度**：可复现性、可分离性、域一致性、注入后的角色表达、30 轮对话持久性。

### 主要结果
- 角色向量跨运行/温度高度可复现，相似角色仍可分离。
- Qwen 上注入向量使目标角色表达显著高于未引导基线，最多与 role prompt 持平。
- 向量叠加可强化单轮角色表达，但未延缓 30 轮对话中的角色衰退。

## 深度分析

### 研究价值评估
- **理论贡献**：将 system prompt 的角色机制落实到激活层，架起 prompt 工程与机制可解释性的桥梁。
- **实际应用**：为可控角色扮演、领域定制、安全性约束（角色护栏）提供激活引导工具。

### 方法优势
1. **可解释性与控制结合**，评估更全面。
2. **多模型验证**，结论具有一定普适性。

### 局限性
- 仅覆盖两个 7-8B 级别模型，规模有限。
- 角色向量无法持久控制 30 轮对话，长程有效性受限。

## 我的综合评价

### 总体评分
**7.8/10** - 问题有价值，双视角验证框架设计合理，但实验规模有限，且暴露了激活引导在长程控制上的边界。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 角色向量化视角新颖 |
| 技术质量 | 8/10 | 双视角框架严谨 |
| 实验充分性 | 7/10 | 模型/规模有限 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 7/10 | 工具价值中等，长程受限 |

> [!tip] 关键启示
> system prompt 的角色确实编码在激活空间中（稳定、可分离），但"编码"与"可控制"是两回事——稳定可分离的向量，其长程行为控制力仍有限。

## 相关论文
- 激活引导（activation steering）相关工作的延伸
