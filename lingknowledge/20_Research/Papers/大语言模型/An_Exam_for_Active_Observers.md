---
date: "2026-07-20"
paper_id: "arXiv:2607.16165"
title: "An Exam for Active Observers"
authors: "Jiarui Zhang, Muzi Tao, Shangshang Wang, Ollie Liu, Xuezhe Ma, Willie Neiswanger"
domain: "大语言模型"
tags:
  - 论文笔记
  - 大语言模型
  - 多模态
  - MLLM
  - Active-Vision
  - Benchmark
  - Visual-Reasoning
  - Agentic-AI
quality_score: "9.0/10"
created: "2026-07-20"
updated: "2026-07-20"
status: analyzed
---

# An Exam for Active Observers

## 核心信息
- **论文ID**：arXiv:2607.16165
- **作者**：Jiarui Zhang*, Muzi Tao*, Shangshang Wang*, Ollie Liu, Xuezhe Ma, Willie Neiswanger (*equal contribution)
- **机构**：University of Southern California (USC)
- **发布时间**：2026-07-17
- **会议/期刊**：--
- **链接**：[arXiv](http://arxiv.org/abs/2607.16165v1) | [PDF](https://arxiv.org/pdf/2607.16165v1)
- **项目页面**：https://activevision.dev | [GitHub](https://github.com/saccharomycetes/ActiveVision) | [Dataset](https://huggingface.co/datasets/activevisionai/ActiveVision)

## 摘要翻译

### 英文摘要
Human vision is a closed loop: gaze is continuously redirected by intermediate hypotheses rather than a single snapshot. Decades of psychophysics and cognitive science have argued that this active observation is essential for a wide range of tasks. Whether today's multimodal large language models (MLLMs) exercise active observation is an empirical question that current vision-language benchmarks do not answer. We introduce ActiveVision, a benchmark that makes active observation measurable for MLLMs, comprising 17 tasks across 3 categories. Frontier MLLMs collapse on ActiveVision: the highest-scoring model we evaluate, GPT-5.5 at the highest exposed reasoning-effort tier, solves only 10.6% of items and scores zero on 11 of the 17 tasks, and even Claude Fable 5, despite topping most reasoning and coding leaderboards, solves just 3.5%, far behind three human participants who average 96.1%. Furthermore, much of the gap persists even when models write and run their own vision code. Such code is unreliable on realistic imagery, and catching its failures itself requires the active perception the models lack.

### 中文翻译
人类视觉是一个闭环：视线持续被中间假设重新引导，而非一次性快照。数十年的心理物理学和认知科学研究表明，这种"主动观察"对广泛的任务至关重要。今天的多模态大语言模型（MLLM）是否具备主动观察能力，是当前视觉语言基准无法回答的经验性问题。我们提出 ActiveVision，一个使 MLLM 的主动观察能力可衡量的基准，包含 3 个类别的 17 个任务。前沿 MLLM 在 ActiveVision 上崩溃：得分最高的模型 GPT-5.5（最高推理算力档）仅解决 10.6% 的题目，在 17 个任务中有 11 个得分为零；即使 Claude Fable 5（在大多数推理和编程排行榜上名列前茅）也仅解决 3.5%，远低于三位人类参与者平均 96.1% 的表现。此外，即使模型编写并运行自己的视觉代码，大部分差距仍然存在。这类代码在逼真图像上不可靠，而捕捉其失败本身就需要模型所缺乏的主动感知能力。

### 核心要点提炼
- **研究背景**：现有视觉语言基准（MMMU-Pro、CharXiv 等）趋于饱和，无法区分前沿模型，但并未真正衡量 MLLM 的主动观察能力
- **研究动机**：人类视觉本质上是主动的（反复扫视、形成假设、验证），但现有 MLLM 是否具备此能力尚无测量手段
- **核心方法**：构建 ActiveVision 基准，包含 17 个强制"反复看图"的任务，跨越三个认知维度
- **主要结果**：最强模型仅 10.6% 准确率，人类 96.1%；增加推理算力几乎无效；代码工具可提升至 50.6% 但仍有巨大差距
- **研究意义**：将"主动视觉"从修辞性说法转变为可量化的能力缺口，为未来架构和训练目标提供明确方向

## 研究背景与动机

### 领域现状
多模态大语言模型（MLLM）发展迅速。从 GPT-4 系列到 Claude Fable 5、GPT-5.5、Gemini 3.1 Pro，前沿模型在 MMMU-Pro、CharXiv 等基准上表现接近饱和，使得不同模型之间几乎无法区分。然而这些基准多为单次图像描述、视觉问答或多选题，可以通过对图像的单次静态描述来回答。

### 现有方法的局限性
现有基准存在三个根本缺陷：
1. **缺乏主动感知测量**：几乎所有基准都需要一次性的图像编码和回答，不要求模型反复查看图像
2. **语言捷径**：许多问题可以通过语言先验而非真正的视觉观察来回答
3. **饱和问题**：前沿模型已接近天花板，无法揭示剩余的核心能力缺陷

### 研究动机
机器人、设计、制造、计算机使用、空间理解和科学发现等实际应用，要求模型在推理过程中不断返回视觉证据。但这些能力在现有基准中几乎未被测量。作者从认知科学和计算机视觉数十年的研究中汲取灵感，将"主动观察"（active observation）操作化为可测量的基准。

## 研究问题

### 核心研究问题
> **MLLM 是否执行主动观察——在推理过程中不断返回图像，形成和检验假设？**

这是一个行为层面而非架构层面的问题。即使 MLLM 的视觉 token 在单次前向传播中编码，自回归推理过程中模型也可能在视觉 token 间转移注意力——问题是这在实践中是否有效发生。

## 方法概述

### 核心思想
ActiveVision 的核心设计原则是：**每个任务实例包含的信息量超过任何单一语言描述所能无损承载的信息量**。如果模型仅对图像进行一次"压缩"（如生成文字描述），然后基于该摘要推理，则会丢失答案所依赖的信息——必须让图像本身保持在推理循环中。

### 方法框架

#### 整体架构

![[pipeline_page1.png|800]]

> 图：ActiveVision 的两阶段任务构建管线。第一阶段：程序化生成精确的几何骨架（位置、形状、曲线）；第二阶段：GPT-image-2 将骨架渲染为逼真图像，同时保持拓扑和计数不变。

#### 任务设计三原则

**任意位置 (Arbitrary positions)**：
- 项目放置在连续采样的坐标上，而非网格或命名锚点
- 20 个散布的点产生 190 对空间关系和 20 个实值坐标对，远超任何语言摘要的承载能力

**任意形状 (Arbitrary shapes)**：
- 区域边界、轮廓、剪影每次从 Fourier 谐波或周期样条新鲜合成
- 连续高方差的剪影空间，无两例重复

**任意轨迹 (Arbitrary traces)**：
- 路径、箭头链、缠绕曲线均为经过采样控制点的平滑随机样条
- 包含数十个有意义的拐点，无法用单一段落描述

#### 三大任务家族

**1. 分布式扫描 (Distributed Scanning) — 5 个任务**
- 图像包含大量空间分散的局部信号（点、笔画、区域、图面）
- 需逐一发现和累积
- 典型失败模式：部分覆盖（只数了 10 个中的 5-6 个）、个体化失败（无法区分相邻信号）
- 任务：有界面计数、连通分量计数、区域计数、单例形状计数、缠绕环计数

**2. 顺序遍历 (Sequential Traversal) — 5 个任务**
- 图像编码连接结构（箭头链、穿过彩色区域的缠绕曲线、迷宫管）
- 需逐步跟随，同时维护当前位置、方向和运行计数
- 典型失败模式：格式塔插值（从起点猜测终点，跳过中间步骤）
- 任务：箭头链跟随、遍历点排序、色彩区域序列、线交点序列、迷宫路径追踪

**3. 视觉属性传递 (Visual Attribute Transfer) — 7 个任务**
- 跨区域细粒度比较
- 从参考区域提取视觉属性（长度、曲率、厚度、颜色排列、点模式、方向）
- 与图像中其他位置的候选者匹配或比较
- 典型失败模式：先验替换（不测量两个区域，而是应用学习到的语言先验）
- 任务：星座匹配计数、剪影匹配计数、笔画匹配计数、轮廓差异发现、场差异发现、信号差异发现、笔画差异发现

## 实验结果

### 实验设置

- **评估模型**：GPT-5.5、Claude Opus 4.7/4.8、Claude Fable 5、Gemini 3.1 Pro、Gemini 3.5 Flash
- **评估方式**：每个任务 5 个实例 × 17 个任务 = 85 个题目
- **评估指标**：精确匹配准确率（Exact Match）
- **人类基线**：3 名参与者，自定步调的 Web 界面
- **推理算力**：测试了所有 API 暴露的推理算力档位（none → xhigh/max）
- **工具使用**：额外评估了 Codex 和两个 Claude Code agent

### 主要结果

#### 主实验结果

| 模型 | 最佳算力档 | 正确数/85 | 准确率 |
|------|-----------|----------|--------|
| GPT-5.5 | xhigh | 9/85 | 10.6% |
| Gemini 3.5 Flash | high | 7/85 | 8.2% |
| Gemini 3.1 Pro | high | 5/85 | 5.9% |
| Claude Opus 4.7 | max | 4/85 | 4.7% |
| Claude Fable 5 | max | 3/85 | 3.5% |
| Claude Opus 4.8 | max | 2/85 | 2.4% |
| **人类（平均）** | -- | **81.7/85** | **96.1%** |

![[performance_cost_page1.png|800]]

> 图：准确率 vs API 成本（对数尺度）。虚线为人类平均值和范围。所有模型停留在狭窄的低准确率区域，推理算力增加近 100 倍仅将 GPT-5.5 准确率从 2.4% 提升至 10.6%。

#### 核心发现

**1. 增加推理算力几乎无效**
- GPT-5.5 从 none→xhigh：每项成本增加近百倍，准确率仅 2.4%→10.6%
- Fable 5 花费 31 倍成本未见准确率提升
- 模型不缺推理步骤，而是无法从图像中提取正确的视觉证据

**2. 计数能力随场景复杂度下降**
- 所有模型在计数任务上的拟合斜率远低于对角线（理想计数）
- 真值越大，漏计数越严重——模型是"保守的计数者"
- 似乎只是"瞥一眼"图像而非彻底扫描

**3. 追踪在第一步就丢失**
- 三个有序遍历任务的前缀生存率在第一个或第二个步骤内崩溃
- 整个评估池中没有一次完整正确完成追踪
- 失败不是渐进漂移，而是从一开始就丢失了路径框架

**4. "相同"成为视觉比较的安全默认值**
- 差异化任务中，漏检率很高但误报率极低
- 多个算力档位回答几乎所有题目为"none"（无差异），漏掉所有真实差异
- 这是回答偏见而非感知行为

### 工具使用评估

| Agent | 准确率 |
|-------|--------|
| Codex (o5) | 50.6% |
| Claude Code (Opus 4.8) | 31.8% |
| Claude Code (Fable 5) | 24.7% |

- 代码工具在属性传递任务上表现良好（可通过 findContours/Canny/模板匹配解决）
- 遍历任务对两个 agent 仍接近零
- 工具失败的核心原因：无法进行主动视觉验证——Agent 需要能"回头看"来判断代码输出是否正确

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1：操作化了"主动观察"概念** — 将认知科学数十年的概念转化为可测量的 MLLM 基准
- **贡献2：揭示了推理与感知的脱钩** — 证明了增加推理算力无法弥补感知缺陷，指出这是独立的能力维度
- **贡献3：建立了严格的"反语言描述"设计原则** — 三个设计属性（任意位置/形状/轨迹）可被后续基准复用

#### 实际应用价值
- 为机器人、医疗影像、制造质检、卫星图像分析等需要反复"看"的领域提供了能力评估工具
- 明确指出了 MLLM 在实际部署中的关键瓶颈：不是"能不能理解"，而是"能不能坚持看"

### 方法优势详解

1. **认知基础扎实**：任务设计直接锚定在心理物理学和神经生理学的经典发现上（subitizing、curve tracing、visual working memory）
2. **反语言捷径设计**：通过 Three Arbitrary 原则确保语言描述无法承载答案信息
3. **逼真渲染**：使用 GPT-image-2 而非简单几何线条，测试真实噪声场景

### 局限性分析

1. **合成图像**：虽然逼真，但仍是 GPT-image-2 生成，非自然图像分布样本。外部有效性取决于任务所测量的基础视觉操作本身
2. **反语言描述性质可能随时间退化**：随着模型描述图像能力增强，此属性可能减弱
3. **Agent 评估非正式赛道**：工具使用评估作为消融实验而非独立赛道
4. **85 个样本较小**：每个任务仅 5 个实例

### 技术路线定位

本文属于 MLLM 能力评估的新方向——从"会不会看"到"会不会一直看"：
```
MMBench/MMMU (通用理解) → MMVP/MMStar (视觉优先) → BLINK/BlindTest (人机差距) → ActiveVision (主动观察)
```

ActiveVision 在 Blink/BlindTest 等"人机差距"基准基础上，更进一步地精确隔离了"主动视觉"这一特定缺失能力。

## 我的综合评价

### 价值评分

#### 总体评分
**9.0/10** — 这是一篇设计极其扎实的基准论文，锚定在深厚的认知科学基础上，揭示了一个所有前沿 MLLM 的通用盲区。

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 9/10 | 首次将"主动观察"操作化为可测量基准，设计原则具有原创性 |
| 技术质量 | 9/10 | 方法严谨，任务设计有认知科学支撑，两阶段管线保证可控性 |
| 实验充分性 | 8/10 | 覆盖所有主流前沿模型和全部算力档位，包含人类基线和工具使用消融 |
| 写作质量 | 9/10 | 清晰、有说服力、叙事流畅 |
| 实用性 | 9/10 | 直接可用的评估工具，对 MLLM 发展方向有实质性指导意义 |

> [!tip] 关键启示
> 当前最强 MLLM 在需要"反复看"的任务上几乎完全崩溃，且增加推理算力无法解决——主动视觉感知是独立于推理的能力维度，需要专门的架构创新和训练目标。

> [!success] 推荐指数
> ⭐⭐⭐⭐⭐ 强烈推荐阅读！这是 MLLM 评估领域的里程碑工作，可能开启"主动视觉 AI"这一新研究方向。

## 相关论文

### 直接相关
- [[20_Research/Papers/大语言模型/BLINK|BLINK]] — 收集人类眨眼间能回答的任务，揭示人机视觉差距
- [[20_Research/Papers/大语言模型/MMVP|MMVP]] — 视觉优先基准，排除文本捷径

### 背景相关
- [[20_Research/Papers/大语言模型/MMMU|MMMU]] — 多模态理解基准
- [[20_Research/Papers/大语言模型/CharXiv|CharXiv]] — 图表理解基准
