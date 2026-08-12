---
date: "2026-08-10"
paper_id: "arXiv:2608.09928"
title: "Multimodal Model Diffing for Feature Discovery and Control"
authors: "Hunar Batra, Lachin Naghashyar, Ashkan Khakzar, Philip Torr, Christian Schroeder de Witt, Constantin Venhoff, Ronald Clark"
domain: "大语言模型"
tags:
  - 论文笔记
  - multimodal
  - sparse-autoencoder
  - MLLM
  - interpretability
  - feature-steering
  - model-diffing
quality_score: "8.5/10"
created: "2026-08-12"
updated: "2026-08-12"
status: analyzed
---

# Multimodal Model Diffing for Feature Discovery and Control

## 核心信息
- **论文ID**：arXiv:2608.09928
- **作者**：Hunar Batra*, Lachin Naghashyar*, Ashkan Khakzar, Philip Torr, Ronald Clark, Christian Schroeder de Witt, Constantin Venhoff (*equal contribution)
- **机构**：University of Oxford, Microsoft
- **发布时间**：2026-08-10
- **会议/期刊**：提交至 NeurIPS 2026
- **链接**：[arXiv](http://arxiv.org/abs/2608.09928v1) | [PDF](https://arxiv.org/pdf/2608.09928v1)
- **分类**：cs.CV, cs.AI, cs.CL, cs.LG

## 摘要翻译

### 英文摘要
Multimodal Large Language Models (MLLMs) exhibit strong visual understanding, yet the internal features that cause these behaviors remain difficult to identify, audit, or control. We introduce MMDiff, a multimodal model-diffing framework that trains multimodal SAEs and turns them into feature-level interfaces for discovering and controlling multimodal behavior. MMDiff supports three uses: (i) feature isolation, by diffing a base-LM SAE against its multimodal-adapted counterpart; (ii) task-specific feature detection, via per-token contrastive firing analysis; and (iii) feature-level control, by causally removing or steering the discovered feature directions. We train multimodal SAEs for three MLLM families, LLaVA-MORE, PaliGemma 2, and InternVL3.5. MMDiff discovers sparse, causally specific features whose removal selectively degrades target behaviors by an average of 12% on spatial tasks and 17% on OCR, and reduces attack success rate by 24% on multimodal safety attacks, with no impact on VQA performance.

### 中文翻译
多模态大语言模型表现出强大的视觉理解能力，但导致这些行为的内部特征仍然难以识别、审计或控制。我们引入 MMDiff，一个多模态模型差分框架，通过训练多模态稀疏自编码器（SAE）将其转化为用于发现和控制多模态行为的特征级接口。MMDiff 支持三种用途：(i) 特征隔离——对比基座 LM SAE 与多模态适配版本以识别多模态训练改变的特征；(ii) 任务特定特征检测——通过逐 token 对比激活分析隔离因果特征；(iii) 特征级控制——因果移除或操控发现的特征方向。我们在 LLaVA-MORE、PaliGemma 2 和 InternVL3.5 三个 MLLM 家族上训练多模态 SAE。MMDiff 发现了稀疏且因果特定的特征：移除这些特征使空间任务性能平均下降 12%、OCR 下降 17%、多模态安全攻击成功率降低 24%，且不影响 VQA 性能。操控这些特征在空间和 OCR 任务上比标准单层操控基线分别提升 +3.6% 和 +1.8%。

### 核心要点提炼
- **研究背景**：MLLM 内部可解释性远远落后于纯文本 LLM，多模态 SAE 尚未被用于发现和控制 MLLM 行为
- **研究动机**：现有 SAE 方法无法隔离"多模态训练引入了哪些新特征"，也无法直接用于定向控制
- **核心方法**：三阶段流水线——(1) 训练多模态 SAE → (2) 差分识别被视觉训练"改造"的特征 → (3) 对比激活发现任务特定特征
- **主要结果**：特征移除只影响目标行为（12-24%），不影响 VQA；特征操控超越基线 +1.8-3.6%

## 研究背景与动机

### 领域现状
MLLM 如 LLaVA、PaliGemma、InternVL 展现出强大的视觉理解能力，但内部机制几乎不透明。SAE 在纯文本 LLM 上成功将隐藏状态分解为可解释的特征方向，但在多模态领域的应用仍处于早期阶段。

### 现有方法的局限性
- **后验分析不足**：现有 SAE 只能描述模型已有特征，无法区分哪些是新引入的多模态特征
- **缺乏控制接口**：SAE 特征用于"理解"而非"操控"，没有从可解释性直接转化为控制手段
- **特征语义不纯**：文本 SAE 特征在多模态环境下语义混淆

### 研究动机
将 SAE 从一个"被动观察"工具升级为"主动发现+控制"的多模态行为接口。

## 研究问题

### 核心研究问题
1. 多模态训练改变了基座 LM 的哪些内部特征方向？
2. 能否精确发现与特定任务（空间推理、安全、OCR）因果关联的特征？
3. 发现的特征能否用于定向操控（移除不安全行为、增强期望能力）？

## 方法概述

### 核心思想
**"模型差分"**（Model Diffing）：对比基座 LM SAE 和多模态适配 SAE 之间的特征差异，识别多模态训练引入的新方向，然后以这些方向为接口进行因果控制。

### 方法框架

#### 整体架构（三阶段流水线）

```
阶段1: 训练多模态SAE
  基座LM → 初始化SAE → 在多模态激活上微调 → 多模态SAE字典

阶段2: 识别适配特征
  计算 decoder cosine(c_f) + 视觉能量(E_v)
  → 筛选: c_f 在 bottom 25% AND E_v > ε
  → 适配特征集 A (~5-20% 的特征)

阶段3: 发现任务特定特征
  对比激活: Δp_f = p_f(D_tgt) - p_f(D_base)
  → Fisher检验 + 词汇过滤
  → 任务特定特征集 T (几百到上千个特征)
```

#### 各模块详细说明

**阶段1：训练多模态 SAE**
- 从基座 LM 匹配的 SAE 套件初始化（warm start 保留语言特征字典）
- 在 50K VQAv2 图文对上缓存激活进行微调
- 训练三种掩码变体：全序列/图像专用/文本专用
- 文本专用 SAE 获得最低 FVU（最佳重构），且最接近基座 LM 字典
- 覆盖三个 MLLM 家族：LLaVA-MORE (LLaMA-3.1-8B)、PaliGemma 2 (Gemma-2-2B)、InternVL3.5-2B (Qwen3-1.7B)

**阶段2：识别适配特征（两个信号）**
- **几何重定向 (decoder cosine)**：$c_f = \cos(W_{\text{dec},f}^{\text{LLM}}, W_{\text{dec},f}^{\text{MLLM}})$，低 $c_f$ 表示特征被"重新分配"了语义
- **模态偏好 (视觉能量)**：$E_v(f) = \mathbb{E}_{\text{vision}}[h_f^2]$，识别视觉响应特征
- 联合筛选：$c_f$ 在 bottom 25% AND $E_v > \epsilon$ → 产生 ~5-20% 的适配特征

**阶段3：发现任务特定特征**
- **分布偏移**：计算 $p_f(D_{\text{tgt}}) - p_f(D_{\text{base}})$ 和 odds ratio
- **词汇过滤**：用中性提示替换原始问题，仅保留图像驱动的特征
- **与适配集交集**：仅保留同时属于 A 的特征 → 空间任务 711-1400 个特征、安全 1061 个、OCR 1070 个

### MMDiff-CAA 操控
将标准 CAA 与 SAE 特征方向结合：
$h'_{\ell_f} \leftarrow h_{\ell_f} + \alpha d_{\ell_f} + \gamma_f v_f$
- $d_\ell$：多层 CAA 方向
- $v_f$：MMDiff 发现的特征解码器方向
- 多层级 + 特征特定层注入

## 实验结果

### 空间推理
- **特征移除**：VSR 准确率下降 6-31%（均值 -10.1% 到 -14.6%，跨三个模型）
- **VQA 影响**：|ΔVQA| ≤ 1.5%，ΔCtrl ≈ 0
- **MMDiff-CAA 操控**：+3.6% avg（峰值 +15.4% on "ahead of"）

### 多模态安全
- **特征移除**：6 个 VLSBench 类别中每类 top 特征降低 ASR 17-28%
- **1061 特征扫描**：均值 ΔASR = -9.67%，ΔVQA = -0.03%
- **关键特性**：安全能力下降且不损害通用 VQA

### OCR
- **MMDiff-CAA 操控**：+1.8% avg over 单层 CAA（峰值 +10.58%）
- 效果集中于解码器方向本身是强操控信号的特征

### 消融研究
- 去掉适配特征过滤 → VSR 下降更大但 VQA 被摧毁（-24-26%），证明适配过滤对选择性至关重要
- 仅词汇过滤 → 不防止 VQA 崩塌
- 仅适配过滤 → VQA 保留但几乎无任务效果
- 完整管线 → -12.3% VSR, -0.1% VQA ← **三者缺一不可**

## 深度分析

### 研究价值评估

#### 理论贡献
- **模型差分方法论**：将代码 diffing 思想迁移到神经网络可解释性，开辟了新的分析范式
- **从观察到控制的桥梁**：SAE 首次从纯可解释性工具升级为行为控制接口
- **多模态 SAE 训练策略**：证明了文本专用训练在保持字典对齐方面的优势

#### 实际应用价值
- **安全审计**：无需重新训练即可发现和移除不安全行为
- **能力增强**：通过特征操控提升特定任务性能
- **模型调试**：精确隔离多模态训练引入的新能力

### 方法优势
1. **选择性极强**：完整管线实现 -12.3% VSR 且仅 -0.1% VQA，选择性远优于任何消融变体
2. **跨模型通用**：在三个不同架构的 MLLM 家族上一致有效
3. **多用途**：统一框架支持发现、审计、安全移除和能力增强

### 局限性
1. **依赖预训练 SAE**：需要为每种基座 LM 匹配 SAE 套件，对新模型覆盖有限
2. **特征选择依赖阈值**：多个超参数（bottom 25%, OR≥3）需要手工设定
3. **操控量级有限**：+1.8-3.6% 的提升较小，实际应用价值有待验证
4. **仅分析部分层**：未覆盖视觉编码器和投影器的可解释性

## 技术路线定位

本文属于**机制可解释性** × **多模态学习**交叉领域。在纯文本 SAE 基础上开创了"多模态模型差分"这一新方向。

## 我的综合评价

**8.5/10** — 方法论贡献突出，实验全面扎实。将 SAE 从观察工具升级为控制接口是一个重要的概念推进。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 模型差分 + 特征控制接口的概念组合非常新颖 |
| 技术质量 | 8/10 | 三阶段流水线设计合理，消融研究彻底 |
| 实验充分性 | 9/10 | 三个模型家族、三个任务域、系统消融，实验极为全面 |
| 写作质量 | 8/10 | 技术细节清晰，动机阐述充分 |
| 实用性 | 7/10 | 依赖预训练 SAE 套件，操控增益有限 |

> [!tip] 关键启示
> 多模态 SAE 不仅是"看看模型学到了什么"的可解释性工具，更是精确操控模型行为的因果接口——"差分"思维是连接理解与控制的桥梁。

> [!warning] 注意事项
> - 完整管线的三阶段缺一不可（仅适配过滤无效果，仅对比激活摧毁通用能力）
> - 操控增益（+1.8-3.6%）较小，尚不足以支持实际部署
> - 需要预训练 SAE 套件，对新架构覆盖有限

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐！本文是将机制可解释性转化为实际控制能力的典范之作，方法论优雅、实验扎实、应用前景广阔。
