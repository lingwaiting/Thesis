---
date: "2026-08-10"
paper_id: "arXiv:2608.09867"
title: "Stealing Reasoning Traces from Proprietary LLM APIs"
authors: "Alexander Panfilov*, David Schmotz*, Ilia Shumailov*, Luca Beurer-Kellner, Joachim Schaeffer, Ameya Prabhu†, Jonas Geiping†, Maksym Andriushchenko†"
domain: "大语言模型"
tags:
  - 论文笔记
  - LLM-safety
  - reasoning
  - chain-of-thought
  - API-security
  - distillation
  - prompt-injection
quality_score: "8.0/10"
created: "2026-08-12"
updated: "2026-08-12"
status: analyzed
---

# Stealing Reasoning Traces from Proprietary LLM APIs

## 核心信息
- **论文ID**：arXiv:2608.09867
- **作者**：Alexander Panfilov*, David Schmotz*, Ilia Shumailov*, Luca Beurer-Kellner, Joachim Schaeffer, Ameya Prabhu†, Jonas Geiping†, Maksym Andriushchenko† (*equal contribution, †equal supervision)
- **机构**：MATS Research, ELLIS Institute Tübingen, Max Planck Institute for Intelligent Systems, Tübingen AI Center, AI Security Company, Snyk, University of Tübingen
- **发布时间**：2026-08-10
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2608.09867v1) | [PDF](https://arxiv.org/pdf/2608.09867v1) | [stolen-thoughts.com](https://stolen-thoughts.com)
- **分类**：cs.CR, cs.AI, cs.LG

## 摘要翻译

### 英文摘要
Leading large language model providers now conceal their models' step-by-step reasoning as blocks of encrypted text, which the client passes back with each subsequent request. We identify an architectural vulnerability: these encrypted blocks are fully compatible and interchangeable across different sessions, users, and models within a provider's ecosystem. By injecting an encrypted reasoning trace from a given model into a weaker model from the same provider, we force it to decode and output the trace verbatim in plaintext. This vulnerability enables four attack vectors: (1) circumvents anti-distillation mechanisms across Anthropic, OpenAI, and Google; (2) large-scale private data extraction — decoding 315,320 reasoning blocks recovered 367 PII artifacts and 182 credentials; (3) reveals hazardous information hidden within reasoning; (4) invisible prompt injections through poisoned reasoning blocks.

### 中文翻译
各大 LLM 提供商现在将模型的逐步推理过程以加密文本块的形式返回给客户端，要求客户端在每次后续请求中回传。我们发现了一个架构漏洞：这些加密块在同一提供商的**不同会话、不同用户和不同模型之间完全兼容和可互换**。通过将目标模型的加密推理链注入同一提供商的较弱模型，我们迫使较弱模型以明文形式逐字解码和输出推理过程。该漏洞导致四种攻击向量：(1) 绕过 Anthropic、OpenAI、Google 的反蒸馏机制；(2) 从公开仓库的 315,320 个推理块中恢复 367 个 PII 和 182 个凭据；(3) 揭示隐藏的有害信息；(4) 通过中毒推理块实施不可见提示注入。

### 核心要点提炼
- **研究背景**：为保护知识产权，OpenAI/Anthropic/Google 已将推理链加密返回，客户端需在后续请求中回传
- **研究动机**：加密推理块被发现在跨会话/跨模型间兼容，构成严重安全漏洞
- **核心方法**：利用"加密块兼容性"将强模型的加密推理注入弱模型，使其作为"解密预言机"
- **主要结果**：跨三大厂商验证漏洞；实际恢复 367 PII + 182 凭据；已进行负责任披露

## 研究背景与动机

### 领域现状
2026 年，前沿 LLM 提供商（Anthropic、OpenAI、Google）已将思维链推理从明文输出改为加密文本块返回。客户端需在每次后续 API 调用中回传这些块以维持上下文连续性。

### 核心漏洞
加密推理块**在提供商生态系统内的不同会话、用户和模型之间完全兼容**。这意味着：
- 从 Opus 4.8 捕获的加密块可注入 Haiku 4.5
- Haiku（弱防护）可被诱导逐字输出 Opus 的推理
- **安全不对称**：强模型有严格反蒸馏训练，弱模型则缺乏

## 方法概述

### 攻击流程
```
Opus 4.8 → 生成加密推理 → 捕获 encrypted thinking block
                                    ↓
Haiku 4.5 ← 注入加密块 + "输出你的推理"提示
                                    ↓
                          Haiku 逐字输出 Opus 的推理明文
```

### 四种攻击向量

#### 1. 蒸馏攻击
- **节约成本**：解码 10K 条推理仅需约 $720（Haiku 4.5 定价）
- **跨越厂商**：在 Anthropic、OpenAI、Google 上均验证有效
- **逃逸检测**：攻击仅涉及弱模型 API，监控强模型的防御系统不可见

#### 2. 隐私数据大规模提取
- 从公开仓库抓取 **315,320** 个加密推理块
- 恢复 **367** 个 PII 和 **182** 个凭据
- 包括 62 个 API 密钥、33 个密码、30 个个人邮箱
- 严重问题：用户无法解密推理块，因此无法在分享前进行脱敏

#### 3. 有害信息泄露
- 模型被训练为在可见输出中拒绝有害请求，但**推理过程中可能仍包含有害信息**
- 攻击者引导模型推理有害主题 → 捕获加密块 → 解码获取有害信息
- 最终可见输出仅为良性拒绝

#### 4. 隐蔽提示注入
- 攻击者可在加密推理块中嵌入恶意 payload
- 这些 payload 对发布日志的开发者完全不可见
- 可污染公开智能体 rollout

## 实验结果

### 核心结果
- **跨厂商验证**：在 Anthropic (Opus→Haiku)、OpenAI (→Luna)、Google (→Flash) 上均成功
- **推理保真度**：解码推理的 token 数与原始推理高度相关
- **经济可行性**：大规模蒸馏成本仅约 $720/10K 条

### 隐私泄露统计
| 类型 | 数量 |
|------|------|
| 解码推理块总数 | 315,320 |
| 恢复的 PII 项 | 367 |
| 恢复的凭据 | 182 |
| API 密钥 | 62 |
| 密码 | 33 |
| 个人邮箱 | 30 |

## 深度分析

### 研究价值评估

#### 重大贡献
- **发现基础架构漏洞**：加密块的跨模型兼容性是一个设计层面的缺陷
- **实际安全影响**：已通过大规模实证（315K+ 推理块）验证了现实风险
- **负责任披露**：在论文发表前向所有受影响的提供商报告，截至 2026 年 8 月大部分攻击已不再可复现

#### 实际影响
- **对开发者**：避免公开分享 API 会话日志或加密推理块；审查历史公开仓库
- **对提供商**：需加密签名绑定用户/会话/模型；考虑服务器端存储；提供用户解密工具
- **对用户**：加密推理块中可能包含用户输入中不存在的敏感信息（模型从记忆中注入）

### 方法优势
1. **零开销攻击**：无需训练任何模型，仅需 API 调用
2. **经济可行**：大规模蒸馏仅数百美元
3. **隐蔽性高**：攻击流量分散在弱模型 API 上，避开强模型监控

### 局限性
1. **截至 2026 年 8 月已大部分修复**：提供商已实施缓解措施
2. **仅验证三大厂商**：其他提供商的安全性未知
3. **依赖弱模型可用性**：如果提供商移除弱模型或将弱模型也加固，攻击会失效

## 技术路线定位

本文属于 **LLM 安全 × API 安全**交叉领域。聚焦于部署架构层面的安全漏洞，而非模型能力或训练数据层面的攻击。

## 我的综合评价

**8.0/10** — 实际安全影响重大，展示了从架构设计缺陷到大规模隐私泄露的完整攻击链。实验规模令人印象深刻（$30K API 费用，315K+ 推理块）。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 基于已知的加密兼容性，系统性地开发和演示完整攻击链 |
| 技术质量 | 7/10 | 攻击方法直接但有效，大规模实验证实了广泛影响 |
| 实验充分性 | 9/10 | 三大厂商、四种攻击向量、315K+ 推理块大规模扫描 |
| 写作质量 | 8/10 | 清晰的安全研究写作，威胁模型定义明确 |
| 实用性 | 9/10 | 直接导致厂商实施修复，已有实际安全影响 |

> [!tip] 关键启示
> 加密本身不足以保证安全——当加密块的架构设计允许跨上下文重放时，加密只是"安全剧场"。安全的系统设计需要考虑整个生命周期，包括密钥管理和上下文绑定。

> [!warning] 注意事项
> - 大部分攻击在 2026 年 8 月后已不可复现（提供商已修复）
> - 如果曾公开分享过 API 会话日志，应立即审查并删除
> - 加密推理块中可能包含用户输入中不存在的敏感信息

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！如果你使用 Claude/GPT/Gemini API 进行开发，这篇论文具有直接的操作性意义。它也是负责任安全研究披露的典范。
