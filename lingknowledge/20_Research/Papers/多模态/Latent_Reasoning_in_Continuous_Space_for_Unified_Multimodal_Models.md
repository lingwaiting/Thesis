---
date: "2026-11-02"
paper_id: "arXiv:2610.42149"
title: "Latent Reasoning in Continuous Space for Unified Multimodal Models"
authors: "Byungwoo Jeon, Yoonwoo Jeong, Hyunseok Lee, Minsu Cho, Jinwoo Shin"
domain: "多模态"
tags:
  - 论文笔记
  - 多模态
  - 文生图
  - 思维链
  - 潜在推理
  - 强化学习
quality_score: "8.3/10"
created: "2026-11-02"
updated: "2026-11-02"
status: analyzed
---

# Latent Reasoning in Continuous Space for Unified Multimodal Models

## 核心信息
- **论文ID**：arXiv:2610.42149
- **作者**：Byungwoo Jeon, Yoonwoo Jeong, Hyunseok Lee, Minsu Cho, Jinwoo Shin
- **机构**：--
- **发布时间**：2026-10-30
- **类别**：cs.CV, cs.CL, cs.LG
- **链接**：[arXiv](https://arxiv.org/abs/2610.42149) | [PDF](https://arxiv.org/pdf/2610.42149)
- **来源**：arXiv

## 摘要翻译

### 英文摘要
Unified Multimodal Models (UMMs) achieve remarkable success on diverse multimodal understanding and generation tasks, yet they still struggle to effectively leverage language-based chain-of-thought reasoning for image generation. As a result, existing reasoning-augmented UMMs often fail to inherit the test-time scaling and fine-grained controllability observed in large language models. We attribute this failure to a fundamental modality mismatch: reasoning is performed in a discrete token space, whereas image generation operates in a continuous space. To bridge this gap, we introduce a Latent Reasoning framework in Continuous space (LARC), which alleviates the token-level bottleneck by reasoning over continuous hidden states. Specifically, the training framework consists of two stages: (i) curriculum supervised fine-tuning (SFT), and (ii) information-gain reinforcement learning (RL). First, the curriculum SFT stage gradually converts token-level reasoning into latent reasoning. Subsequently, the information-gain RL stage self-evolves latent reasoning without ground truth supervision. Experiments across diverse text-to-image generation benchmarks show that LARC consistently improves generation quality, prompt following, and compositional fidelity. Notably, LARC achieves state-of-the-art performance on GenEval with a 7%p gain over BAGEL and demonstrates clear test-time scaling behavior, outperforming token-based generation under both parallel and sequential test-time scaling strategies.

### 中文翻译
统一多模态模型（UMM）在多样的多模态理解与生成任务上取得了显著成功，但在图像生成中仍难以有效利用基于语言的思维链推理。因此，现有的推理增强 UMM 往往无法继承大语言模型所展现的测试时扩展（test-time scaling）与细粒度可控性。作者将此失败归因于一个根本性的模态错配：推理在离散 token 空间中进行，而图像生成在连续空间中进行。为弥合这一鸿沟，作者提出连续空间潜在推理框架 LARC，通过在连续隐状态上进行推理来缓解 token 级瓶颈。训练框架包含两个阶段：(i) 课程监督微调（curriculum SFT），(ii) 信息增益强化学习（information-gain RL）。课程 SFT 阶段逐步把 token 级推理转化为潜在推理；随后信息增益 RL 阶段在没有真值监督的情况下自演化潜在推理。跨多种文生图基准的实验表明，LARC 一致地提升生成质量、提示遵循与组合保真度。值得注意的是，LARC 在 GenEval 上取得 SOTA，较 BAGEL 提升 7 个百分点，并展现出清晰的测试时扩展行为，在并行与串行两种测试时扩展策略下均优于基于 token 的生成。

### 核心要点提炼
1. **问题**：UMM 难以把语言思维链推理用于图像生成，因为推理（离散 token）与生成（连续空间）存在模态错配。
2. **方法**：LARC 在连续隐状态上做推理，绕过 token 级瓶颈；训练分课程 SFT + 信息增益 RL 两阶段。
3. **关键结果**：GenEval 上 SOTA（较 BAGEL +7p），并首次在文生图任务上展示出清晰的 test-time scaling 行为。

## 方法概述

### 方法架构
![[2610.42149_fig1.png|700]]

LARC 的核心逻辑：
- **连续空间推理**：把推理从离散 token 空间迁移到连续隐状态空间，消除"离散推理 ↔ 连续生成"的模态错配。
- **课程 SFT（阶段一）**：以课程方式逐步把 token 级推理蒸馏为潜在（latent）推理。
- **信息增益 RL（阶段二）**：无需真值监督，通过信息增益目标让潜在推理自演化。
- **测试时扩展**：潜在推理天然支持并行/串行两种 test-time scaling，继承 LLM 的可控性。

### 关键创新
1. **模态错配的定位**：明确指出 UMM 无法继承 test-time scaling 的根因是"离散推理 vs 连续生成"。
2. **连续潜在推理**：在隐状态上推理，打破 token 瓶颈，是与既有 token 级 CoT 方法的本质区别。
3. **信息增益 RL**：用信息增益作为自监督信号，使潜在推理摆脱对 ground truth 的依赖。

## 实验结果

- **基准**：多样文生图基准（含 GenEval），关注生成质量、提示遵循、组合保真度。
- **关键结果**：GenEval 上取得 SOTA，较 BAGEL 提升 7 个百分点；在并行与串行两种 test-time scaling 策略下均优于基于 token 的生成。
- **结论**：连续空间潜在推理能稳定提升文生图质量，并恢复测试时扩展能力。

## 深度分析

### 优势
- 对"为什么 UMM 无法享受 test-time scaling"给出了清晰、可操作的理论归因（模态错配）。
- 两阶段训练（课程 SFT + 信息增益 RL）设计完整，且 RL 阶段不依赖 ground truth，扩展性好。
- 在真实基准（GenEval）上取得 7p 的显著提升，且验证了 test-time scaling，说服力强。

### 局限与开放问题
- 实验集中于文生图（text-to-image），对视频生成、多模态理解等任务的可迁移性待验证。
- 信息增益 RL 的目标设计与收敛稳定性在摘要中未展开，潜在推理的可解释性仍是开放问题。
- 与既有"latent/quiet reasoning"（如 Coconut 等 LLM 潜在思维）路线的系统性对比不足。

### 与相关工作的关系
- 相比 token 级 CoT 的 UMM：把推理搬到连续空间，直接针对模态错配，属于"连续潜在思维"路线。
- 相比 BAGEL（GenEval 前任 SOTA）：在组合保真度与 test-time scaling 上更进一步。
- 与 LLM 的 latent reasoning（Coconut 等）呼应，是把该思想迁移到生成式多模态的代表性工作。
