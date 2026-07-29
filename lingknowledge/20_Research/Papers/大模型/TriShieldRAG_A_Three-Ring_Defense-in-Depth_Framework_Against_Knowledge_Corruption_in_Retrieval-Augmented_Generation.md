---
date: "2026-07-28"
paper_id: "2607.23838"
title: "TriShieldRAG: A Three-Ring Defense-in-Depth Framework Against Knowledge Corruption in Retrieval-Augmented Generation"
authors: "Susil Kumar Mohanty, Rohit Patel, Kosuru Yuvaraj, Jeenal Chaudhary, Disha Singhania"
domain: "大模型"
tags: [论文笔记, RAG, Security, Defense-in-Depth, LLM-Safety, Knowledge-Corruption]
quality_score: "10.57/10"
related_papers: []
created: "2026-07-28"
updated: "2026-07-28"
status: analyzed
---

# TriShieldRAG: A Three-Ring Defense-in-Depth Against RAG Knowledge Corruption

## 核心信息
- **论文ID**：2607.23838
- **作者**：Susil Kumar Mohanty, Rohit Patel, Kosuru Yuvaraj, Jeenal Chaudhary, Disha Singhania
- **机构**：--
- **发布时间**：2026-07-26
- **会议/期刊**：arXiv 预印本 (cs.CR, cs.AI, cs.CL)
- **链接**：[arXiv](https://arxiv.org/abs/2607.23838) | [PDF](https://arxiv.org/pdf/2607.23838)

## 摘要翻译

RAG 让大语言模型使用查询时从外部知识库检索的文档回答问题。但如果知识库接受多方写入，攻击者只需少量对抗文档即可引导模型输出选定错误答案。PoisonedRAG 已证明仅需 5 篇精心制作的文档就能翻转无防护系统约 90% 的答案，三种自然单阶段防御仍留有 30% 以上攻击成功率。我们构建 TriShieldRAG 来弥补这一缺口——在管线中设置三个独立、形式化指定的防线：Ingest Guard（摄入守卫）筛查文档的词法和统计投毒特征；Retrieval Scorer（检索评分器）按来源和一致性加权信任分重排序检索集；Cross-LLM Consensus（跨 LLM 共识）轮询三个架构差异化的语言模型并在出现分歧时允许一次有界重检索。我们推导了 Ring 2 和 Ring 3 的生效条件：少数投毒假设和明确来源标签假设。在 5000 文档 Wikipedia 知识库和 10 个目标问题上，完整管线将攻击成功率从约 91% 降至约 13%，同时保持良性查询准确率。

## 研究问题
如何系统性地防御 RAG 知识库投毒攻击，同时不影响良性查询性能？

## 方法概述

### 核心思想
借鉴军事防御的"纵深防御"（Defense-in-Depth）理念，在 RAG 管线的三个关键节点设置独立防线，每道防线不可绕过且相互独立。

### 方法框架
**Ring 1 — Ingest Guard（摄入守卫）**
- 在文档写入知识库时筛查词法和统计投毒特征
- 基于已知投毒签名进行模式匹配

**Ring 2 — Retrieval Scorer（检索评分器）**
- 对检索结果按来源真实性（provenance）和一致性加权信任分重排序
- 形式化依赖：少数投毒假设（大多数文档是良性）

**Ring 3 — Cross-LLM Consensus（跨 LLM 共识）**
- 三个架构差异化 LLM（Claude, Mistral Small, Llama 3.2）独立生成答案后投票
- 出现分歧时允许一次有界重检索

### 关键创新
- 首次将纵深防御理念形式化应用于 RAG 安全
- 三层独立防线，每层不可绕过
- 形式化推导各防线生效的必要条件

## 实验结果
- **攻击成功率**：91% → 13%（5 篇投毒文档）
- **良性查询准确率**：保持不降
- **知识库**：5000 文档 Wikipedia

## 深度分析
- **优势**：纵深防御设计精巧，三层防线相互补充，每层解决不同类型威胁
- **局限性**：依赖少数投毒和来源标签假设（实际场景中不一定成立）；仅测试非自适应攻击者
- **适用场景**：多用户 RAG 系统、公共知识库问答、企业内部知识管理

> [!tip] 关键启示
> RAG 安全需要纵深防御，单点防御远远不够——从摄入到检索到生成，每个环节都需要独立验证。

> [!success] 推荐指数
> ⭐⭐⭐⭐ RAG 安全方向的重要实践指南，适合所有部署生产 RAG 系统的团队。
