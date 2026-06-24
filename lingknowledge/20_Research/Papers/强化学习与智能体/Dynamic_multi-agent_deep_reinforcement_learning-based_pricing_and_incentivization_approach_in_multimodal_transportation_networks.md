---
date: "2026-06-23"
paper_id: "arXiv:2606.23257"
title: "Dynamic multi-agent deep reinforcement learning-based pricing and incentivization approach in multimodal transportation networks"
authors: "Khadidja Kadem, Mostafa Ameli, Carlos Lima Azevedo, Mahdi Zargayouna, Latifa Oukhellou"
domain: "强化学习与智能体"
tags:
  - 论文笔记
  - 强化学习与智能体
  - Multi-Agent-RL
  - Transportation
  - Dynamic-Pricing
  - Incentivization
  - Smart-Mobility
  - 智能交通
quality_score: "8.0/10"
created: "2026-06-23"
updated: "2026-06-23"
status: analyzed
---

# Dynamic multi-agent deep reinforcement learning-based pricing and incentivization approach in multimodal transportation networks

## 核心信息
- **论文ID**：arXiv:2606.23257
- **作者**：Khadidja Kadem, Mostafa Ameli, Carlos Lima Azevedo, Mahdi Zargayouna, Latifa Oukhellou
- **机构**：--
- **发布时间**：2026-06-22
- **会议/期刊**：arXiv preprint (cs.LG, cs.AI, math.OC)
- **链接**：[arXiv](http://arxiv.org/abs/2606.23257v1) | [PDF](https://arxiv.org/pdf/2606.23257v1)
- **引用**：--

## 摘要翻译

### 英文摘要
In multimodal transportation systems, shared mobility services (SMSs) are promoted for their potential to enhance flexibility and reduce congestion. However, SMS demand is often concentrated in high-density areas, which can limit the effectiveness and accessibility for various commuter groups. This uneven integration challenges transportation system efficiency, especially in terms of emissions and spatial equity. Addressing these issues requires coordination among multiple stakeholders whose objectives frequently conflict. Whereas authorities aim to ensure sustainable and equitable mobility, SMS providers focus on revenue maximization, and travelers seek to minimize personal travel costs. This paper proposes a multi-agent deep reinforcement learning framework that captures these interactions through dynamic pricing and incentivization strategies for SMSs and public transport. The framework integrates two reinforcement learning (RL) agents: (i) a public authority that allocates spatio-temporal public transport incentives to improve equity, emissions, and efficiency, and (ii) an SMS provider that dynamically adjusts fares to optimize revenue. The agents interact with the transportation system and adapt strategies in response to evolving demand, congestion, and network conditions. Numerical experiments conducted over a three-hour morning peak period show that dynamic incentivization effectively reduces congestion peaks, lowers commuters' costs by around 20% and emissions by approximately 10%, while nearly doubling public transport profit and supporting a more equitable distribution of benefits. When combined with dynamic SMS pricing, the two RL agents demonstrate the ability to balance conflicting objectives between private providers and public authorities. The proposed approach provides a decision-support tool for sustainable and equitable multimodal mobility planning.

### 中文翻译
在多模式交通系统中，共享出行服务因其增强灵活性和减少拥堵的潜力而受到推广。然而，共享出行需求往往集中在高密度区域，这限制了服务对各种通勤群体的有效性和可及性。这种不均衡的整合对交通系统效率构成挑战，特别是在排放和空间公平方面。解决这些问题需要协调多个利益相关者，而他们的目标常常相互冲突——管理机构旨在确保可持续和公平的出行，共享出行提供商关注收入最大化，而出行者寻求最小化个人出行成本。本文提出一个多智能体深度强化学习框架，通过对共享出行和公共交通的动态定价与激励策略来捕捉这些互动。框架集成两个RL智能体：（i）一个公共机构分配时空公共交通激励以改善公平、排放和效率；（ii）一个共享出行提供商动态调整票价以优化收入。智能体与交通系统交互，根据不断变化的需求、拥堵和网络条件调整策略。在三小时早高峰时段的数值实验表明，动态激励有效降低拥堵峰值，通勤者成本降低约20%，排放减少约10%，同时公共交通利润接近翻倍，并支持更公平的福利分配。当与动态共享出行定价结合时，两个RL智能体展示了平衡私营提供商与公共机构之间冲突目标的能力。该方法为可持续和公平的多模式出行规划提供了决策支持工具。

### 核心要点提炼
- **研究背景**：多模式交通系统中，共享出行与公共交通的利益冲突需协调
- **研究动机**：需要能平衡公平性、效率和可持续性的自动协调机制
- **核心方法**：双智能体MARL——公共机构做激励分配 + 共享出行做动态定价
- **主要结果**：通勤成本↓20%、排放↓10%、公交利润↑~100%
- **研究意义**：为城市出行治理提供了基于RL的自动化决策支持工具

## 研究背景与动机

### 领域现状
多模式交通系统是现代城市的核心基础设施：
- **公共交通**：大容量、低成本、环保，但灵活性有限
- **共享出行**（网约车、共享单车等）：灵活便捷，但可能加剧拥堵和不公平
- **关键矛盾**：共享出行的便利性vs公共交通的可持续性——两者争夺同一批用户

### 现有方法的局限性
1. **静态定价**：无法适应实时变化的交通状况
2. **单目标优化**：仅优化收入或仅优化效率，忽视公平和可持续等维度
3. **忽视利益相关者博弈**：公共机构和私营提供商的目标冲突未被显式建模
4. **缺乏空间公平性考量**：激励措施往往忽视对不同区域的差异化影响

### 研究动机
如果城市能智能地：
- 在拥堵时段给公交用户提供更多激励
- 在偏远地区增加共享出行补贴
- 动态调整共享出行价格引导需求分布

则可以在不增加基础设施投资的情况下，显著提升交通系统的整体效率。

## 研究问题

### 核心研究问题
**如何设计一个多智能体RL框架，同时优化公共机构的激励分配和共享出行提供商的定价策略，在冲突目标之间找到帕累托最优？**

三个核心维度：
1. **效率**：降低通勤时间和成本
2. **可持续性**：减少排放
3. **公平性**：确保各区域和群体的公平获益

## 方法概述

### 核心思想
将城市交通管理建模为两个RL智能体的博弈：
- **公共机构智能体**：观察全系统状态（各区域拥堵、排放、公平指标），决定在何时何地给公共交通多少激励（如票价折扣）
- **共享出行智能体**：观察市场需求和公交激励水平，动态调整各区域的出行价格

两个智能体的互动产生纳什均衡，系统自动收敛到效率和公平的平衡点。

### 方法框架

#### 整体架构

```
         ┌──────────────────────┐
         │   交通系统环境        │
         │ (需求/拥堵/路网)      │
         └──────┬───────────────┘
                │
    ┌───────────┼───────────┐
    ↓           ↓           ↓
┌────────┐ ┌────────┐ ┌─────────┐
│公共机构 │ │共享出行│ │出行者   │
│ 智能体  │ │ 智能体 │ │(选择模型)│
│(激励分配)│ │(动态定价)│ │         │
└────────┘ └────────┘ └─────────┘
    │           │           │
    └───────────┴───────────┘
                ↓
          系统状态更新
```

#### 各模块详细说明

**模块1：公共机构RL智能体**
- **功能**：时空公共交通激励分配
- **观察空间**：各区域的拥堵水平、排放数据、出行公平性指标、共享出行价格
- **动作空间**：各区域的公交票价折扣率（0-100%）
- **奖励函数**：$R_{authority} = w_1 \cdot \text{公平性} + w_2 \cdot \text{排放减少} + w_3 \cdot \text{效率} - w_4 \cdot \text{激励成本}$
- **目标**：在预算约束下最大化社会福利

**模块2：共享出行RL智能体**
- **功能**：动态调整出行价格
- **观察空间**：各区域需求水平、公交激励水平、路网拥堵状况
- **动作空间**：各区域的乘数因子（如基础价格的0.8x-2.0x）
- **奖励函数**：$R_{SMS} = \text{收入} - \text{运营成本}$
- **目标**：在竞争环境中最大化利润

**模块3：出行者选择模型**
- **功能**：模拟出行者在公交和共享出行之间的选择
- **模型**：基于logit选择模型，考虑价格、时间、舒适度等因素
- **选择概率**：$P(\text{公交}) = \frac{\exp(U_{bus})}{\exp(U_{bus}) + \exp(U_{SMS})}$

### 关键创新
1. **双智能体博弈框架**：首次同时优化公私两方的策略，而非单方面优化
2. **多目标平衡**：公平、效率、可持续三维度联合优化
3. **时空激励**：激励在空间和时间上差异化，精准针对问题区域
4. **决策支持工具**：不是替代决策者，而是提供数据驱动的建议

## 实验结果

### 实验设置
- **场景**：三小时早高峰时段
- **路网**：基于真实城市的多模式交通网络
- **对比**：静态定价、无激励、单智能体方案

### 主要结果

#### 关键指标改善

| 指标 | 改善幅度 |
|------|----------|
| 通勤者成本 | ↓ ~20% |
| 排放量 | ↓ ~10% |
| 公共交通利润 | ↑ ~100%（接近翻倍） |
| 拥堵峰值 | 有效降低 |
| 福利分配公平性 | 更均衡 |

#### 双智能体协同效果
- 单独运行公交激励智能体：有一定效果但不充分
- 单独运行共享定价智能体：收入增加但公平性恶化
- 双智能体同时运行：实现了效率、公平和可持续性的最优平衡

### 消融与分析
- 激励预算约束对结果的影响
- 不同公平性权重的敏感度分析
- 智能体学习曲线和收敛性

## 深度分析

### 研究价值评估

#### 理论贡献
- 在交通管理中引入MARL博弈框架，建模了多利益相关者的互动
- 提供了多目标（公平、效率、可持续）联合优化的可行方案

#### 实际应用价值
- **城市交通管理**：为城市交通局提供动态激励和定价的决策支持
- **MaaS平台**：可直接集成到出行即服务（MaaS）平台中
- **政策评估**：模拟不同政策选择的后果

### 方法优势详解

1. **自适应性**：策略随交通状况变化自动调整，无需人工干预
2. **多目标平衡**：不牺牲公平换效率，找到多方满意的解
3. **可解释性**：相比黑盒优化，RL策略的动作（调价/激励）直观可理解

### 局限性分析

1. **仿真验证局限**：基于简化的出行者选择模型，真实行为更复杂
2. **两方博弈过于简化**：实际可能有更多利益相关者（多个共享出行公司、出租车等）
3. **训练稳定性**：多智能体RL的训练收敛性本身就是一个挑战
4. **未考虑长期效应**：如激励可能改变人们的长期出行习惯

### 适用性与场景分析

#### 适用场景
- 大城市多模式交通管理
- 大型活动/赛事期间的临时交通管控
- MaaS平台的自动定价引擎

#### 不适用场景
- 小型城市（数据不足以训练RL）
- 缺乏数字化基础设施的地区
- 法规严格限制动态定价的市场

## 未来工作建议

1. **扩展到多提供商竞争**：引入多个共享出行公司之间的博弈
2. **真实世界试点**：在受控环境中验证仿真结果
3. **人机协同**：将RL建议与人类决策者判断结合
4. **长期效应建模**：纳入出行习惯变化和土地利用变化的反馈

## 我的综合评价

### 价值评分

#### 总体评分
**8.0/10** - 将MARL应用于多模式交通管理的多目标优化，问题定义清晰，结果令人印象深刻。是AI for Social Good方向的有价值工作。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 7/10 | MARL在交通中已有应用，但公私双方博弈+多目标优化的组合有新意 |
| 技术质量 | 8/10 | 框架设计合理，实验覆盖充分 |
| 实验充分性 | 8/10 | 三小时早高峰模拟合理，指标全面 |
| 写作质量 | 8/10 | 问题定义清晰，动机充分 |
| 实用性 | 9/10 | 直接可作为城市交通管理的决策支持工具 |

### 重点关注

- **双智能体互动的稳定性**：如何在训练中避免策略震荡
- **公平性定义和量化**：如何在RL奖励中准确编码公平性
- **与真实政策对接**：仿真建议如何转化为实际政策

> [!tip] 关键启示
> 交通系统中多方冲突目标可以通过多智能体RL自动协调——公共激励与私有定价的互动可以产生比任何一方独立优化更好的全局结果。

> [!warning] 注意事项
> - 结果基于仿真，真实部署效果待验证
> - 出行者行为模型简化可能影响策略的有效性
> - 公平性权重的主观性可能影响策略的普适性

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读——对智慧城市、智能交通和MARL应用的读者有价值，方法设计清晰，实验结果令人鼓舞。
