---
date: "2026-07-02"
paper_id: "arXiv:2607.01223"
title: "Theoria: Rewrite-Acceptability Verification over Informal Reasoning States"
authors: "Ben Slivinski, Michael Saldivar"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 推理验证
  - 形式化验证
  - 可审计性
  - LLM安全
  - 对抗鲁棒性
quality_score: "8.6/10"
created: "2026-07-02"
updated: "2026-07-02"
status: analyzed
---

# Theoria: Rewrite-Acceptability Verification over Informal Reasoning States

## 核心信息
- **论文ID**：arXiv:2607.01223
- **作者**：Ben Slivinski, Michael Saldivar (Independent Researchers)
- **机构**：独立研究者
- **发布时间**：2026-07-01
- **类别**：cs.AI, cs.CL, cs.LG, cs.LO, cs.SE
- **链接**：[arXiv](http://arxiv.org/abs/2607.01223) | [PDF](https://arxiv.org/pdf/2607.01223) | [GitHub](https://github.com/zaladbar/theoria)
- **引用**：--

## 摘要翻译

### 英文摘要
When should an AI system's answer be trusted? Formal proof assistants offer certainty but cannot reach most of the problem distribution; scalar LLM judges offer coverage but produce opaque scores that cannot be audited after the fact. We present Theoria, a verification architecture that closes this gap. A candidate solution is rewritten into a sequence of typed state transitions, each licensed by an explicit justification, and every transition is independently auditable. The foundational invariant is completeness of change: every difference between consecutive proof states must be accounted for, so hidden premises surface as unlicensed mutations rather than passing silently.

### 中文翻译
AI 系统的答案何时值得信任？形式化证明助手提供确定性但无法覆盖大多数问题分布；标量 LLM 评判器提供覆盖率但产生无法事后审计的不透明分数。我们提出 Theoria，一种弥合这一鸿沟的验证架构。候选解答被重写为一系列类型化状态转换，每个转换由显式理据授权，每个转换可独立审计。基础不变式是变化完整性：连续证明状态之间的每个差异都必须有据可查，因此隐藏前提会以未授权变更的形式暴露而非悄然通过。

### 核心要点提炼
- **研究背景**：形式化证明（高确定性低覆盖）与 LLM 评判（高覆盖低可审计性）之间存在根本性鸿沟
- **研究动机**：需要一种既保留 LLM 的广泛覆盖能力、又具备可审计性的验证方案
- **核心方法**：将非形式化推理重写为类型化状态转换序列，每步附显式理据
- **主要结果**：HLE-Verified Gold 上 91.4% 严格精确率，GPQA Diamond 上 97.1%，对抗投毒检测率 94.7%
- **研究意义**：提供了在保持 LLM 覆盖面的同时实现可审计验证的实用方案

## 研究背景与动机

### 领域现状
当前验证 LLM 输出的两种主流范式各有致命缺陷：
- **形式化证明助手**（如 Lean, Coq）：提供数学确定性但需要将问题形式化，大多数现实问题无法做到
- **LLM-as-Judge**：用强模型打分评估，覆盖面广但分数不透明、不可审计，且受限于与生成模型相同的连贯性问题

### 研究动机
Theoria 试图在二者之间找到第三条路：**保留 LLM 的覆盖面，同时提供可审计的结构化证明轨迹**。

## 研究问题

### 核心研究问题
能否设计一种验证架构，使得：(1) 非形式化推理可以被转换为可审计的状态转换序列；(2) 任何未授权的推理跳跃都会以"未授权变更"的形式被检测到？

## 方法概述

### 核心思想
**变化完整性（Completeness of Change）** 是 Theoria 的基础不变式：连续证明状态之间的每个差异都必须由显式理据覆盖。如果 LLM 在推理中隐含地引入了一个前提（如"所有人都喜欢猫"），这个前提如果在文本中没有被显式陈述为理据，就会以"未授权变更"的形式暴露并导致验证失败。

### 方法框架

#### 整体架构

```
候选解答 → [重写器] → 类型化状态转换序列 → [验证器] → 通过/拒绝 + 可审计证明轨迹
              ↓                           ↓
         每步附理据                   逐步独立审计
    (引用/计算/问题给定事实)      (变化完整性检查)
```

#### 各模块说明

**模块1：重写器（Rewriter）**
- 将 LLM 的原始解答重写为结构化格式
- 每个状态转换包含：
  - 转换前状态
  - 转换后状态
  - 显式理据（citation / computation / problem-given fact）
  - 类型标注

**模块2：验证器（Verifier）**
- 逐个检查状态转换的合法性
- 核心检查：**变化完整性**——前后状态的差异是否被理据覆盖
- 隐藏前提：如果理据中未包含转换所需的某个前提 → 未授权变更 → 拒绝
- 可选的 favorable adjudication：考虑答案键歧义

**模块3：证明轨迹生成**
- 每次认证生成人类可读的证明轨迹
- 每个步骤可独立地被人类或自动化工具质询

## 实验结果

### 主要结果

| 基准 | 覆盖率 | 精确率 | 说明 |
|------|--------|--------|------|
| HLE-Verified Gold (185题) | 56.8% (105题) | 91.4% 严格 | Favorable: 100% |
| GPQA Diamond (65题) | -- | 97.1% | Wilson CI [85.1%, 99.5%] |
| 对抗投毒证明 (95题) | -- | 94.7% | vs 整体式 83.2%, p=0.0017 |

### 关键对比

**vs 求解器裸跑**：
- 求解器准确率 83.8%（web-augmented）
- Theoria 认证的子集中求解器正确率 92.4%
- 拒绝的子集中求解器正确率仅 72.5%（**19.9pp 选择效应**）

**vs 整体式 LLM 评判器**：
- 匹配覆盖率下精确率相当（Opus: 92.4%, Codex: 93.3%, Theoria: 91.4%）
- 但**错误集合几乎不相交**（Jaccard 0.14-0.36），呈互补关系
- 显式弃权测试中，整体式评判器只弃权 20%，Theoria 结构性拒绝 43.2%

**对抗投毒优势**：
- 整体 11.5pp 差距
- **隐藏前提**：90.6% vs 62.5%（28pp 差距）
- **伪造引用**：100% vs 90%
- **算术和定理误用**：性能相同（与形式分析预测一致）

### 错误重叠分析
- 三种方法共 15 个不同问题产生错误
- 仅 2 个问题被三种方法同时弄错
- **87% 的错误可通过组合方法恢复**

## 深度分析

### 研究价值评估

#### 理论贡献
1. **变化完整性不变量**：为结构化推理验证提供了简洁而强大的形式化准则
2. **结构化 vs 整体式的互补性证明**：两种方法在错误类别上高度不重叠，为组合验证提供了理论基础
3. **对抗鲁棒性的形式化预测**：准确预测了 Theoria 在隐藏前提和伪造引用上的优势，在算术错误上无优势

#### 实际应用价值
- **高风险决策**：科学声明、法律分析、金融计算等需要可审计推理的场景
- **AI 安全审计**：对部署模型进行结构化的推理质量审计
- **教育**：生成可逐步检查的证明轨迹

### 方法优势
1. **可审计性**：每步独立可查，这是 LLM 评判器无法提供的
2. **结构性拒绝**：拒绝信号基于证明能否构建，而非模型置信度阈值
3. **对抗鲁棒性优势**：在理论上可预测的错误类别上确实表现更好

### 局限性
1. **覆盖率限制**：仅认证 56.8% 的问题，43% 的问题被拒绝
2. **重写器瓶颈**：依赖 LLM 重写能力，对某些推理类型可能失败
3. **与实际使用场景的差距**：当前验证限于文本推理，扩展到多模态和代码推理需更多工作

### 四条论证线索
论文为 Theoria 的价值提供了四条超越原始精确率的论证：
1. 与整体式评判器的**互补错误覆盖**
2. 可审计的**证明轨迹**
3. 在形式分析预测的**错误类别上的对抗优势**
4. 基于证明构造可行性的**结构性拒绝信号**

## 我的综合评价

### 总体评分
**8.6/10** - 在 LLM 推理验证领域做出了重要贡献。"变化完整性"概念简洁有力，实验设计严谨且诚实（主动承认精确率不优于整体式评判），四条论证线索系统性强。

### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 变化完整性 + 结构化重写验证的新范式 |
| 技术质量 | 8/10 | 架构设计合理，但重写器实现细节不足 |
| 实验充分性 | 9/10 | 多基准、多基线、对抗测试、错误分析全面 |
| 写作质量 | 9/10 | 极其诚实透明，主动讨论局限性 |
| 实用性 | 8/10 | 覆盖率限制影响直接部署，但审计场景价值高 |

> [!tip] 关键启示
> 验证 AI 输出不仅需要判断"对或错"，更需要"为什么对、为什么错"的可审计轨迹——Theoria 在覆盖面与可审计性之间找到了一个实用的平衡点。

> [!warning] 注意事项
> - Theoria 并非替代整体式评判器，而是互补——组合使用效果最佳
> - 覆盖率 56.8% 意味着近半数问题被拒绝，需要配套的降级策略
> - 当前版本仅限文本推理，实际部署需扩展

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！LLM 推理验证方向的必读论文，方法简洁、实验透明、洞察深刻。

## 相关论文
- [[EvalSafetyGap]] - LLM 评估安全鸿沟：同样关注评估/验证的可靠性问题
- [[AdversaBench]] - LLM 红队测试：共享对抗鲁棒性关注
- 形式化验证相关：Lean、Coq 等证明助手
