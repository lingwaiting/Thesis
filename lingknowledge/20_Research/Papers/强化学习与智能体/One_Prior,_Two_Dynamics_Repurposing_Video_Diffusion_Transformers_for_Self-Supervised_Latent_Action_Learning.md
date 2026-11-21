---
date: "2026-11-21"
paper_id: "arXiv:2611.26617"
title: "One Prior, Two Dynamics: Repurposing Video Diffusion Transformers for Self-Supervised Latent Action Learning"
authors: "Yuxin Jiang, Wenqiang Sun, Jiaqi Li, Yuchao Gu, Lvmin Zhang, Ivor Tsang, Mike Zheng Shou"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Latent-Action
  - Video-Diffusion
  - World-Model
quality_score: "8.5/10"
created: "2026-11-21"
updated: "2026-11-21"
status: analyzed
---

# One Prior, Two Dynamics: Repurposing Video Diffusion Transformers for Self-Supervised Latent Action Learning

## 核心信息

- **论文 ID**：arXiv:2611.26617
- **作者**：Yuxin Jiang, Wenqiang Sun, Jiaqi Li, Yuchao Gu, Lvmin Zhang, Ivor Tsang, Mike Zheng Shou
- **机构**：Show Lab（新加坡国立大学 NUS）、A\*STAR（新加坡）、腾讯、斯坦福大学
- **发布时间**：2026-11-19
- **领域**：强化学习与智能体 / 世界模型 / 潜在动作学习
- **分类**：cs.CV, cs.AI, cs.LG, cs.RO
- **链接**：[arXiv](https://arxiv.org/abs/2611.26617) | [PDF](https://arxiv.org/pdf/2611.26617) | [项目主页](https://showlab.github.io/o2laf)

## 摘要翻译

### 英文原文

> Videos reveal how the world changes, but rarely the actions that cause those changes. Latent action learning recovers such supervision from visual transitions, yet often learns the underlying dynamics largely from scratch. We ask whether a pretrained video generator can instead provide the prior for both inverse and forward dynamics. We introduce O²LaF, built on a simple principle: one prior, two dynamics. We initialize both models from the same pretrained video DiT and adapt them to their distinct roles: a feed-forward transition encoder with learnable action queries for inverse dynamics, and a causal, action-conditioned video generator for forward dynamics. By controlling information access and optimizing both dynamics end-to-end through a compact bottleneck, O²LaF learns transferable actions from unlabeled videos. Across navigation and robotic manipulation, O²LaF sets a new state of the art in few-shot action probing and enables stronger zero-shot action transfer across visual contexts and embodiments. Beyond generative video priors, we further extend our framework to predictive representation priors such as V-JEPA2, achieving competitive performance.

### 中文翻译

视频揭示了世界如何变化，却很少揭示导致这些变化的动作。潜在动作学习（latent action learning）试图从视觉转变中恢复这种监督信号，但现有方法往往从零开始学习底层的动力学。我们提出这样一个问题：预训练的视频生成器能否同时为逆动力学和正动力学提供先验？我们提出了 **O²LaF**，其构建在一个简单的原则上：**一个先验、两个动力学**（one prior, two dynamics）。我们让两个模型都从同一个预训练视频 DiT 初始化，并将它们适配到各自不同的角色：一个带可学习动作查询的前馈转移编码器负责逆动力学，一个因果的、以动作条件生成的视频生成器负责正动力学。通过控制信息访问、并经由一个紧凑的瓶颈端到端地联合优化两种动力学，O²LaF 能从无标签视频中学习可迁移的动作。在导航与机器人操作两个任务上，O²LaF 在少样本动作探测上刷新了 SOTA，并在跨视觉上下文和跨具身（embodiment）上实现了更强的零样本动作迁移。除了生成式视频先验之外，我们进一步将框架扩展到预测式表示先验（如 V-JEPA2），同样取得了有竞争力的表现。我们的发现揭示了一个更广泛的潜在动作学习范式：复用预训练视频模型来发现可迁移的动作并预测其后果。

### 核心要点提炼

1. **核心思想**：同一个预训练视频 DiT 被"一分为二"复用——既做逆动力学模型（IDM）推断动作，又做正动力学模型（FDM）预测结果，即 "one prior, two dynamics"。
2. **关键机制**：通过控制两个模型各自可访问的信息（信息访问控制），并让动作经过一个紧凑的连续瓶颈（32 维高斯潜在动作）端到端传播梯度，迫使学到的潜在动作只编码"导致变化的关键信息"。
3. **主要成果**：在少样本动作探测上取得 SOTA；零样本动作迁移跨越第一/第三人称视角与人类-机器人具身；并扩展至 V-JEPA2 等预测式先验同样有效。

## 研究背景与动机

视频是互联网上最丰富的"世界如何变化"的观测数据，但视频几乎从不标注导致这些变化的物理动作。这种监督缺失，限制了我们从海量视频中学习**可控世界模型**（world models）与**具身智能体**（embodied agents）的能力。

**潜在动作学习**（latent action learning）提供了一条出路：它通过一个逆动力学模型（IDM）从观测转移 $x_t \rightarrow x_{t+1}$ 推断潜在动作 $z_t$，再通过一个正动力学模型（FDM）预测其结果 $\hat{x}_{t+1} = \mathrm{FDM}(x_t, z_t)$。一个紧凑的瓶颈限制了 $z_t$ 能编码的内容，从而促使它只保留"解释视觉变化所必需"的信息。

然而，现有方法在两个方向上都存在缺陷：

- **逆动力学侧**：IDM 往往从零开始学习（无论是在原始像素上还是在冻结的预训练特征之上），只能依赖 FDM 的重建监督来学习"从转移推断动作"的能力。这类专用潜在动作模型甚至被通用视觉基础模型超越。
- **正动力学侧**：近期方法用预训练视频生成器作 FDM，但保留了标准的扩散训练配方，导致生成器可以学会"无视潜在动作"（因为初始帧条件已提供了足够外观信息），从而没有充分利用先验作为学习信号。

作者的关键洞察是：**现代视频 DiT 在海量数据上预训练，已经学会了"视觉状态如何随时间演化"的时空结构**，这恰恰是逆动力学和正动力学共同需要的东西。因此问题变成：**能否让一个预训练视频 DiT 同时充当逆动力学和正动力学？**

> 图1（teaser）：O²LaF 的整体愿景——从无标签视频中，用同一个预训练视频 DiT 同时学习"从转移推断动作"与"根据动作预测未来"。

![[teaser_v21_page1.png|600]]

> 图2（动机图）：对冻结视频 DiT 在 $t=0$ 输入干净视频并加文本"视频里的动作是什么？"，可视化交叉注意力。响应会跟随正在活动的手和被操纵区域，随交互变化而移动。这说明预训练视频 DiT 已经具备动作/转移敏感的时空计算，值得被复用。

![[motivation_attn_f2_page1.png|600]]

> 图3（范式对比）：现有潜在动作学习方法在"先验从何处进入逆-正动力学流水线"上的差异。O²LaF 是首个让同一个预训练视频先验同时支撑 IDM 与 FDM 的方法。

![[paradigm_comp_page1.png|600]]

## 研究问题

1. 预训练视频 DiT 能否被复用为**逆动力学**（从视觉转移推断动作）和**正动力学**（根据动作预测视觉后果）的共享先验？
2. 如何**控制信息访问**，避免"强的 FDM 绕过动作"这一根本问题（即 FDM 仅凭初始帧或噪声目标就能预测未来，从而忽略潜在动作）？
3. 学到的潜在动作能否在**少样本**下高效地对齐真实控制，并在**零样本**下跨视觉上下文与跨具身迁移？

## 方法概述

### 核心思想：One Prior, Two Dynamics

O²LaF 的前提是：大规模视频预训练已经捕获了视觉状态如何演化，这既为逆动力学所需，也为正动力学所需。因此，作者**用同一个预训练视频 DiT 初始化两个模型，但通过控制各自可访问的信息，将它们适配到不同角色**：

$$
z_i \sim q_\phi\left(z_i \mid \widetilde{\mathbf{x}}^{\mathrm{IDM}}_{\le i+1},\, t_{\mathrm{IDM}}\right), \qquad
\hat{\mathbf v} = f_\theta\left(\widetilde{\mathbf{x}}^{\mathrm{FDM}},\, t_{\mathrm{FDM}},\, \mathbf c_0,\, \overline{\mathbf z}\right)
$$

其中 IDM $q_\phi$ 从观测视频推断潜在动作，FDM $f_\theta$ 预测其视觉后果；$\mathbf c_0=\text{"a video"}$ 是固定中性文本条件，$\overline{\mathbf z}=[0,z_0,\ldots,z_{K-1}]$ 将每个 $z_i$ 与 $x_{i+1}$ 的预测对齐。两个动力学都由同一个预训练视频 DiT 初始化（Wan2.2-TI2V-5B），分别用 LoRA（rank-128）独立适配，新增的动作读出/条件模块从零训练。

### 方法框架

![[method11_page1.png|600]]

> 图4（方法架构图，核心）：上分支为逆动力学 IDM——前馈复用视频 DiT，带可学习动作查询 + 非对称注意力 + 变分动作瓶颈；下分支为正动力学 FDM——因果联合预测，初始帧干净、未来帧加噪，动作经 AdaLN 注入；两者通过采样的潜在动作端到端联合优化。

### 模块说明

**（1）视频 DiT 作为逆动力学（IDM）**

- **前馈复用**：逆动力学操作在观测视频上，无需迭代去噪。因此 IDM 只做单次前向传播（flow timestep $t_{\mathrm{IDM}}$）。输入构造为 $\widetilde{\mathbf{x}}^{\mathrm{IDM}}=(1-t_{\mathrm{IDM}})\mathbf{x}+t_{\mathrm{IDM}}\boldsymbol\epsilon$。当 $t_{\mathrm{IDM}}=0$ 时模型接收干净视频潜在，增大 timestep 则逐步抹除视觉信息，从而提供一族"噪声相关"的表示供动作推断。
- **动作查询（learnable action queries）**：引入可学习的动作查询，一个共享的查询嵌入按转移复制，用时间位置编码区分每个 $q_i$（对应 $x_i\rightarrow x_{i+1}$）。采用**非对称注意力掩码**：(i) 视频 token 跨帧因果注意力；(ii) 每个动作查询 $q_i$ 只读取两个端点状态 $(x_i, x_{i+1})$；(iii) 动作查询之间互不关注，视频 token 也不关注查询。这把每个查询变成一个"只读的转移寄存器"，而非辅助通信通道。
- **变分动作瓶颈**：动作查询的最终表示 $h_i$ 经线性高斯头 $g_\phi$ 得到紧凑潜在动作：$[\mu_i,\log\sigma_i^2]=g_\phi(\mathrm{LN}(h_i))$，$q_\phi(z_i\mid\cdot)=\mathcal N(\mu_i,\mathrm{diag}(\sigma_i^2))$。训练时用重参数化采样 $z_i=\mu_i+\sigma_i\odot\xi_i$，推理用后验均值 $\mu_i$。连续潜在空间避免了量化，能表示视觉动力学的细粒度变化（$d_z=32 \ll D$）。

**（2）视频 DiT 作为正动力学（FDM）**

- **初始状态与动作条件**：FDM 接收干净的初始状态 $x_0$ 与时间对齐的潜在动作序列 $\overline{\mathbf z}$。初始状态提供外观与空间结构（置于瓶颈之外），促使潜在动作只编码可迁移的动力学而非场景外观。动作经投影后与 flow-timestep 嵌入结合，通过 AdaLN 注入每个 Transformer block。
- **因果联合 flow matching**：保持 $x_0$ 干净、对全部未来状态用共享 timestep $t_{\mathrm{FDM}}$ 加噪：$\widetilde{\mathbf{x}}^{\mathrm{FDM}}=[x_0, x^{t_{\mathrm{FDM}}}_1,\ldots,x^{t_{\mathrm{FDM}}}_K]$。所有未来状态的速度在单次前向中**联合**预测。为防止信息泄漏，用**帧因果注意力**替代原双向时间注意力，使 $x_i$ 无法关注 $x_{>i}$。这去除了"干净历史捷径"（否则自回归只需复制上一帧即可，绕过动作瓶颈）。
- **高噪声 timestep 采样**：因果注意力防住了未来状态泄漏，但每次预测仍能看到自己的含噪目标。常见生成配方（如 SD3 的 logit-normal 采样）偏向中等噪声，对潜在动作学习不利——含噪未来仍保留足够信息让 FDM 不完全依赖动作。作者因此**强调高噪声** $t_{\mathrm{FDM}}$。在端点 $t_{\mathrm{FDM}}=1$ 处，flow matching 等价于"以干净初始状态与潜在动作为条件重建未来潜在"：$\|\hat v_i-v_i^\star\|_2^2=\|\hat x_i-x_i\|_2^2$。

**（3）端到端潜在动作学习**

- **角色特定的噪声采样**：噪声在两种动力学中扮演不同角色。IDM 的噪声控制转移推断可用的视觉信息，FDM 的噪声控制"通过含噪未来仍可访问多少目标信息"。因此独立采样两个 flow timestep：$t_{\mathrm{IDM}}\sim\pi_{\mathrm{IDM}}, t_{\mathrm{FDM}}\sim\pi_{\mathrm{FDM}}$。主实验采用域无关配置 $\pi_{\mathrm{IDM}}=\delta_0$（干净输入）与 $\pi_{\mathrm{FDM}}=0.7\pi_{\mathrm{base}}+0.3\pi_{\mathrm{high}}$（$\operatorname{supp}(\pi_{\mathrm{high}})\subseteq[0.9,1)$）。
- **学习目标**：flow-matching 预测损失 + 潜在动作后验的 KL 正则：

$$
\mathcal L = \mathbb E\left[\frac{1}{K}\sum_{i=1}^{K} w(t_{\mathrm{FDM}})\|\hat v_i-(\epsilon_i-x_i)\|_2^2\right] + \frac{\beta}{K}\sum_{i=0}^{K-1} D_{\mathrm{KL}}[q_\phi(z_i\mid\cdot)\Vert\mathcal N(0,I)]
$$

  通过重参数化，FDM 的梯度经潜在动作回传到 IDM。预测损失引导 IDM 编码"解释未来视觉变化所必需"的转移信息，而低维潜在空间与 KL 正则约束动作通道携带的信息量（$\beta=10^{-4}$）。

- **推理与动作迁移**：FDM 在训练期塑造潜在动作空间，在推理期则作为动作条件的视频世界模型。给定初始状态与推断的潜在动作，从高斯噪声初始化未来潜在，用 Euler 求解器从 $t=1$ 积分到 $t=0$。从源视频推断的动作可条件于不同的目标首帧生成，实现零样本跨上下文动作迁移。

## 实验结果

### 数据集

| 领域 | 训练数据 | 评估数据 | 说明 |
| --- | --- | --- | --- |
| 相机导航 | MiraData | MIND | 全局场景与相机运动 |
| 机器人操作 | EgoDex（人类自我中心演示） | GR-1 机器人演示（DreamDojo In-lab Eval） | 细粒度手-物体交互 |

> 关键点：训练全程**不使用任何动作标注**；O²LaF 仅用了 DreamDojo 大规模自我中心训练数据中的 EgoDex 子集。

### 基线

- 导航：AdaWorld、Olaf-World（均在相同 MiraData 视频上训练）
- 操作：DreamDojo、VideoWorld2、LAPA-DINOv3、VILLA-X（使用其发布模型）
- 表示质量用冻结编码器 + 统一探测协议评估；动作条件生成用受控的二阶段世界模型对比评估。

### 实现细节

- IDM 与 FDM 均从 Wan2.2-TI2V-5B 初始化，rank-128 LoRA 适配。
- 32 维高斯潜在动作，$\beta=10^{-4}$，冻结视频 VAE 与文本编码器。
- 公平起见，为每个冻结的潜在动作编码器单独训练一个双向 FDM（同一 Wan2.2-TI2V-5B 检查点、相同训练与生成协议），以隔离潜在动作表示的影响。

### 指标

- **导航**：预测 4 个同时的三元控制（2 平移轴 + 2 旋转轴），独立、类别平衡的逻辑回归探测，报告 4 项全部正确的**联合精确匹配准确率**。
- **操作**：对双腕平移与旋转拟合 Ridge 回归探测，报告 EEF $R^2$（平移、旋转、双臂平均）。
- **迁移**：ViPE 估计轨迹的 Sim(3) 对齐平移 NRMSE 与旋转 RPE（相机保真度），VBench 评估视觉质量；机器人用 PSNR/SSIM/LPIPS 评估自重建与跨具身循环一致性。

### 主结果

**（1）少样本动作探测（few-shot action probing）**

O²LaF 在各领域、各标签预算下一致提升线性可解码性。性能随前几个标注样本急剧上升，说明控制相关信息以极小监督即可获得。特别是，O²LaF 在 GR-1 上达到 **EEF $R^2=0.277$**，对比 DreamDojo 的 $0.149$——而它的编码器**只在人类演示上训练、无机器人域适配**，说明学到的潜在动作捕获了跨人-机具身可迁移的运动结构。

**（2）零样本跨上下文动作迁移（相机导航）**

从 24 条参考轨迹（12 第一人称 + 12 第三人称）提取动作，迁移到 50 与 20 个新视觉上下文，共 840 次跨上下文生成。O²LaF 在两个视角上都取得最低相机运动误差与最高成像质量（下表摘要，最佳加粗）：

| 方法 | 1st-P 平移↓ | 1st-P 旋转↓ | 1st-P 图像质量↑ | 3rd-P 平移↓ | 3rd-P 旋转↓ | 3rd-P 图像质量↑ |
| --- | --- | --- | --- | --- | --- | --- |
| AdaWorld | 2.539 | 2.055 | 0.639 | 1.342 | 1.217 | 0.616 |
| Olaf-World | 1.627 | 1.300 | 0.690 | 0.985 | 0.701 | 0.648 |
| **O²LaF** | **1.289** | **0.786** | **0.698** | **0.511** | **0.330** | **0.682** |

有趣的发现是：**潜在动作 CFG 一致改善相机运动保真度与成像质量**（类似文本 CFG 的效果），这与"CFG 对预定义动作控制效果差"的既有结论相反。作者假设学到的潜在动作形成了有意义的连续运动流形，使 CFG 能放大运动相关的条件、提升样本保真度。

![[main_viz_mind_f_page1.png|600]]

> 图5（MIND 导航可视化）：第一人称导航中 O²LaF 复现了参考相机的渐进向上重定向，而基线运动有限或视角变化不准确；第三人称中 O²LaF 捕获了智能体的右转与倾斜姿态及相应背景运动，基线基本无法复现转向。

**（3）零样本跨具身动作迁移（机器人操作）**

用 85 对视频（20 个操作任务）评估人→机器人 与 机器人→人 的动作迁移。O²LaF 在所有自重建与跨循环指标上全面超越 DreamDojo（下表摘要）：

| 方法 | 人 自重建 PSNR↑ | 人 跨循环 PSNR↑ | 机器人 自重建 PSNR↑ | 机器人 跨循环 PSNR↑ |
| --- | --- | --- | --- | --- |
| DreamDojo | 23.38 | 22.61 | 18.73 | 18.35 |
| **O²LaF** | **26.72** | **24.30** | **19.92** | **18.85** |

定性上，人的伸手动作诱导了相应的机器人臂运动，机器人动作也能引出人的伸手与目标导向交互。O²LaF 在巨大具身差异下仍保留了臂特定的运动（活动臂、伸手方向）。

![[main_viz_robot_f_page1.png|600]]

> 图6（机器人操作可视化）：人类伸手动作诱导对应机器人臂运动，O²LaF 在跨具身下保留活动臂与伸手方向，运动对应更忠实同时保留目标视觉上下文。

### 消融与分析

**（1）适配预训练动力学（IDM adaptation / FDM scaling）**

| IDM 变体 | 1st-P k=1 | 1st-P k=50 | 3rd-P k=1 | 3rd-P k=50 |
| --- | --- | --- | --- | --- |
| Head-only（冻结 DiT） | 43.88 | 39.33 | 22.15 | 44.27 |
| LoRA-16 | 70.27 | 84.73 | 55.12 | 93.92 |
| **LoRA-128** | **72.81** | **87.95** | 52.08 | **94.37** |

- 冻结视频 DiT + 可训练动作头性能远差于 LoRA 适配；rank-16 LoRA 已逼近 rank-128。
- FDM 从 1.3B 扩到 14B（固定 IDM）一致提升动作可解码性 → 有效的潜在动作学习既需要适配预训练表示，也需要更强的预测监督。

**（2）控制信息暴露（temporal factorization / noise levels）**

| FDM 分解 | 1st-P k=1 | 1st-P k=50 | 3rd-P k=1 | 3rd-P k=50 |
| --- | --- | --- | --- | --- |
| Bidir. joint | 72.90 | 83.71 | 45.54 | 92.36 |
| **Causal joint** | 72.81 | **87.95** | **52.08** | **94.37** |
| AR (h=1) | 55.45 | 70.13 | 45.79 | 79.49 |

- **因果联合预测**显著优于自回归，去除干净历史捷径后动作可解码性更强。
- 5×5 的 $(t_{\mathrm{IDM}},t_{\mathrm{FDM}})$ 扫描揭示三点：① 中等 IDM 噪声常优于干净输入（偏向高层动作相关特征）；② 低 FDM 噪声表示差（目标部分可见造成捷径），但最大噪声也非总是最优（靠近噪声先验时速度目标方差大）；③ 最优噪声配置随领域变化——导航两侧偏好 $(0.3, 0.98)$，操作在不同臂上最优值不同。

**（3）扩展到其他视频先验（IDM backbones）**

用统一 FDM、仅替换 IDM 骨干，比较四种学习范式：ST-Transformer（从零训练）、VideoChat3（语言对齐视频 MLLM）、V-JEPA2（自监督预测视频编码器）、Wan2.2（生成式视频 DiT）。

| IDM 骨干 | MIND 1st-P↑ | MIND 3rd-P↑ | Color $R^2$（L/a/b，越低越好） |
| --- | --- | --- | --- |
| ST-Transformer | 58.79 | 79.70 | -0.419 / -0.190 / -0.530 |
| VideoChat3 | 36.96 | 43.86 | 0.411 / -0.147 / 0.364 |
| **V-JEPA2** | **88.62** | **96.14** | 0.514 / 0.816 / 0.742 |
| **Wan2.2** | 87.95 | 94.37 | **-0.297 / 0.081 / 0.223** |

- **V-JEPA2 动作探测最强，但其潜在动作在跨上下文生成中出现明显的源外观泄漏**（Color $R^2$ 显著为正）；Wan2.2 则更好保留目标场景。
- 结论：IDM 与 FDM **共享生成式视频先验**提升了表示对齐，利于可迁移的生成控制。**动作探测强 ≠ 下游生成控制好**，凸显了超越探测、用下游应用验证潜在动作的重要性。

![[main_idm_prior_viz_vjepa_page1.png|600]]

> 图7（V-JEPA2 prior 可视化）：V-JEPA2 作为 IDM 骨干时，其潜在动作在跨上下文生成中泄漏了源视频的外观（源外观被"搬"到目标场景），而 Wan2.2 更好地保留了目标场景。

**（4）平衡动作学习与生成（learning vs. deployment）**

端到端学习因果 FDM 中，混合噪声采样（vs. 仅高噪声）显著提升生成质量而保持相当探测性能；限制 IDM 梯度到高噪声 timestep 无一致探测收益。由于二者存在内在张力，作者采用**两阶段部署**：冻结学好的 IDM、训练双向 FDM，显著提升生成质量；并且从联合训练的 FDM 初始化（而非原始预训练 DiT）进一步同时改善视觉质量与动作跟随保真度。

## 深度分析

### 研究价值

1. **范式级贡献**：首次提出"复用预训练视频生成器作为逆动力学与正动力学的共享先验"这一思路，并证明其可迁移到 V-JEPA2 等预测式表示先验，暗示一个更广泛的潜在动作学习范式——"复用预训练视频模型来发现可迁移动作并预测其后果"。
2. **深刻的机制洞察**：系统回答了"先验从何处进入 IDM/FDM 流水线"这一被忽视的结构性问题，并通过信息访问控制（帧因果注意力 + 高噪声采样 + 初始帧干净 + 紧凑瓶颈）正面解决"强 FDM 绕过动作"这一核心难点。
3. **实践价值**：零样本跨视角、跨人-机具身动作迁移 + 可复用的动作条件世界模型初始化，对具身智能与通用世界模型有直接价值。

### 方法优势

- **信息访问控制是灵魂**：非对称注意力让动作查询变成"只读转移寄存器"；帧因果联合预测去除了干净历史捷径；高噪声采样抑制了含噪目标泄漏；初始帧置于瓶颈之外把外观从动作中剥离。这些设计环环相扣，共同保证瓶颈里装的是"真正导致变化的动力学信息"。
- **端到端 + 紧凑连续瓶颈**：连续高斯动作（非 VQ）保留细粒度变化；32 维极紧凑 + KL 正则约束信息量；重参数化让 FDM 梯度直通 IDM。
- **域无关的单一噪声配置**：主实验用一个统一配置跨导航与操作，工程上简洁可复现。

### 局限性

- **可解释性弱**：学到的潜在动作空间语义不透明，作者也将其列为未来工作。
- **学习-生成的内在张力**：学习有信息量的动作要求限制视觉捷径，而高保真生成要求更广的噪声覆盖，二者无法在端到端内同时最优，只能靠两阶段部署折中。
- **未做闭环规划/控制**：视频预测动力学尚未扩展到高效规划与闭环控制（作者列为未来方向）。
- **计算成本**：两阶段部署需要额外训练一个双向 FDM，推理为迭代 flow 积分，成本不低。

## 与相关论文对比

| 方法 | 先验位置 | 差异 |
| --- | --- | --- |
| AdaWorld | 无预训练先验，从零学习 | 像素级重建易产生捷径，可解码性弱 |
| Olaf-World | IDM 从零 + 外部视频表示对齐 | 仍从零学 IDM，O²LaF 直接用 DiT 先验 |
| DreamDojo | 强 FDM + 从零动作编码器 | 需大规模自我中心数据，人-机迁移弱 |
| VideoWorld2 / Coevolving | 预训练视频生成器作 FDM | 保留标准扩散配方，生成器可"无视"动作 |
| V-JEPA2 类（表示先验） | 纯预测式表示 | 探测强但外观泄漏，共享生成先验更利于迁移 |

O²LaF 的独特之处在于：**同一个生成式先验同时支撑两个动力学**，并通过"控制信息访问"让先验真正作为学习信号被利用，而非仅仅作为生成骨干被"架上去"。

## 我的综合评价

**总体评分：8.5/10**

这是一篇立意清晰、机制扎实、实验充分的工作。"one prior, two dynamics" 这一口号式原则背后，是信息访问控制这一系列环环相扣的设计，直击潜在动作学习长期存在的"FDM 绕过动作"痛点。少样本探测 SOTA、零样本跨视角/跨具身迁移、以及扩展到 V-JEPA2 的泛化性实验，共同支撑了"预训练视频模型可作为可复用动力学先验"的范式主张。扣分点在于：潜在动作空间可解释性弱、学习与生成的内在张力只能靠两阶段折中、且尚未打通闭环控制。整体上这是一篇值得精读的潜在动作学习/世界模型方向代表作。

### 分项评分

| 维度 | 评分 | 说明 |
| --- | --- | --- |
| 创新性 | 9/10 | "一先验两动力学 + 信息访问控制"范式新颖，洞察深刻 |
| 技术质量 | 8.5/10 | 设计精巧（非对称注意力、帧因果、高噪声采样） |
| 实验充分性 | 9/10 | 双领域 + 探测 + 迁移 + 5×5 噪声扫描 + 跨先验消融 |
| 写作质量 | 8/10 | 逻辑清晰，公式规范 |
| 实用性 | 8/10 | 可复用世界模型初始化，但闭环控制尚未打通 |

## 相关论文

- [[AdaWorld|AdaWorld: 潜在动作与世界模型]]（导航基线）
- [[Olaf-World|Olaf-World: 对齐外部表示提升动作迁移性]]
- [[DreamDojo|DreamDojo: 强 FDM 与大规模自我中心数据]]
- [[VideoWorld2|VideoWorld2: 预训练视频生成器作 FDM]]
- [[Genie|Genie: 从视频生成可玩世界]]
- [[LAPA|LAPA: 从零学习潜在动作]]
- [[V-JEPA2|V-JEPA2: 自监督预测视频编码器]]
- [[Wan2.2|Wan2.2: 文本到视频 DiT]]
