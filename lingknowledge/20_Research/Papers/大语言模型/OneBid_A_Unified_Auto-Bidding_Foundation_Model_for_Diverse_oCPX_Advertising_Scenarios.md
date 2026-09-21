---
date: "2026-09-21"
paper_id: "arXiv:2609.21550"
title: "OneBid: A Unified Auto-Bidding Foundation Model for Diverse oCPX Advertising Scenarios"
authors: "Yewen Li, Peng Jiang, Yitian Li, Pengfei Lv, Xialong Liu, Peng Jiang, Qingpeng Cai"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 强化学习
  - 智能竞价
  - Foundation-Model
  - Mixture-of-Experts
quality_score: "8.4/10"
created: "2026-09-21"
updated: "2026-09-21"
status: analyzed
---

# OneBid: A Unified Auto-Bidding Foundation Model for Diverse oCPX Advertising Scenarios

## 核心信息
- **论文ID**：arXiv:2609.21550
- **作者**：Yewen Li, Peng Jiang, Yitian Li, Pengfei Lv, Xialong Liu, Peng Jiang, Qingpeng Cai（通讯）
- **机构**：快手（Kuaishou Technology，北京）
- **发布时间**：2026-09-18
- **会议/期刊**：arXiv 预印本（ACM 版权）
- **链接**：[arXiv](https://arxiv.org/abs/2609.21550) | [PDF](https://arxiv.org/pdf/2609.21550)

## 摘要翻译

### 英文摘要
Auto-bidding is central to computational advertising, where strategies must maximize advertisers' conversion value under economic constraints. It has evolved from rule-based controllers to RL and generative methods like Decision Transformer (DT). Yet these methods increasingly mismatch the prevailing optimized cost-per-X (oCPX) paradigm, which spans heterogeneous scenarios each served by a separate model. Inspired by foundation models, OneBid unifies oCPX scenarios into one model: it learns a reusable backbone from heterogeneous oCPX logs and adapts via offline post-training, extending DT's Return-to-Go to Return-to-Go + Cost-to-Go, using a sequence-level Mixture-of-Experts architecture, and aligning via Critic-guided Relative Offline Policy optimization (CROP). It delivers +2.2% ADVV overall, peaking at +13.1% in the ROAS scenario, fully deployed at Kuaishou.

### 中文翻译
智能竞价是计算广告的核心，策略需在经济约束下最大化广告主转化价值。它已从规则控制器演进到强化学习与生成式方法（如 Decision Transformer）。但这些方法与主流的优化成本（oCPX）范式日益脱节——oCPX 涵盖注册、购买等异构场景，每个场景由独立模型服务，导致流程碎片化、跨场景建模潜力未被挖掘。受基础模型启发，OneBid 将 oCPX 场景统一到一个模型中：从异构 oCPX 日志学习可复用骨干，通过离线后训练适配具体场景；将 DT 的单一 Return-to-Go 扩展为 Return-to-Go + Cost-to-Go 两个原子信号，采用序列级混合专家架构，并通过 CROP 对齐。线上 A/B 测试显示整体 ADVV +2.2%，ROAS 场景峰值 +13.1%，已全面部署于快手。

### 核心要点提炼
- **研究背景**：oCPX 广告场景碎片化，每个场景独立建模，跨场景知识未复用。
- **研究动机**：借鉴基础模型"一模型多任务"思想，统一异构 oCPX 场景。
- **核心方法**：DT 基础上扩展双目标条件（RTG + CTG）、序列级 MoE、CROP 离线策略优化。
- **主要结果**：线上 ADVV 整体 +2.2%，ROAS 峰值 +13.1%，已部署快手。

## 研究背景与动机

### 领域现状
智能竞价（Auto-bidding）策略从规则控制器 → 强化学习 → 生成式方法（DT）演进。主流 oCPX 范式按转化目标（注册、购买、ROAS 等）划分场景，每个场景单独训练一个模型。

### 现有方法的局限性
- **目标单一**：DT 的单一 Return-to-Go 条件无法同时表达"转化价值"与"成本控制"两个目标。
- **流程碎片化**：场景间各自为政，无法共享跨场景的公共知识。
- **在线探索风险**：GRPO 式微调需要在线探索，存在安全与 OOD 风险。

### 研究动机
统一异构 oCPX 场景需要解决三个挑战：多目标控制、严格延迟约束下的可扩展容量、安全的离线策略改进。

## 研究问题

### 核心研究问题
如何构建一个可复用的竞价基础模型，统一异构 oCPX 场景，同时满足多目标控制、低延迟扩展与安全的离线策略优化？

## 方法概述

### 核心思想
以 Decision Transformer 为骨架，通过"双目标条件 + 序列级 MoE + 离线策略对齐"三步，构建跨场景通用的竞价基础模型。

### 方法框架

#### 整体架构

![[2609.21550_fig1.png|800]]

> 图1：OneBid 概览。(I) 预训练：将异构 oCPX 日志组织成统一竞价轨迹；(II) 序列级混合专家（S-MoE）Transformer 骨干；(III) 后训练：Critic 学习后，用 CROP 优化 actor。

**模块1：双目标条件（RTG + CTG）**
- **功能**：将单一 Return-to-Go 扩展为 Return-to-Go（转化价值）与 Cost-to-Go（成本比率）两个原子信号。
- **关键技术**：在 next-action 预测上加入 value-aware 正则化。

**模块2：序列级混合专家（S-MoE）**
- **功能**：共享专家编码跨场景公共知识，稀疏路由专家捕获场景特定模式。
- **关键技术**：稀疏激活保证低延迟，随模型规模与数据量一致缩放。

**模块3：CROP（Critic-guided Relative Offline Policy optimization）**
- **功能**：用学到的 Critic 对候选动作做组内相对打分，离线改进策略。
- **关键技术**：规避 GRPO 式在线探索的安全风险，约束策略偏移降低 OOD。

## 实验结果

### 实验设置
- **部署**：线上 A/B 测试，已全面部署于快手 oCPX 广告系统。

### 主要结果
- **整体**：oCPX Ads 上 ADVV（广告主转化价值）提升 **+2.2%**。
- **峰值**：ROAS 场景提升 **+13.1%**。
- **缩放**：预训练阶段随模型规模与数据量表现出一致的缩放特性。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：将"基础模型"范式从 NLP/视觉引入计算广告，提出 oCPX 场景统一建模的方案。
  - 创新点：双目标条件 + 序列级 MoE + 离线策略对齐的组合。

#### 实际应用价值
- **应用场景1**：工业级智能竞价系统。
  - 优势：真实线上部署验证，商业价值直接、可量化。
- **应用场景2**：其他带约束的序列决策（如推荐、定价）。
  - 优势：离线策略改进思路可迁移。

### 方法优势详解
- **优势1（统一性）**：一个骨干服务多场景，减少碎片化运维成本。
- **优势2（安全性）**：CROP 完全离线，避免在线探索风险，契合广告系统对稳定性的要求。

### 局限性分析
- **局限1（泛化性）**：结论主要来自快手特定业务数据，跨平台/跨行业迁移性待验证。
- **局限2（工程细节）**：S-MoE 与 CROP 的超参敏感，论文对部分实现细节着墨有限。

## 我的综合评价

### 总体评分
**8.4/10** — 工业落地完整（真实 A/B + 全量部署）、方法组合合理，是"基础模型 + 广告"方向极具参考价值的工程型工作。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 组合式创新，非全新范式 |
| 技术质量 | 9/10 | 三挑战均有针对性设计 |
| 实验充分性 | 8/10 | 线上 A/B 强，但公开基准少 |
| 写作质量 | 8/10 | 结构清晰 |
| 实用性 | 9/10 | 已全量部署，商业价值明确 |

> [!tip] 关键启示
> 基础模型的"跨任务统一"思想在广告竞价同样成立——关键是把多目标、低延迟、离线安全三个约束同时纳入架构设计。

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐：工业落地的标杆案例，对广告/推荐/定价等序列决策方向有直接借鉴意义。
