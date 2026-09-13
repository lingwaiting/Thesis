---
date: "2026-09-13"
paper_id: "arXiv:2609.11209"
title: "REVA: Reusable Evidence View Aggregation for Context-Efficient RAG Serving"
authors: "Tuan Nguyen, Qiran Hu, Banruo Liu, Khoa D. Doan, Kok-Seng Wong, Fan Lai"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - RAG
  - 上下文压缩
  - 注意力挖掘
  - 推理效率
  - 高效服务
quality_score: "7.5/10"
created: "2026-09-13"
updated: "2026-09-13"
status: analyzed
---

# REVA: Reusable Evidence View Aggregation for Context-Efficient RAG Serving

## 核心信息
- **论文ID**：arXiv:2609.11209
- **作者**：Tuan Nguyen, Qiran Hu, Banruo Liu, Khoa D. Doan, Kok-Seng Wong, Fan Lai
- **机构**：VinUni-Illinois Smart Health Center（越南 VinUniversity）、伊利诺伊大学厄巴纳-香槟分校（UIUC）
- **发布时间**：2026-09-10
- **会议/期刊**：IEEE ICDM 2026（已接收）
- **类别**：cs.LG, cs.CL, cs.IR
- **链接**：[arXiv](https://arxiv.org/abs/2609.11209) | [PDF](https://arxiv.org/pdf/2609.11209) | [代码](https://github.com/UIUC-MLSys/REVA)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Retrieval-augmented generation (RAG) improves knowledge-intensive large language model (LLM) applications by conditioning generation on retrieved documents, but longer contexts increase latency, key-value (KV) cache memory, and token cost. Post-retrieval compression can reduce this cost, yet existing compressors often operate independently for each query, rely on auxiliary models or rewriting, and introduce online overhead that can offset the benefit of shorter prompts. We revisit RAG compression from a data-mining perspective by aggregating historical query–document–model interactions into reusable evidence views. We first show that modern compressors have unstable gains over simple truncation and can add substantial inference-time latency. We then propose Reusable Evidence View Aggregation (REVA), a framework that mines the target generator's historical attention traces into a document-keyed, budget-agnostic score store. REVA maps token-level attention to readable word units, aggregates importance across repeated document accesses, and renders budget-specific plain-text views that preserve document order and the standard RAG interface. Across four representative benchmarks and modern LLMs, REVA improves generation quality by 1.0–5.8 points over existing advances, while reducing compression overhead by a factor of 5.3 to 15.6, adding less than 40 ms of latency.

### 中文翻译
检索增强生成（RAG）通过将生成过程条件化于检索到的文档上，改进了知识密集型大语言模型（LLM）应用，但更长的上下文会增加延迟、KV 缓存内存和 token 成本。检索后压缩可以降低成本，但现有压缩器往往对每个查询独立运行、依赖辅助模型或重写，并引入可能抵消更短提示收益的在线开销。本文从数据挖掘的视角重新审视 RAG 压缩，将历史 query–document–model 交互聚合为可复用的证据视图。我们首先表明，现代压缩器相对简单截断的优势并不稳定，且可能显著增加推理延迟。随后我们提出 REVA（Reusable Evidence View Aggregation）框架，将目标生成器的历史注意力轨迹挖掘为文档键控、预算无关的分数存储。REVA 将 token 级注意力映射到可读的单词单元，跨重复文档访问聚合重要性，并渲染出保持文档顺序和标准 RAG 接口的预算特定纯文本视图。在四个代表性基准和现代 LLM 上，REVA 相较现有方法将生成质量提升 1.0–5.8 分，同时将压缩开销降低 5.3 到 15.6 倍，额外延迟不到 40 毫秒。

### 核心要点提炼
- **研究背景**：RAG 提升了知识密集任务质量，但长上下文带来延迟、KV 缓存和 token 成本压力
- **研究动机**：现有检索后压缩器按查询独立运行、依赖辅助模型、引入在线开销，且对生成模型"无感知"，浪费预算在模型已内化的信息上
- **核心方法**：将压缩重构为"服务轨迹上的数据挖掘"问题——聚合历史注意力信号构建轻量、模型感知、响应感知的文档视图
- **主要结果**：质量提升 1.0–5.8 分，压缩开销降低 5.3–15.6 倍，额外延迟 <40ms
- **研究意义**：为 RAG 服务提供了可审计、低开销、模型感知的压缩范式，并揭示"历史交互可复用"这一被忽视的机会

## 研究背景与动机

### 领域现状
RAG 通过把外部检索证据接入 LLM 生成过程，显著提升知识密集任务质量，但代价是更长的输入上下文，进而推高预填充延迟、KV 缓存内存与 API token 成本。为缓解这一权衡，检索后压缩（post-retrieval compression）在检索之后、生成之前压缩文档，代表性方法包括句子/段落剪枝（FILCO、RECOMP、EXIT）、token 剪枝（Selective Context、LLMLingua 系列）与重写（CompAct、FaviComp）。

### 现有方法的局限性
作者通过系统性研究揭示三个关键发现：
1. **优势不稳定**：现代压缩器并不必然优于朴素的文档前缀截断基线，而截断几乎无运行时开销
2. **在线成本可能吞噬收益**：压缩本身的在线成本（调用外部模型、评分、重写）可能使整体延迟不降反升
3. **文档被频繁复用却未利用**：检索文档在大量相关查询间被反复访问，历史 query-document-model 交互本可作为未来证据需求的代理信号，却普遍被浪费

### 研究动机
这些发现指向一个被忽视的机会：历史查询提供了关于生成器"知识缺口与证据需求"的可复用信号。在生成过程中，LLM 通过注意力揭示其对检索文档 token 的分配；在大规模 RAG 负载下，相似查询常检索到重叠文档，因此过去的交互可作为未来证据需求的信息性代理。作者由此把压缩重构为**服务轨迹上的数据挖掘问题**。

## 研究问题

**核心研究问题**：能否在不引入在线开销、且保持证据可读性的前提下，利用生成器的历史注意力信号，为每个文档构建模型感知、响应感知、预算无关的可复用压缩视图？

作者明确要实现这一愿景需满足三个部署约束：
1. **离线化打分**：避免为估计 token 重要性而在线调用外部压缩器或用生成器预填充文档（生产环境 TTFT 目标常低于 200ms）
2. **轨迹感知**：token 重要性是上下文相关的，仅用问题注意力会过度强调词法匹配而漏掉仅在生成阶段才重要的答案承载片段
3. **可读性**：原始 token 剪枝会产出难以审计的畸形片段，部署团队需要保留可读文本以便核查

## 方法概述

### 方法架构
REVA 由三部分构成：

1. **注意力挖掘（Attention Mining）**：采集目标生成器在历史 query-document-model 交互中的注意力轨迹，将 token 级注意力映射到可读的单词单元
2. **分数存储（Budget-agnostic Score Store）**：将历史重要性信号聚合为文档键控、预算无关的分数存储，随更多查询访问同一文档而渐进式细化
3. **视图渲染（View Rendering）**：在服务时直接从存储渲染预算特定的纯文本证据视图，保持文档顺序与标准 RAG 接口，最小化在线开销

![[reva_pipeline_page1.png|600]]

### 关键创新
1. **视角转换**：将 RAG 压缩从"逐查询在线压缩"重构为"历史交互挖掘 + 离线聚合 + 在线渲染"，把打分移出在线路径
2. **模型/响应感知**：证据视图基于目标生成器自身的注意力偏好构建，避免在模型已内化信息上浪费预算
3. **预算无关 + 顺序保持**：单一分数存储可渲染任意预算的视图，且保留文档顺序，兼顾可审计性与接口兼容

## 实验结果

- **质量**：在四个代表性基准与现代 LLM 上，REVA 相较现有方法生成质量提升 1.0–5.8 分
- **开销**：压缩开销降低 5.3–15.6 倍，额外延迟 <40ms，满足严格响应性要求
- **质量-延迟前沿**：REVA 达到更强的质量-延迟前沿，证明离线聚合策略在保持质量的同时显著压缩服务成本

## 深度分析

### 研究价值
1. **工程洞见扎实**：三点发现（截断基线竞争力、在线成本吞噬收益、文档复用未利用）直击现有 RAG 压缩的痛点，具有工业参考价值
2. **范式可复用**：将"历史注意力聚合"作为可复用服务工件，与 Prompt Cache、RAGCache 等 KV 缓存复用思路形成互补，但侧重点从"状态复用"转向"证据重要性复用"
3. **可审计性**：保留纯文本、顺序保持的视图，回应了部署团队对可解释性与调试的诉求

### 局限性
1. **冷启动问题**：新文档缺乏历史交互时，REVA 的分数存储无法提供有效信号，可能需要回退策略
2. **生成器绑定**：证据视图针对特定目标生成器的注意力偏好，生成器更换后可能需重新挖掘
3. **注意力信号的充分性**：注意力与证据重要性之间的对应关系并非严格成立，需在更多模型与任务上验证

### 未来工作
- 探索跨生成器的注意力信号迁移，缓解冷启动
- 将注意力挖掘与检索器的相关性信号融合
- 在更长上下文、多跳推理等更复杂 RAG 场景下验证

## 相关论文对比

| 方向 | 代表方法 | 与 REVA 的差异 |
|------|---------|---------------|
| Token 剪枝 | LLMLingua、LongLLMLingua | 逐查询在线压缩，产生难审计的 token 片段 |
| 句子/段落剪枝 | FILCO、RECOMP、EXIT | 依赖外部模型评分，引入在线开销 |
| 重写 | CompAct、FaviComp | 需要辅助模型，可能丢失桥接证据 |
| 状态复用 | Prompt Cache、RAGCache | 复用 KV 缓存（状态）而非压缩文本（证据） |

REVA 的独特之处在于：把历史注意力聚合为**模型感知的文本证据视图**，同时实现低开销、可审计与预算无关三者的统一。
