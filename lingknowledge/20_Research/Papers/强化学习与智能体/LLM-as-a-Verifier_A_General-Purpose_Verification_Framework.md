---
date: "2026-07-07"
paper_id: "arXiv:2607.05391"
title: "LLM-as-a-Verifier: A General-Purpose Verification Framework"
authors: "Jacky Kwok, Shulu Li, Pranav Atreya, Yuejiang Liu, Yixing Jiang, Chelsea Finn, Marco Pavone, Ion Stoica, Azalia Mirhoseini"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - LLM-Verifier
  - Verification-Scaling
  - Test-Time-Compute
  - Reward-Modeling
  - Agentic-AI
  - SWE-Bench
  - Terminal-Bench
quality_score: "8.8/10"
created: "2026-07-07"
updated: "2026-07-07"
status: analyzed
---

# LLM-as-a-Verifier: A General-Purpose Verification Framework

## 核心信息
- **论文ID**：arXiv:2607.05391
- **作者**：Jacky Kwok, Shulu Li, Pranav Atreya, Yuejiang Liu, Yixing Jiang, Chelsea Finn, Marco Pavone, Ion Stoica, Azalia Mirhoseini
- **机构**：Stanford University, UC Berkeley, NVIDIA Research
- **发布时间**：2026-07-06
- **会议/期刊**：--
- **链接**：[arXiv](https://arxiv.org/abs/2607.05391) | [PDF](https://arxiv.org/pdf/2607.05391v1)
- **引用**：--

## 摘要翻译

### 英文摘要
Scaling pre-training, post-training, and test-time compute have become the central paradigms for improving the capabilities of LLMs. In this work, we identify verification—the ability to determine the correctness of a solution—as a new scaling axis. To unlock this and demonstrate its effectiveness, we introduce LLM-as-a-Verifier, a general-purpose verification framework that provides fine-grained feedback for agentic tasks without requiring additional training. Unlike standard LM judges that prompt LLMs to produce discrete scores for candidate solutions, LLM-as-a-Verifier computes the expectation over the distribution of scoring token logits to generate continuous scores. This probabilistic formulation enables verification to scale along multiple dimensions: (1) score granularity, (2) repeated evaluation, and (3) criteria decomposition. In particular, we show that scaling the scoring granularity leads to better separation between positive and negative solutions, resulting in more calibrated comparisons. Moreover, scaling repeated evaluation and criteria decomposition consistently lead to additional gains in verification accuracy through variance and complexity reduction. We further introduce a cost-efficient ranking algorithm for selecting the best solution among candidates using the verifier's continuous scores. LLM-as-a-Verifier achieves state-of-the-art performance on Terminal-Bench V2 (86.5%), SWE-Bench Verified (78.2%), RoboRewardBench (87.4%), and MedAgentBench (73.3%). Beyond verification, the fine-grained signals from LLM-as-a-Verifier can also serve as a proxy for estimating task progress. We build an extension for Claude Code, enabling developers to monitor and improve their own agentic systems. Finally, we show that LLM-as-a-Verifier can provide dense feedback for RL, improving the sample efficiency of SAC and GRPO on robotics and mathematical reasoning benchmarks.

### 中文翻译
扩展预训练、后训练和测试时计算已成为提升LLM能力的核心范式。本文将验证（verification）——判断解决方案正确性的能力——识别为一个新的扩展维度。为此，我们提出了LLM-as-a-Verifier，一个通用的验证框架，无需额外训练即可为Agent任务提供细粒度反馈。与传统的LM Judge（提示LLM输出离散评分）不同，LLM-as-a-Verifier计算评分token logits分布的期望来生成连续评分。这种概率化公式使验证能够沿多个维度扩展：（1）评分粒度，（2）重复评估，（3）标准分解。实验表明，扩展评分粒度能更好地分离正负样本，产生更校准的比较；而扩展重复评估和标准分解则通过降低方差和复杂度持续提升验证准确率。我们还提出了一种成本高效的排序算法，利用验证器的连续评分在候选中选择最优解。LLM-as-a-Verifier在Terminal-Bench V2（86.5%）、SWE-Bench Verified（78.2%）、RoboRewardBench（87.4%）和MedAgentBench（73.3%）上达到SOTA。此外，验证器的细粒度信号可作为任务进度的代理，我们为Claude Code构建了扩展以监控和改进Agent系统。最后，验证器可为RL提供稠密奖励，提升SAC和GRPO在机器人和数学推理基准上的样本效率。

### 核心要点提炼
- **研究背景**：Agent系统在代码、机器人、医疗等领域广泛应用，但如何从多个候选轨迹中选择最佳方案（Best-of-N）是关键瓶颈
- **研究动机**：传统LM Judge使用离散评分导致高平局率（Tie Rate），无法有效区分复杂解的质量差异
- **核心方法**：通过计算评分token的完整概率分布期望而非仅取argmax，得到连续评分，配合粒度扩展、重复评估、标准分解三维度提升验证质量
- **主要结果**：在四个跨域基准上达到SOTA；可作为RL稠密奖励信号提升样本效率
- **研究意义**：首次将"验证"形式化为独立的Scaling维度，证明无需训练即可获取强大的验证能力

## 研究背景与动机

### 领域现状
当前LLM能力提升遵循三个核心Scaling范式：预训练（Pre-training）、后训练（Post-training）和测试时计算（Test-time Compute）。Best-of-N采样（生成N个候选、选择最佳）是测试时计算的关键技术，但需要一个可靠的验证器（Verifier）来判断候选项的质量。

### 现有方法的局限性
1. **LM Judge（离散评分）**：提示模型输出离散分数token并选最高概率token，导致评分粗糙、平局率高（Terminal-Bench上27%平局率），无法区分质量相近的候选轨迹
2. **训练奖励模型（Reward Model）**：受训练数据分布限制，跨域泛化能力差，且需要大量标注数据

### 研究动机
Oracle实验表明：当有一个完美的验证器时，Terminal-Bench V2的Pass@K可达98.9%。但现实中缺乏可靠的通用的验证器。这驱动作者提出一个无需训练、跨域通用的细粒度验证框架。

## 研究问题

### 核心研究问题
如何构建一个通用的、无需训练的验证框架，能够提供足够细粒度的反馈信号来有效区分LLM Agent生成的候选轨迹，并且在多个维度上可扩展？

## 方法概述

### 核心思想
将验证器视为一个"概率评分器"而非"离散裁判"：不取最高概率的离散分数，而是计算整个评分token概率分布的期望，从而获得连续评分。这个简单的改变使得评分天然具有更高的分辨力（零平局率），并解锁了三个可独立扩展的维度。

### 方法框架

#### 整体架构

![[SOTA.png|800]]

> 图1：LLM-as-a-Verifier的整体效果概览——在代码（Terminal-Bench V2, SWE-Bench Verified）、机器人（RoboRewardBench）和医疗（MedAgentBench）四个基准上均达到SOTA。

#### 核心公式

**细粒度奖励估计**：

给定任务提示 $x$、语言模型 $p_\theta$、评估标准 $c$ 和候选轨迹 $\tau_i$, $\tau_j$，LLM-as-a-Verifier不取argmax，而是计算期望：

$$R(x, \tau) = \frac{1}{CK} \sum_{c=1}^{C} \sum_{k=1}^{K} \sum_{g=1}^{G} p_{\theta}(v_g \mid x, c, \tau)\,\phi(v_g)$$

其中 $C$ 是评估标准数量，$K$ 是重复评估次数，$G$ 是评分token粒度级别，$p_{\theta}(v_g \mid x, c, \tau)$ 是模型对评分token $v_g$ 的概率，$\phi(v_g)$ 将token映射到标量值。

**偏好概率转换**（Bradley-Terry模型）：

$$P(\tau_i \succ \tau_j \mid x) = \frac{1}{1+\exp\!\big(-(R(x,\tau_i)-R(x,\tau_j))\big)}$$

#### 三个扩展维度

**1. 评分粒度扩展（Score Granularity, $G$）**

增大评分token集 $V_{\text{score}}$ 的规模（如从1-5扩展到1-20），为模型提供更精细的概率投影空间。实验证明信噪比（SNR）从 $G=1$ 的0.775提升到 $G=20$ 的0.799。

**2. 重复评估扩展（Repeated Evaluation, $K$）**

通过 $K$ 次独立评估的平均来降低方差（$\mathcal{O}(1/K)$）。$K=1$时74.7%准确率提升到$K=16$时77.5%。早期收益来自方差降低，后期收益递减因相关偏差。

**3. 标准分解扩展（Criteria Decomposition, $C$）**

将单一的"是否正确"分解为多个子标准（如代码轨迹分解为：Specification/Output/Errors），各标准独立评分后取均值。单标准准确率75.2%-76.4%，三标准集成后达78.3%。

#### 概率化枢轴锦标赛（Probabilistic Pivot Tournament, PPT）

![[pivot_tournament_page1.png|800]]

> 图2：PPT的五步流程——(1)候选池→(2)环形淘汰→(3)枢轴选择→(4)枢轴锦标赛→(5)最终选择。将复杂度从 $\mathcal{O}(N^2)$ 降至 $\mathcal{O}(Nk^2)$。

PPT三步走：
1. **环形淘汰（Ring Pass）**：随机哈密顿环结构确保每个候选项恰好出现在"A位置"和"B位置"各一次，消除LLM的位置偏差
2. **枢轴选择（Pivot Selection）**：基于环形得分选择Top-$k$作为枢轴集，将后续验证预算集中在最有希望的候选项上
3. **枢轴轮次（Pivot Rounds）**：仅对枢轴vs非枢轴和枢轴vs枢轴进行两两比较，大幅降低复杂度

总比较次数从 $\binom{N}{2}$ 降至 $N + k(N-k) + \binom{k}{2} = \mathcal{O}(Nk^2)$。

## 实验结果

### 实验设置

**基准测试**：
| 基准 | 领域 | 特点 |
|------|------|------|
| Terminal-Bench V2 | 代码 | Shell环境，长时间任务，多步推理 |
| SWE-Bench Verified | 代码 | 500个真实GitHub Issue，需生成补丁 |
| RoboRewardBench | 机器人 | 操作轨迹偏好判断，需要视觉理解 |
| MedAgentBench | 医疗 | 电子健康记录环境，安全关键场景 |

### 主要结果

![[SOTA.png|800]]

**跨基准SOTA性能**：

| 基准 | Baseline最佳 | Pass@1 | Oracle | **LLM-as-a-Verifier** |
|------|-------------|--------|--------|----------------------|
| Terminal-Bench V2 | 84.7% (GPT-5.5) | 83.1% | 92.1% | **86.5%** |
| SWE-Bench Verified | 76.8% (Opus 4.5) | 76.1% | 84.4% | **78.2%** |
| RoboRewardBench | 81.4% (RoboReward-8B) | -- | -- | **87.4%** |
| MedAgentBench | 70.2% (Opus 4.8) | 70.2% | 75.0% | **73.3%** |

![[judge_vs_verifier_page1.png|600]]

> 图3：Verifier（连续）vs Judge（离散）的对比——(左)Verifier在所有评估预算下均优于Judge；(右)Judge有26.7%平局率，Verifier为零平局。

### 关键消融：Judge vs Verifier

在`query-optimize`案例上，100次重复评估：
- 离散Judge（1-5分）：88/100次平局，仅12/100次正确排序
- 连续Verifier（1-5分）：69/100次正确排序，零平局
- 连续Verifier（1-20分）：77/100次正确排序，零平局

### 验证扩展实验

![[granularity_page1.png|600]]

> 图4：三个扩展维度的独立效果——评分粒度、重复评估和标准分解均独立提升验证准确率。

### 任务进度代理（Value-Order Correlation）

![[voc_pytorch_model_cli_page1.png|800]]

> 图5：Verifier分数与代码生成步骤之间的VOC——成功轨迹分数单调递增，失败轨迹分数持续低迷。

- Terminal-Bench V2：成功轨迹VOC 0.848，失败轨迹0.769
- RoboRewardBench：Qwen 3.6 Verifier VOC达**0.966**，远超RoboReward-8B（0.877）和TOPReward（0.565）

### RL稠密奖励

![[combined_libero_math_page1.png|800]]

> 图6：Verifier作为RL稠密奖励提升样本效率——LIBERO上1.8倍，MATH上1.1倍。

- **Off-policy（SAC + LIBERO）**：$\approx 1.8\times$样本效率，最终成功率0.76 vs 0.69
- **On-policy（GRPO + MATH）**：$\approx 1.1\times$样本效率（约10%步数减少）

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：验证作为新的Scaling维度**
  - 创新点：首次将"验证"与预训练、后训练、测试时计算并列为LLM能力的独立扩展轴
  - 学术价值：为Test-time Compute领域提供了新的理论框架
  - 影响范围：Agent系统、RL训练、代码生成等多个子领域

- **贡献2：概率化评分范式**
  - 创新点：从"取最高概率token"转变为"计算概率分布期望"，零额外训练成本换取细粒度信号
  - 学术价值：简单而优雅的数学框架，可被任何能输出logprob的模型采用

- **贡献3：三维度验证扩展理论**
  - 创新点：证明粒度扩展（信噪比提升）、重复评估（方差降低）和标准分解（偏差降低）是三种互补且正交的改进方式

#### 实际应用价值
- **应用场景1：Agent系统的Best-of-N选择**
  - 适用性：任何生成多个候选轨迹的Agent系统
  - 优势：即插即用，无需训练，跨域通用
  - 潜在影响：已集成到Claude Code扩展（TurboAgent）

- **应用场景2：RL训练的稠密奖励**
  - 适用性：稀疏奖励环境中的off-policy和on-policy RL
  - 优势：无需环境特定的奖励塑形，自动提供进度信号
  - 潜在影响：显著降低RL训练的样本复杂度

### 方法优势详解

1. **零训练成本**：与需要大量标注数据训练的奖励模型不同，LLM-as-a-Verifier直接利用预训练LLM的logprob输出
2. **跨域通用**：同一框架在代码、机器人、医疗三个完全不同的领域均达到SOTA
3. **信号质量**：连续评分消除平局，SNR随粒度单调提升，VOC高达0.966
4. **计算效率**：PPT将Best-of-N的排序复杂度从 $\mathcal{O}(N^2)$ 降至 $\mathcal{O}(Nk^2)$

### 局限性分析

1. **需要logprob访问**：核心方法依赖模型的token级logprob，对不暴露logprob的闭源模型需要两阶段变通方案（见Appendix）
2. **验证成本**：虽然有PPT优化，仍需多次LLM调用（粒度×重复×标准），对延迟敏感场景需权衡
3. **评估标准需人工设计**：标准分解（Criteria Decomposition）的效果依赖人为设计的子标准质量
4. **未见对抗鲁棒性分析**：论文未讨论验证器本身可能被欺骗或操纵的场景

### 技术路线定位

本文属于 **Test-time Compute Scaling** 技术路线，但开辟了新的子方向：**Verification Scaling**。

```
Best-of-N采样 → Reward Model训练 → LLM-as-a-Judge → LLM-as-a-Verifier（本文）
                                                    ↓
                                     Verification Scaling:
                                     (粒度↑ + 重复↑ + 分解↑)
```

## 我的综合评价

### 价值评分

**总体评分**：**8.8/10** — 论文提出了一个简洁优雅且实用的框架，将"验证"形式化为独立Scaling维度的洞见具有启发性。

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 将验证提升为独立的Scaling轴，概率化评分的简单思路产生显著效果，视角新颖 |
| 技术质量 | 9/10 | 数学框架严谨（期望vs argmax），三个扩展维度正交互补，PPT算法设计巧妙 |
| 实验充分性 | 9/10 | 跨三个领域四个基准的充分验证，消融实验覆盖所有扩展维度，RL实验覆盖on/off-policy |
| 写作质量 | 9/10 | 结构清晰，图文并茂，案例研究（query-optimize）极具说服力 |
| 实用性 | 8/10 | 已集成Claude Code扩展，但对闭源模型的支持依赖变通方案 |

### 重点关注

- 值得深入理解VOC作为任务进度代理的监控应用
- PPT的环形设计消除位置偏差的方法可推广到其他LLM排序任务
- 三维扩展中"标准分解"对复杂任务的设计空间值得进一步探索

> [!tip] 关键启示
> 有时最简单的改变（从argmax变为期望）就能解锁全新的能力维度——不要低估概率分布中蕴藏的信息量。

> [!warning] 注意事项
> - 需模型支持logprob输出，使用前确认模型能力
> - 多维度扩展会增加推理成本，需根据延迟预算调节
> - 验证器本身的质量受基座模型能力限制

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读！这是Test-time Compute领域的重要贡献，思路简洁优雅、实验扎实全面，具有很高的实用价值和理论启发性。
