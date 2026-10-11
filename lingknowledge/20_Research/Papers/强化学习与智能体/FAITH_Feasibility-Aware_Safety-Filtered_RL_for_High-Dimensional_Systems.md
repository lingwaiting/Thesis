---
date: "2026-10-11"
paper_id: "arXiv:2610.12432"
title: "FAITH: Feasibility-Aware Safety-Filtered RL for High-Dimensional Systems"
authors: "Songyuan Zhang, Baljeet Singh, Sarthak Ranjeet Kaingade, Chuchu Fan, Bryan Trinh"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习
  - Safe-RL
  - Safety-Filter
  - Humanoid-Robot
  - Robot-Learning
quality_score: "8.3/10"
created: "2026-10-11"
updated: "2026-10-11"
status: analyzed
---

# FAITH: Feasibility-Aware Safety-Filtered RL for High-Dimensional Systems

## 核心信息
- **论文ID**：arXiv:2610.12432
- **作者**：Songyuan Zhang, Baljeet Singh, Sarthak Ranjeet Kaingade, Chuchu Fan, Bryan Trinh
- **机构**：麻省理工学院（MIT）+ Amazon
- **发布时间**：2026-10-08
- **会议/期刊**：arXiv 预印本（cs.RO / cs.LG / eess.SY）
- **链接**：[arXiv](https://arxiv.org/abs/2610.12432) | [PDF](https://arxiv.org/pdf/2610.12432)
- **引用**：--

## 摘要翻译

### 英文摘要
Safe reinforcement learning commonly places safety and task performance in the same policy objective, where they can introduce competing updates. Safety filters separate them at action execution, but classical designs require an analytic safety function and dynamics model, and standard minimal-intervention filters are myopic to long-horizon task return because they minimize only instantaneous action deviation. Hard projections are also undefined when no safe action exists. We present FAITH, a feasibility-aware, model-free framework that approximates the optimal state-action safety value and amortizes minimal-intervention filtering with a feedforward network. The task policy optimizes the task return through the filtered dynamics, which recovers the feasible constrained problem without a competing safety term in the task-policy update. When no action satisfies the learned safety condition, the same filter approaches the action with minimum predicted peak harm. On a double integrator example and a Safety Gym environment, FAITH achieves the highest return among methods with no feasible-start violations and matches the lowest harm from infeasible starts. On a 29-DoF humanoid, it reaches a 99.95% safety rate while retaining 97% of the unfiltered return in Walking-Avoid, and obtains the highest measured safety rate in Push-Avoid by learning to sacrifice balancing and fall away from the protected region. The same policies are also demonstrated on a real-world Unitree G1 humanoid.

### 中文翻译
安全强化学习通常把安全与任务性能放在同一个策略目标中，二者可能产生相互竞争的更新。安全滤波在动作执行层面将二者分离，但经典设计需要解析的安全函数与动力学模型，而标准的"最小干预"滤波器因只最小化瞬时动作偏差，对长时域任务回报是短视的；当不存在安全动作时，硬投影也未被定义。我们提出 **FAITH**，一个可行性感知、无模型的框架，它近似最优的状态-动作安全价值，并用前馈网络将最小干预滤波"摊销"（amortize）。任务策略通过滤波后的动力学优化任务回报，从而在不引入竞争性安全项的情况下恢复可行约束问题。当没有动作满足学到的安全条件时，同一滤波器会逼近"预测峰值伤害最小"的动作。在双积分器和 Safety Gym 环境上，FAITH 在无可行起点违规的方法中取得最高回报，并匹配不可行起点下的最低伤害。在 29 自由度的双足人形机器人上，它在 Walking-Avoid 中达到 99.95% 的安全率、同时保留 97% 的未滤波回报；在 Push-Avoid 中学到"牺牲平衡、向远离保护区方向跌倒"，取得最高实测安全率。同样的策略也在真实 Unitree G1 人形机器人上得到演示。

### 核心要点提炼
- **研究背景**：足式/人形机器人在人类与设备附近部署时，需保证每个连杆都在保护区域之外；安全不仅依赖当前几何，还依赖未来动作能否最终避免违规。
- **研究动机**：把安全与回报放在同一策略目标中会导致竞争性更新；经典安全滤波需要解析模型且对长时域回报短视。
- **核心方法**：FAITH 用安全 critic + backup actor 近似最优状态-动作安全价值，用前馈残差网络摊销最小干预滤波，任务策略"透过滤波器"只用任务奖励训练。
- **主要结果**：29-DoF 人形机器人 99.95% 安全率且保留 97% 未滤波回报；Push-Avoid 中学到"跌倒远离保护区"取得最高安全率。
- **研究意义**：统一了"可行时避障 + 不可行时降害"两种情形于一个无模型滤波器，无需模式切换或独立恢复控制器。

## 研究背景与动机

### 领域现状
RL 已能训练足式与人形机器人的敏捷行为，但部署于人类/设备周围时，需保证每个连杆不进入保护区。接触丰富、欠驱动的系统中，安全不仅取决于当前几何，还取决于未来动作能否最终阻止违规；强扰动可能使违规不可避免，此时控制器应在安全可行时最大化回报、否则最小化伤害。

### 现有方法的局限性
- **目标内安全（penalty / primal-dual / primal）**：安全与任务信号作用于同一策略，回报提升与伤害降低可能方向相反，产生竞争更新；小权重放行违规、大权重过于保守，primal-dual 替换为不稳定的鞍点问题。
- **经典安全滤波**：需要解析安全函数与动力学模型，每步在线求解优化问题；学习型方法可去掉显式模型，但常保留在线动作采样/优化，高维动作空间下代价高。
- **最小干预滤波器短视**：只最小化瞬时动作偏差，不优化长时域回报；且无可行动作时硬投影未定义。

### 研究动机
将安全与回报分离到不同模块，同时用无模型方式逼近最优安全价值并摊销滤波，使任务策略能"透过滤波器"适应干预、且无需在目标中引入竞争性安全项。

## 研究问题

### 核心研究问题
如何在无解析模型的前提下，把"安全执行"与"任务回报优化"解耦，并统一处理"可行时最大化回报"与"不可行时最小化伤害"两种情形，且能扩展到高维（如 29-DoF 人形）系统？

## 方法概述

### 核心思想
用**安全 critic**（状态-动作峰值伤害价值 $Q_h$）+ **backup actor**（供给 Bellman 目标的低伤害动作）逼近最优安全价值；用**前馈滤波器**摊销最小干预投影；任务策略**透过滤波后的动力学**只用任务奖励训练。安全改变策略经历的动力学，但不进入其目标。

### 方法框架

#### 整体架构
FAITH 分离安全学习与任务回报优化：(a) 任务策略提出名义动作，安全滤波器产生执行动作；(b) 安全 critic、backup actor 与滤波器从回放中学习；(c) PPO 只用名义动作似然比 + 滤波轨迹回报更新任务策略。部署时仅需任务策略与滤波器的前向传播。

![[2610.12432_fig1.png|600]]

> 图1：FAITH 框架——安全学习与任务回报优化解耦。任务策略（蓝）、安全滤波器（紫）、安全 critic（红）、backup actor（绿）各自分工。

#### 关键组件说明

**组件1：安全 Q 函数（Safety Critic）**
- 定义最优安全 critic $Q_h^\star(s,a)=\min_\pi \sup_{k\ge0} h(s^k)$，满足 Bellman 方程 $Q_h^\star(s,a)=\max\{h(s), \min_{a'}Q_h^\star(f(s,a),a')\}$。
- 用神经网络 $Q_{h,\phi}$ 近似，$\min_{a'}$ 用 backup actor $\mu_\psi$ 近似，通过折扣安全 Bellman 目标训练。$Q_{h,\phi}(s,a)\le0$ 作为"学到的实际安全估计"（而非形式化证书）。

**组件2：前馈安全滤波器**
- 摊销投影：$a_{\mathrm{exec}}=F_\omega(s,a_{\mathrm{nom}})=\mathrm{clip}(a_{\mathrm{nom}}+\Delta_\omega(s,a_{\mathrm{nom}}))$。
- 损失 $\mathcal{L}_F=\mathbb{E}_\nu[\|a_{\mathrm{exec}}-a_{\mathrm{nom}}\|^2+\lambda[Q_{h,\phi}(s,a_{\mathrm{exec}})+\delta]_+]$，λ 随训练增大到 $\lambda_\max\gg1$。
- 当无动作满足安全条件时，hinge 处处激活，滤波器退化为最小化 $\|a-a_{\mathrm{nom}}\|^2+\lambda Q_{h,\phi}(s,a)$，即优先更低预测峰值伤害，**无需模式切换**；并给出 $\mathcal{O}(1/\lambda)$ 的逼近界。

**组件3：任务策略与训练回路**
- 滤波动力学 $\tilde f_\omega, \tilde r_\omega$ 定义后，任务策略在滤波系统上最大化回报 $J_\omega(\theta)$，用 PPO 更新（每次更新时固定 ω），任务奖励中不加任何安全项。
- 完美滤波器等价性引理：在 $\mathcal{F}$ 上，滤波系统的最优回报等于受约束问题的最优回报。

## 实验结果

### 实验目标
回答三个问题：(Q1) 可行起点下能保留多少回报且保持安全？(Q2) 伤害不可避免时能否最小化伤害？(Q3) 能否扩展到带全身约束的高维系统？

### 数据集与环境
- 一维双积分器（DI）、Safety Gym（SafetyCarCircle1-v0）、29-DoF Unitree G1 人形（Isaac Lab，Walking-Avoid 与 Push-Avoid 任务）。
- 基线：RCRL（primal-dual）、RC-CRPO（primal 变体）、PPO(β)（惩罚）、ROCBF(α)（降阶 CBF）。

### 主要结果

- **DI**：可行起点下 100% 安全率且为安全方法中最高回报；不可行起点下最低峰值伤害（最大制动力）。
- **Safety Gym**：所有起点可行时 100% 安全率 + 高回报，其余基线均有违规。
- **Walking-Avoid（29-DoF）**：FAITH 达 99.95% 安全率、回报 2365±345，匹配 ROCBF 最高安全率的同时保留约 97% 未滤波回报。
- **Push-Avoid**：FAITH 安全率 85.45%（最高），通过在强推下"牺牲平衡、向远离保护区跌倒"来降害，共同失败子集上峰值伤害 0.153（最低）。
- **硬件**：同一控制器在真实 Unitree G1 上演示了"轻推恢复平衡并避开箱子 / 强推跌倒远离"两种行为。

### 结果分析
消融显示：滤波动力学训练（w/o filtered dyn. 回报下降）、backup replay（w/o 安全率下降）、足够大的 $\lambda_\max$（太小则安全率骤降）均对最终性能关键；学到的滤波器逼近解析最优控制器，且比在线数值搜索快 18 倍。核心洞见是"安全与回报不竞争"——安全通过滤波器改变轨迹，而非在任务目标里与回报角力。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：把"安全滤波"从"解析 CBF + 在线求解"推进到"无模型 + 前馈摊销"，并用安全 critic 统一可行/不可行两种情形。
  - 创新点：同一滤波器损失在可行时逼近最小干预投影、不可行时逼近最小峰值伤害，无需模式开关或恢复控制器。
- **贡献2**：任务策略"透过滤波器"训练，从原理上消除目标内安全与回报的竞争。
  - 创新点：给出完美滤波器下等价性引理，说明滤波系统的最优解恢复受约束问题的最优解。

#### 实际应用价值
- **应用场景1**：人形/足式机器人在人类周边作业的安全控制——统一避障与"保护性跌倒"降害。
- **应用场景2**：任何可定义伤害函数的高维安全控制问题（机械臂、自动驾驶等），无需显式动力学模型。

### 局限性
- 折扣、有限 λ 与学习误差使学到的控制器**无形式化安全/最优性保证**（论文明确声明）。
- 评估限于平面保护区域，未纳入撞击严重程度与人体伤害量化。
- 硬件演示为定性，缺安全/伤害统计，且未量化 LiDAR 噪声、控制延迟、通信延迟的影响。

### 未来工作
- 提供有限样本下的安全/最优性形式化保证。
- 扩展到非平面、动态障碍与更丰富的人机交互场景。
- 量化感知噪声与控制延迟对安全率的影响，提升鲁棒性。

### 与相关工作对比
- **vs RCRL / RC-CRPO / PPO(β)**：这些方法在目标内强制安全，存在竞争更新；FAITH 在动作层解耦，避免鞍点/权重调参。
- **vs ROCBF**：ROCBF 需降阶解析模型、且忽略加速度极限产生次优轨迹；FAITH 无模型且可扩展到全身约束。
- **研究路线归属**：属于"可行性感知安全 RL"与"安全滤波"的交汇，补充了"训练任务策略透过滤波器"这一在 legged/humanoid 场景的规模化验证。
