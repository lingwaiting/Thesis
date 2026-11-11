---
date: "2026-11-11"
paper_id: "arXiv:2611.12732"
title: "Σ-LeWM: Learning World Models via S-Information for Robot Control"
authors: "Xinrui Zu, Michael Przystupa, Zhao Yang, Shujian Yu, Kevin Sebastian Luck"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - World-Model
  - Model-Based-RL
  - Robot-Learning
  - Representation-Learning
  - 世界模型
quality_score: "8.5/10"
created: "2026-11-11"
updated: "2026-11-11"
status: analyzed
---

# Σ-LeWM: Learning World Models via S-Information for Robot Control

## 核心信息
- **论文ID**：arXiv:2611.12732
- **作者**：Xinrui Zu, Michael Przystupa, Zhao Yang, Shujian Yu, Kevin Sebastian Luck
- **机构**：--
- **发布时间**：2026-11-09
- **类别**：cs.LG, cs.AI, cs.RO
- **链接**：[arXiv](https://arxiv.org/abs/2611.12732) | [PDF](https://arxiv.org/pdf/2611.12732)

## 摘要翻译

### 英文摘要
World models have increasingly been applied as a model-based control method for robotics tasks such as manipulation and locomotion. While early world model architectures relied on decoders to prevent mode collapse and learn a predictable latent space, recent works on world models, such as Joint Embedding Predictive Architecture or LeWorldModel, increasingly aim to learn embeddings without a grounding decoder, utilising only a latent forward dynamics predictor and a stabilising entropy term. In this paper, we set out to demonstrate that the further introduction of two additional latent losses to LeWorldModel, a backward dynamics predictor and an inverse dynamics action predictor, enforces further structure on the latent embedding, leading to further gains in both performance and planning efficiency. These are critical properties for improving the applicability of such methods to real-time model-based control of real-world robots.

### 中文翻译
世界模型正日益被用作机器人操作与运动等任务中基于模型的控制方法。早期世界模型架构依赖解码器来防止模式坍塌、学习可预测的潜在空间；而近期工作（如 JEPA、LeWorldModel）则越来越多地尝试在不使用 grounding 解码器的情况下学习嵌入，仅利用一个潜在前向动力学预测器和一个稳定的熵项。本文证明：向 LeWorldModel 再引入两个额外的潜在损失——后向动力学预测器和逆动力学动作预测器——能为潜在嵌入施加更强的结构，从而在性能与规划效率两方面都取得进一步增益。这两点性质对于提升此类方法在真实机器人实时基于模型控制中的适用性至关重要。

### 核心要点提炼
- **研究背景**：无解码器世界模型（JEPA/LeWM）正成为主流，但潜在空间结构仍有改进空间。
- **研究动机**：希望在不引入推理期额外开销的前提下，为潜在嵌入施加更强的结构，提升长程预测与规划效率。
- **核心方法**：在 LeWM 基础上加两个辅助损失（后向动力学 + 逆动力学），通过 DCL 训练，推导出「Σ-信息」（三路互信息之和）的理论解释。
- **主要结果**：在长程任务上优于标准 LeWM，规划效率（CEM 采样量）显著改善。
- **研究意义**：为无解码器世界模型提供了信息论层面的统一视角与轻量改进范式。

## 研究背景与动机

### 领域现状
世界模型（World Model）是基于模型的强化学习（MBRL）的核心组件，用于预测「场景在动作后如何变化」。早期方法（如 Dreamer 系列）依赖解码器重建观测来约束潜在空间、防止模式坍塌。近期兴起的联合嵌入预测架构（JEPA）与 LeWorldModel（LeWM）则放弃解码器，仅用潜在前向动力学预测器 + 稳定化熵正则项（SIGReg）来学习表征，从而摆脱了对像素级重建的依赖。

### 现有方法的局限性
- 仅靠前向预测 + 熵正则，潜在空间对「状态-动作-下一状态」三元组的整体结构刻画不足。
- 缺少对反向（从未来推现在）与逆（从状态对推动作）信息的显式利用。
- 长程（long-horizon）预测与规划效率仍受限于潜在表征的质量。

### 研究动机
作者认为，通过显式最大化后向与逆方向的信息目标，可以为潜在空间施加额外的结构性偏置，进而同时提升预测精度与规划效率，且不增加推理期计算。

## 研究问题

### 核心研究问题
如何在**不增加推理期额外动力学模型**的前提下，为无解码器世界模型（LeWM）的潜在空间施加更强的结构，从而提升长程预测能力与规划效率？

## 方法概述

### 核心思想
「Σ-LeWM」在标准 LeWM 上额外加入两个**仅训练期使用**的辅助预测函数——后向动力学 $f_\text{back}(z_{t+1},\tilde a_t)\to z_t$ 与逆动力学 $f_\text{inv}(z_t,z_{t+1})\to \tilde a_t$——并用 DCL 对比损失训练它们。推理期规划与 LeWM 完全一致，但潜在空间被注入了更丰富的结构。

### 方法框架

#### 整体架构

![[fig1_overview_page1.png|700]]

> 图1：Σ-LeWM 主要实验结果概览——在长程任务（a）上优于标准 LeWM，并带来更优的规划效率（b），二者对真实机器人实时模型控制都很重要。

模型组件（训练期）：
$$\mathbf{z}_t = \mathrm{enc}^O(o_t),\quad \tilde{\mathbf{a}}_t = \mathrm{enc}^A(a_t)$$
$$\text{Forward: } z_{t+1} = f(z_t, \tilde a_t);\quad \text{Backward: } z_t = f_\text{back}(z_{t+1}, \tilde a_t);\quad \text{Inverse: } \tilde a_t = f_\text{inv}(z_t, z_{t+1})$$

#### 各模块详细说明

**模块1：前向动力学（保留 LeWM）**
- **功能**：预测下一潜在状态 $z_{t+1}$
- **损失**：$\mathcal{L}_{\mathrm{forward}} = \mathcal{L}_{\mathrm{LeWM}} + \sum_{k=2}^{m} \| \hat z_{t+k} - z_{t+k} \|_2^2$（加入多步预测项，权重 $\lambda_\mathrm{multi}=1$）

**模块2：后向动力学（新增辅助）**
- **功能**：从「未来状态 + 动作」反推当前状态 $z_t$
- **损失**：$\mathcal{L}_{\mathrm{back}} = \mathcal{D}(f_{\text{back}}(z_{t+1},\tilde a_t), E^z(z_t))$，用 DCL 对比损失对齐表征

**模块3：逆动力学（新增辅助）**
- **功能**：从「状态对」推断动作 $\tilde a_t$
- **损失**：$\mathcal{L}_{\mathrm{inv}} = \mathcal{D}(f_{\text{inv}}(z_t,z_{t+1}), \tilde a_t)$

总目标：
$$\mathcal{L}_{\Sigma} = \mathcal{L}_{\mathrm{forward}} + \lambda_{\mathrm{back}}\mathcal{L}_{\mathrm{back}} + \lambda_{\mathrm{inv}}\mathcal{L}_{\mathrm{inv}}$$

### 方法架构图

![[fig6_ablation_page1.png|700]]

> 图2：Σ-LeWM 与标准 LeWM 及不同损失组合消融的对比（横轴为 base 模型）。

### 理论分析：Σ-信息（S-Information）

作者给出信息论统一视角：LeWM 的前向预测 + SIGReg 熵正则本质上是**最大化联合互信息** $\max I(Z_{t+1}; Z_t, A_t)$（预测 MSE 最小化条件熵 $H(Z_{t+1}|Z_t,A_t)$ 的上界，SIGReg 最大化边际熵 $H(Z_{t+1})$）。

Σ-LeWM 新增的后向/逆目标则最大化：
$$\mathcal{I}_{\mathrm{back}} = I(Z_t; Z_{t+1}, \widetilde{A}_t),\quad \mathcal{I}_{\mathrm{inv}} = I(\widetilde{A}_t; Z_t, Z_{t+1})$$

三者之和恰好等于**全相关（Total Correlation）与对偶全相关（Dual Total Correlation）之和**：
$$\mathcal{I}_{\mathrm{forward}} + \mathcal{I}_{\mathrm{back}} + \mathcal{I}_{\mathrm{inv}} = \mathrm{TC}(Z_t,\widetilde{A}_t,Z_{t+1}) + \mathrm{DTC}(Z_t,\widetilde{A}_t,Z_{t+1})$$

这正是「Σ」（求和）命名的由来——通过同时最大化三路互信息，Σ-LeWM 对潜在三元组施加了更强的统计结构。

## 实验结果

### 数据集与基线
- 世界模型标准基准 + 两个模拟机器人操作任务（Gear-Assembly 齿轮装配、Cube-Stacking 立方体堆叠）。
- 基线：标准 LeWM，以及不同损失组合的消融版本。

### 主要结果

- **长程任务**：Σ-LeWM 在长程任务上优于标准 LeWM。
- **规划效率**：在 CEM 阶梯采样下，达到同等性能所需的规划采样量显著减少（latency 下降）。
- **物理可解释性 / 规划可学习性**：通过线性探针（linear probe）验证 Σ-LeWM 潜在空间具备更好的物理可解释性与 planner learnability。

![[fig3_long_horizon_page1.png|700]]

> 图3：更长 horizon 下的性能对比。

![[fig5_frontier_page1.png|700]]

> 图4：规划效率前沿（任务难度 vs 采样预算）。

## 深度分析

### 研究价值评估

#### 理论贡献
- 将无解码器世界模型统一进「联合互信息最大化」的信息论框架，并引入 TC/DTC 分解给出简洁解释。
- 提出「Σ-信息」这一概念，把前向/后向/逆三路目标整合为可解释的整体。

#### 实际应用价值
- 轻量：辅助损失仅在训练期使用，推理期规划与 LeWM 完全一致，可无痛迁移。
- 对实时机器人模型控制友好：规划效率提升意味着更少的 MPC/CEM 采样即可达到同等控制质量。

### 方法优势详解

- **无推理期开销**：backward/inverse 只作训练正则，不增加部署复杂度。
- **理论自洽**：三路互信息之和 = TC + DTC，为设计提供了明确的信息论依据。
- **兼容性强**：可直接叠加在 LeWM 及其变体之上。

### 局限性分析

- 两个辅助损失的权重 $\lambda_{\mathrm{back}}, \lambda_{\mathrm{inv}}$ 需要额外调参（论文做了 loss-weight 消融）。
- 评测集中在标准基准与两个模拟操作任务，真实机器人上的验证尚待补充。
- 理论分析建立在高斯固定方差等假设上，严格性有限。

## 技术路线定位

本文属于**无解码器世界模型（JEPA/LeWM 路线）**，通过信息论目标扩展潜在空间结构，处于「表征学习 × 世界模型」的交叉点。

```
Dreamer(解码器重建) → JEPA/LeWM(无解码器+熵正则) → Σ-LeWM(三路互信息/TC+DTC) → 实时机器人控制
```

## 我的综合评价

### 价值评分

#### 总体评分
**8.5/10** - 思路清晰、理论自洽、工程轻量的世界模型改进，信息论视角有启发性。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | 两个辅助损失非全新，但 Σ-信息（TC+DTC）的统一视角有增量贡献 |
| 技术质量 | 8/10 | 公式严谨、理论推导自洽，DCL 训练实现清晰 |
| 实验充分性 | 7/10 | 标准基准 + 双任务消融较扎实，缺真实机器人验证 |
| 写作质量 | 8/10 | 结构清楚，信息论解释到位 |
| 实用性 | 8/10 | 无推理期开销、可直接叠加，实用价值高 |

> [!tip] 关键启示
> 世界模型潜在空间的结构可以「免费」地通过后向/逆信息目标增强——三者之和恰好对应 TC+DTC，为无解码器世界模型提供了简洁的理论脚手架。

> [!warning] 注意事项
> - 推理期与 LeWM 一致，但训练期新增两个网络，训练成本略增。
> - 辅助损失权重需按任务调参。
