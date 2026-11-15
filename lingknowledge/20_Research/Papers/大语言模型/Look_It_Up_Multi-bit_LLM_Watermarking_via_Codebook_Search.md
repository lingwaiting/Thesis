---
date: "2026-11-15"
paper_id: "arXiv:2611.16890"
title: "Look It Up: Multi-bit LLM Watermarking via Codebook Search"
authors: "Jarosław Janas, Paweł Morawiecki, Josef Pieprzyk"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 文本水印
  - 多比特水印
  - 码本搜索
  - 短文本归属
quality_score: "7.5/10"
related_papers: []
created: "2026-11-15"
updated: "2026-11-15"
status: analyzed
---

# Look It Up: Multi-bit LLM Watermarking via Codebook Search

## 核心信息
- **论文ID**：arXiv:2611.16890
- **作者**：Jarosław Janas, Paweł Morawiecki, Josef Pieprzyk
- **机构**：--
- **发布时间**：2026-11-12
- **会议/期刊**：arXiv 预印本（cs.CR / cs.AI / cs.CL）
- **链接**：[arXiv](https://arxiv.org/abs/2611.16890) | [PDF](https://arxiv.org/pdf/2611.16890)
- **引用**：--

## 摘要翻译

### 英文摘要
Attributing short LLM-generated texts to a specific source remains challenging for existing multi-bit watermarking methods, which often require long generations or costly decoding to recover payloads reliably. We propose a codebook-based watermarking scheme that maps an n-bit payload to a longer u-bit codeword and embeds it during generation through token-level logit biasing. During extraction, the received bit sequence is matched to the nearest codeword in a precomputed secret codebook using Hamming or Levenshtein distance. Experiments show that our method achieves near-perfect recovery for 16-24 bit payloads on 200-token generations and strong performance on 127-token generations for base models. Results are weaker on some instruction-tuned models with very short texts, where low-entropy output distributions reduce embedding reliability. We also study a BCH-based variant that avoids codebook storage but generally reduces robustness. The Levenshtein variant improves resilience to deletions and light paraphrasing, while insertions and stronger rewrites remain challenging. We analyze collision probability, codeword separation, extraction time, tokenization consistency, and text quality, highlighting both the practicality and limitations of codebook-based watermarking for short-form attribution.

### 中文翻译
将短 LLM 生成文本归属到特定来源，对现有多比特水印方法仍是挑战——它们往往需要长文本或高成本解码才能可靠恢复载荷。我们提出一种基于码本（codebook）的水印方案，把 n 比特载荷映射为更长的 u 比特码字，并在生成期间通过 token 级 logit 偏置嵌入。提取时，接收到的比特序列用汉明距离或莱文斯坦距离匹配到预计算秘密码本中最近的码字。实验表明，该方法在 200-token 生成上对 16–24 比特载荷取得近乎完美恢复，在基础模型的 127-token 生成上表现也很强；在部分指令微调模型的极短文本上表现较弱（低熵输出分布降低嵌入可靠性）。我们还研究了一种避免码本存储的 BCH 变体，但通常降低鲁棒性。莱文斯坦变体提升了对删除与轻度改写的韧性，而插入与更强的改写仍具挑战。我们分析了碰撞概率、码字间距、提取时间、分词一致性与文本质量，凸显了基于码本水印在短文本归属上的实用性与局限。

### 核心要点提炼
- **研究背景**：多比特水印的短文本归属仍是难题，现有方法需长文本或高成本解码。
- **研究动机**：在短生成文本上可靠地恢复多比特载荷（归属来源）。
- **核心方法**：n 比特载荷 → 更长的 u 比特码字，生成期 token 级 logit 偏置嵌入，提取期用汉明/莱文斯坦距离最近码字匹配。
- **主要结果**：200-token 生成上 16–24 比特载荷近乎完美恢复；莱文斯坦变体对删除/轻度改写更鲁棒。

## 研究问题

核心研究问题：**如何设计一种多比特 LLM 水印方案，在短生成文本上以低成本可靠地恢复载荷，从而实现对 AI 生成文本的来源归属？**

## 方法概述

### 核心方法

1. **码本映射（codebook mapping）**
   - 将 n 比特载荷映射到更长的 u 比特码字（u > n），用冗余换取纠错能力。

2. **生成期嵌入（token-level logit biasing）**
   - 在生成过程中通过 token 级 logit 偏置把码字嵌入文本。

3. **提取期最近码字匹配**
   - 接收到的比特序列用汉明距离或莱文斯坦距离匹配到预计算秘密码本中的最近码字，恢复原始载荷。

4. **BCH 变体**
   - 用 BCH 码避免码本存储，但通常降低鲁棒性。

### 方法架构

![[watermark_codebook_scheme.png|600]]

> 图：基于码本的多比特水印方案示意，展示载荷→码字映射、生成期嵌入与提取期最近码字匹配。

### 关键创新

1. **码本 + 最近邻匹配** - 用冗余码字 + 距离匹配解决短文本下载荷恢复的可靠性问题。
2. **莱文斯坦距离变体** - 提升对删除与轻度改写的韧性，优于纯汉明距离。
3. **系统化分析** - 碰撞概率、码字间距、提取时间、分词一致性、文本质量等多维评估，界定方法边界。

## 实验结果

### 实验设置
- **模型**：多种基础模型与指令微调模型。
- **文本长度**：200-token 与 127-token 生成。
- **载荷规模**：16–24 比特。
- **对比**：汉明距离 vs 莱文斯坦距离、码本 vs BCH 变体。

### 主要结果
- **恢复率**：200-token 生成上 16–24 比特载荷近乎完美恢复；基础模型 127-token 表现强。
- **指令微调模型**：极短文本 + 低熵输出下可靠性下降。
- **鲁棒性**：莱文斯坦变体对删除与轻度改写更鲁棒，插入与强改写仍具挑战。

## 深度分析

### 研究价值评估
- **理论贡献**：为短文本多比特水印提供了码本 + 距离匹配的工程范式，系统界定了可行边界。
- **实际应用**：适用于需要来源归属的短文本场景（如社交媒体、问答回复），归属成本低。

### 方法优势
1. **短文本可靠**：解决现有多比特水印"需长文本"的痛点。
2. **成本低**：提取只需距离匹配，无需高成本解码。
3. **分析全面**：对碰撞、码字间距、鲁棒性等多维评估，结论可信。

### 局限性
- 对插入与强改写鲁棒性不足。
- 极短文本 + 低熵（如部分指令微调模型）可靠性下降。
- 秘密码本的存储/分发在真实部署中的安全性未充分展开。

## 与相关论文对比
- **现有多比特水印**：通常需长文本或高成本解码，本文用码本 + 距离匹配在短文本上实现可靠恢复。
- **零比特 / 检测式水印**：只判断"是否 AI 生成"，本文聚焦"归属到具体来源"的多比特载荷。

## 我的综合评价

### 总体评分
**7.5/10** - 一项务实、分析扎实的多比特水印工作，用码本 + 最近邻匹配把短文本归属做可靠，鲁棒性边界清晰，但对强改写的韧性与秘密码本安全仍有留白。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 码本 + 距离匹配的组合有新意，非颠覆性突破 |
| 技术质量 | 8/10 | 设计简洁，多维分析严谨 |
| 实验充分性 | 8/10 | 多模型、多长度、多距离、BCH 变体覆盖全 |
| 写作质量 | 8/10 | 结构清晰，边界诚实 |
| 实用性 | 7/10 | 短文本归属实用，但强改写/低熵场景受限 |

> [!tip] 关键启示
> 短文本多比特水印的可靠恢复，靠的是"用冗余码字 + 最近码字匹配"来纠错，而非一味增加文本长度——莱文斯坦距离变体还能额外扛住删除与轻度改写。

## 相关论文
- [[SPADE_Soft_Prompts_for_Ai-text_DEtection|SPADE]] - 同样面向 AI 文本识别，但走"事后检测"而非"生成期水印"路线，二者可互补
