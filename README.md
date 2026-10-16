# Thesis — AI/ML 论文研究知识库 · AI/ML Paper Research Knowledge Base

> 基于 [Obsidian](https://obsidian.md/) 的个人 AI/ML 论文研究知识库，聚焦**大语言模型（LLM）**、**多模态**、**强化学习与智能体**、**计算机视觉**四大方向。
> A personal AI/ML paper research knowledge base built on [Obsidian](https://obsidian.md/), focusing on **LLMs**, **multimodal learning**, **reinforcement learning & agents**, and **computer vision**.

## 📁 仓库结构 · Repository Structure

```
lingknowledge/                    # Obsidian Vault
├── 10_Daily/                     # 每日 arXiv 论文推荐（自动生成）
│                                 # Daily paper recommendations (auto-generated)
├── 20_Research/
│   └── Papers/                   # 论文深度分析笔记
│                                 # In-depth paper notes
│       ├── 大语言模型/             # LLM
│       ├── 多模态/                 # Multimodal
│       └── 强化学习与智能体/        # RL & Agents
└── 99_System/
    └── Config/
        └── research_interests.yaml  # 研究方向与关键词配置
                                     # Research interests config
```

## 🔬 研究方向 · Research Directions

| 领域 Domain | 优先级 Priority | 关键词 Keywords |
|-------------|----------------|----------------|
| **大语言模型** LLM | ⭐⭐⭐⭐⭐ | LLM, Transformer, RLHF, RAG, Chain-of-Thought, MoE |
| **多模态** Multimodal | ⭐⭐⭐⭐⭐ | VLM, Diffusion Model, MLLM, Vision-Language, Video Understanding |
| **计算机视觉** Computer Vision | ⭐⭐⭐⭐ | Object Detection, Segmentation, 3D Reconstruction, Neural Rendering |
| **强化学习与智能体** RL & Agents | ⭐⭐⭐⭐ | Multi-Agent, LLM Agent, Planning, Policy Optimization, World Model |

> 注：`计算机视觉` 在配置中为独立领域，相关论文暂并入 `多模态` 目录，未单独建立子目录。
> Note: Computer Vision is a standalone domain in the config; its papers are currently merged into the `多模态` directory.

## 📊 数据统计 · Statistics

- **深度分析笔记** In-depth notes：**283 篇**（大语言模型 188 · 多模态 24 · 强化学习与智能体 71）
- **每日推荐** Daily recommendations：**115 篇**（2026-06-20 起，持续更新）

## 🛠 使用方式 · Usage

1. 克隆仓库后用 [Obsidian](https://obsidian.md/) 打开 `lingknowledge` 文件夹作为 Vault
   Clone the repo, then open the `lingknowledge` folder as a Vault in Obsidian.
2. 编辑 `99_System/Config/research_interests.yaml` 自定义研究兴趣、关键词与优先级
   Customize research interests, keywords, and priorities in `99_System/Config/research_interests.yaml`.

## 🤖 自动化流程 · Automation Pipeline

| 工具 Tool | 用途 Purpose |
|-----------|-------------|
| `start-my-day` skill | 每日从 arXiv 搜索近一月论文，按「相关性 40% + 热门度 30% + 新近性 20% + 质量 10%」综合评分，生成每日推荐并自动深度分析前 3 篇 |
| `paper-analyze` skill | 单篇论文深度分析，生成图文并茂的结构化笔记 |
| `extract-paper-images` skill | 从 arXiv 源码包 / PDF 提取论文配图 |

- `start-my-day`: searches recent arXiv papers, scores them across relevance / popularity / recency / quality, and generates daily recommendations with top-3 deep dives.
- `paper-analyze`: deep analysis of a single paper into a structured, image-rich note.
- `extract-paper-images`: extracts figures from the arXiv source package or PDF.

## 📝 笔记规范 · Note Conventions

- **每日推荐**（`10_Daily/`）：`YYYY-MM-DD论文推荐.md`，包含今日概览、评分排序的论文列表与阅读建议
  **Daily recommendations**: include a daily overview, score-ranked paper list, and reading suggestions.
- **深度分析**（`20_Research/Papers/`）：对重点论文的结构化解读，涵盖研究问题、方法、实验与关键洞察，图文并茂
  **In-depth notes**: structured interpretations covering research question, method, experiments, and key insights, richly illustrated.

## 🔗 相关链接 · Links

- [arXiv](https://arxiv.org/)
- [Semantic Scholar](https://www.semanticscholar.org/)
- [Obsidian](https://obsidian.md/)
