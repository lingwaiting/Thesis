# Thesis — AI/ML 论文研究知识库

基于 [Obsidian](https://obsidian.md/) 的个人 AI/ML 论文研究知识库，聚焦**大语言模型（LLM）**、**强化学习与智能体**、**多模态**和**计算机视觉**四大方向。

## 📁 仓库结构

```
lingknowledge/
├── 10_Daily/              # 每日 arXiv 论文推荐（由 start-my-day skill 自动生成）
├── 20_Research/
│   └── Papers/
│       ├── 大语言模型/       # LLM 相关论文深度分析
│       │   ├── MedRLM/            # 医学 LLM
│       │   ├── AdversaBench.md   # LLM 红队测试
│       │   ├── AI-PAVE-Br.md     # 电商属性值抽取
│       │   ├── Two-Stage Transformer.md  # 工业故障诊断
│       │   └── ...
│       └── 强化学习与智能体/   # RL & Agent 相关论文深度分析
│           ├── Contagion_Networks/
│           ├── Multi-Agent_Multi-Objective/
│           ├── Decentralized Traffic Management.md
│           └── ...
├── 99_System/
│   └── Config/
│       └── research_interests.yaml  # 研究方向配置
```

## 🔬 研究方向

| 领域 | 优先级 | 关键词 |
|------|--------|--------|
| **大语言模型** | ⭐⭐⭐⭐⭐ | LLM, Transformer, RLHF, RAG, Chain-of-Thought, MoE |
| **多模态** | ⭐⭐⭐⭐⭐ | VLM, Diffusion Model, MLLM, Video Understanding |
| **计算机视觉** | ⭐⭐⭐⭐ | Object Detection, Image Generation, 3D Reconstruction |
| **强化学习与智能体** | ⭐⭐⭐⭐ | Multi-Agent, LLM Agent, Planning, Policy Optimization |

## 🛠 使用方式

1. 克隆仓库后用 [Obsidian](https://obsidian.md/) 打开 `lingknowledge` 文件夹作为 Vault
2. `99_System/Config/research_interests.yaml` 可自定义研究兴趣和关键词

## 📝 论文笔记规范

- **每日推荐**（`10_Daily/`）：由自动化 pipeline 从 arXiv 筛选，包含评分、趋势分析和阅读建议
- **深度分析**（`20_Research/Papers/`）：对重点论文的结构化解读，涵盖方法、实验和关键洞察

## 🔗 相关链接

- [arXiv](https://arxiv.org/)
- [Semantic Scholar](https://www.semanticscholar.org/)
- [Obsidian](https://obsidian.md/)
