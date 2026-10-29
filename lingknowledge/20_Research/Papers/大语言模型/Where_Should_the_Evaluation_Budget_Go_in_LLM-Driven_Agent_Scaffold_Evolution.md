---
date: "2026-10-29"
paper_id: "arXiv:2610.37276"
title: "Where Should the Evaluation Budget Go in LLM-Driven Agent Scaffold Evolution?"
authors: "Sichun Luo, Yi Huang, Mukai Li, Haochen Luo, Guanzhi Deng, Haibo Wang, Jian Xu, Zefa Hu, Junlan Feng, Qi Liu"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - LLM
  - 智能体
  - 工作流进化
  - 自动评估
  - 预算分配
  - 脚手架进化
quality_score: "7.0/10"
created: "2026-10-29"
updated: "2026-10-29"
status: analyzed
---

# Where Should the Evaluation Budget Go in LLM-Driven Agent Scaffold Evolution?

## 核心信息
- **论文ID**：arXiv:2610.37276
- **作者**：Sichun Luo, Yi Huang, Mukai Li, Haochen Luo, Guanzhi Deng, Haibo Wang, Jian Xu, Zefa Hu, Junlan Feng, Qi Liu
- **机构**：--
- **发布时间**：2026-10-27
- **类别**：cs.LG, cs.AI, cs.CL, cs.NE
- **链接**：[arXiv](https://arxiv.org/abs/2610.37276) | [PDF](https://arxiv.org/pdf/2610.37276)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Large language model (LLM)-driven agent scaffold evolution improves agent workflows through iterative candidate generation and evaluation. Evaluation both guides subsequent search and determines the final scaffold, but evaluating every candidate on the full task set incurs substantial execution costs. We investigate how evaluation budgets can be allocated across these two roles. Our experiments show that sparse feedback can sustain productive evolution, while additional targeted evaluation improves final selection. Replay analyses further show that shared informative items and a common scoring reference support comparisons among closely matched candidates. Motivated by these findings, we propose an evaluation allocation framework that combines sparse search-time feedback with targeted final confirmation. A short fully evaluated warm-up establishes a shared informative core and calibrates a response model whose item parameters remain fixed during search. After search, the framework refits the model and applies paired successive halving, reusing accumulated observations and executing only missing evaluations. Across four benchmarks and two evolution frameworks, the framework achieves higher mean performance than full evaluation in most settings while using approximately half as many evaluation cells.

### 中文翻译
由大语言模型（LLM）驱动的智能体脚手架进化，通过迭代式候选生成与评估来改进智能体工作流。评估既引导后续搜索，又决定最终的脚手架，但对每个候选都在完整任务集上评估会带来巨大的执行成本。本文研究评估预算应如何在上述两个角色之间分配。实验表明，稀疏反馈即可维持有效的进化，而额外的定向评估能改善最终选择。回放分析进一步表明，共享的高信息量条目和统一的评分基准有助于在相近候选之间做比较。基于这些发现，本文提出一个评估分配框架，将稀疏的搜索期反馈与定向的最终确认相结合：一段完全评估的短热身建立共享的信息核心，并校准一个响应模型（其条目参数在搜索期间保持固定）；搜索结束后，框架重新拟合模型并应用成对逐次减半（paired successive halving），复用已累积的观测、只执行缺失的评估。在四个基准和两个进化框架上，该框架在大多数设置下取得比全量评估更高的平均性能，同时仅使用约一半的评估单元。

### 核心要点提炼
- **研究问题**：智能体脚手架进化中，评估预算（算力/成本）应如何在"引导搜索"与"最终选择"之间分配
- **关键发现**：稀疏反馈足以维持进化；定向评估改善最终选择；共享高信息条目 + 统一评分基准有助于相近候选的比较
- **核心方法**：短热身（建立共享信息核心 + 校准响应模型）+ 稀疏搜索期反馈 + 搜索后重拟合 + 成对逐次减半（复用观测、只补缺失评估）
- **主要结果**：四个基准、两个进化框架上，多数设置性能高于全量评估，评估单元减少约一半

## 研究背景与动机

### 领域现状
LLM 驱动的智能体脚手架进化（agent scaffold evolution）通过"生成候选 → 评估 → 选择 → 再生成"的迭代来优化工作流（如工具的编排、提示词结构）。评估在这一过程中承担双重角色：既反馈给搜索过程引导方向，又用于挑选最终提交的脚手架。

### 现有方法的局限性
1. **全量评估成本高昂**：对每个候选都在完整任务集上评估，执行成本（评估单元）随候选数量线性增长
2. **预算分配缺乏理论指导**：现有工作默认"均匀全量评估"，没有回答"多少预算用于搜索期反馈、多少用于最终确认"这一核心问题
3. **角色混叠**：评估同时服务两个目标，但两者对预算的敏感性可能不同

### 研究动机
把评估预算视为可分配的稀缺资源，系统研究其在"搜索引导"与"最终选择"之间的最优配置，从而在不牺牲（甚至提升）最终性能的前提下大幅降低评估成本。

## 研究问题

### 核心研究问题
1. 评估预算在"引导搜索"与"决定最终脚手架"两个角色间应如何分配？
2. 稀疏反馈能否维持有效的进化？定向评估能带来多大最终选择收益？
3. 如何设计一套既省预算又可靠的评估分配框架？

## 方法概述

### 核心思想
用"稀疏搜索期反馈 + 定向最终确认"替代"均匀全量评估"：搜索期只需稀疏反馈即可驱动进化，把省下的预算集中用于最终候选的精细比较。

### 方法框架

#### 整体架构
```
短热身(全量评估) → 校准响应模型(条目参数固定) → 搜索期稀疏反馈
        ↓
搜索结束 → 重拟合模型 → 成对逐次减半(复用观测/补缺失评估) → 最终脚手架
```

#### 各模块详细说明

**模块1：短热身（warm-up）**
- 对少量候选做完全评估，建立共享的信息核心（informative core）
- 校准一个响应模型（如 IRT 类条目参数模型），其条目参数在搜索期间保持固定

**模块2：搜索期稀疏反馈**
- 用稀疏反馈维持进化过程，避免对每个候选全量评估

**模块3：搜索后定向确认**
- 重新拟合响应模型，应用成对逐次减半（paired successive halving）
- 复用已累积的观测，只执行缺失的评估单元

## 实验结果

### 主要结果
- 稀疏反馈能维持有效的进化过程
- 额外的定向评估显著改善最终候选选择
- 在四个基准、两个进化框架上，多数设置下框架性能高于全量评估，评估单元减少约一半

### 结果分析
- **核心洞察**：搜索期与最终确认对预算的需求不对称——搜索期可稀疏，最终确认需定向精细
- **实用价值**：以约一半的评估成本取得更好的最终性能

## 深度分析

### 研究价值评估

#### 理论贡献
- 首次系统拆解"评估的双重角色"并给出预算分配策略
- 复用响应模型 + 成对逐次减半，把"预算分配"变成可操作的算法
- 学术价值：中高。问题定义清晰，方法务实

#### 实际应用价值
- 直接降低智能体脚手架自动进化的成本，使大规模工作流优化更可行
- 对任意"候选生成 + 评估选择"的迭代优化场景（NAS、提示优化）都有迁移价值

### 方法优势详解
1. **成本-性能双赢**：评估单元减半，多数设置性能反而更高
2. **复用已有观测**：不浪费搜索期已产生的评估信息

### 局限性分析
1. **依赖响应模型假设**：条目参数固定、可复用等假设是否跨任务成立需验证
2. **框架名称未显式给出**（摘要中以 \method 占位），可复现性细节需看正文
3. **基准范围**：四个基准是否覆盖足够多样化的 agent 工作流有待确认

### 适用性与场景分析
- 适合：评估成本高、候选多的智能体工作流自动进化
- 可迁移：提示优化、神经架构搜索等"迭代生成-评估"场景

## 我的综合评价

### 价值评分

#### 总体评分
**7.0/10** - 问题切中实际痛点（评估成本），"稀疏搜索反馈 + 定向最终确认"的分配思路清晰且有实验支撑；但创新偏工程化，且依赖响应模型等假设。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 6/10 | 预算分配视角有新意，方法本身是现有技术的组合 |
| 技术质量 | 7/10 | 框架设计合理，成对逐次减半复用观测巧妙 |
| 实验充分性 | 7/10 | 四基准 + 两进化框架，覆盖较全 |
| 写作质量 | 7/10 | 结构清晰，动机充分 |
| 实用性 | 8/10 | 直接降低 agent 进化成本，实用价值高 |

### 重点关注
- 响应模型的条目参数如何在校准后保持固定并复用
- 成对逐次减半相比普通逐次减半的改进点

## 相关论文
- 暂无直接相关论文

## 外部资源
- arXiv: https://arxiv.org/abs/2610.37276

> [!tip] 关键启示
> 评估既是"搜索的导航"又是"选择的标准"，两者对预算的敏感度不同：搜索期可以稀疏，最终选择需要定向精细。把预算花在刀刃上，比均匀全量评估更省且更好。

> [!warning] 注意事项
> - 方法依赖响应模型（条目参数固定/可复用）假设
> - 摘要未给出框架正式名称，需核对正文实现细节
> - 结论的跨任务泛化性待进一步验证

> [!success] 推荐指数
> ⭐⭐⭐⭐ 适合关注 LLM agent 自动进化、工作流优化与评估成本控制的读者阅读。
