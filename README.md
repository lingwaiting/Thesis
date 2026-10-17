# Thesis — AI/ML 论文研究知识库 · AI/ML Paper Research Knowledge Base

> 基于 [Obsidian](https://obsidian.md/) 的个人 AI/ML 论文研究知识库，聚焦 **大语言模型（LLM）**、**多模态**、**强化学习与智能体**、**计算机视觉** 四大方向。
> A personal AI/ML paper research knowledge base built on [Obsidian](https://obsidian.md/), focusing on **LLMs**, **multimodal learning**, **reinforcement learning & agents**, and **computer vision**.

自动化流程每日从 arXiv 抓取、评分、筛选论文，生成图文并茂的深度分析笔记，沉淀为可检索、可回溯的个人研究库。
An automation pipeline scrapes, scores, and filters recent arXiv papers daily, generating richly illustrated in-depth notes into a searchable, reviewable personal research library.

---

## 📁 仓库结构 · Repository Structure

```
lingknowledge/                          # Obsidian Vault（用 Obsidian 打开此目录）
├── 10_Daily/                           # 每日论文推荐（自动生成）
│   └── YYYY-MM-DD论文推荐.md           #   每日概览 + 评分排序的论文列表 + 阅读建议
│
├── 20_Research/
│   └── Papers/                         # 论文深度分析笔记（按研究方向分类）
│       ├── 大语言模型/                  #   LLM
│       │   ├── <Slug>.md               #     深度分析笔记（分类层级）
│       │   └── <Slug>/images/          #     该论文的配图（PNG/PDF + index.md 图片索引）
│       ├── 多模态/                      #   Multimodal
│       │   ├── <Slug>.md
│       │   └── <Slug>/images/
│       └── 强化学习与智能体/             #   RL & Agents
│           ├── <Slug>.md
│           └── <Slug>/images/
│
└── 99_System/
    └── Config/
        └── research_interests.yaml      # 研究方向、关键词与优先级配置
```

- `10_Daily/` — 每日推荐的**索引性**内容：今日概览、评分排序的论文清单、每篇的一句话总结与核心贡献，并以 `[[wiki 链接]]` 关联到 `20_Research/Papers/` 中的深度分析笔记。
- `20_Research/Papers/` — 重点论文的**深度分析**：研究问题、方法、实验与关键洞察，配图存放于同名 `images/` 子目录。
- `99_System/` — 系统配置（研究兴趣、自动化参数），非论文内容。

---

## 📝 命名规范 · Naming Conventions

知识库采用统一、严格的命名约定，便于检索与脚本化处理。

### 论文深度分析（`20_Research/Papers/<分类>/`）

每篇论文由**两个同名实体**组成，命名格式为 `<缩写>_<全英文标题>`：

```
<分类>/<Slug>.md            # 深度分析笔记（分类层级，与图片文件夹同级）
<分类>/<Slug>/images/*.png  # 配图（含 index.md 图片索引）
```

其中 `<Slug>` 的生成规则：
- **格式**：`缩写_全英文标题`（取自论文 `title` 字段）
- **空格 → 下划线** `_`；**连字符保留**（如 `Code-Switching`、`MLLM`、`GPT-3`）
- **逗号、撇号等标点原样保留**（如 `Active_Adaptation,_Not_Static_Defense_...`、`...of_LLMs'_Performance...`）
- **笔记文件与图片文件夹同名**，笔记位于**分类层级**（不放进文件夹内）

示例：
```
大语言模型/CoTranslator_Reverse_to_Forward_Chain-of-Thought_Translator_for_Reasoning_Distillation.md
大语言模型/CoTranslator_Reverse_to_Forward_Chain-of-Thought_Translator_for_Reasoning_Distillation/images/
```

### 每日推荐（`10_Daily/`）

- 文件名：`YYYY-MM-DD论文推荐.md`（`YYYY-MM-DD` 为推荐日期）
- 内容结构：`今日概览` → 评分排序的论文列表（作者 / 机构 / 链接 / 一句话总结 / 核心贡献 / 关键结果）→ `阅读建议`

---

## 🔬 研究方向 · Research Directions

| 领域 Domain | 优先级 Priority | 关键词 Keywords（节选） |
|-------------|:--------------:|------------------------|
| **大语言模型** LLM | ⭐⭐⭐⭐⭐ | LLM, Transformer, RLHF, RAG, Chain-of-Thought, MoE, Reasoning, Alignment |
| **多模态** Multimodal | ⭐⭐⭐⭐⭐ | VLM, Diffusion Model, MLLM, Vision-Language, Video Understanding, Image Captioning |
| **计算机视觉** Computer Vision | ⭐⭐⭐⭐ | Object Detection, Segmentation, 3D Reconstruction, Neural Rendering |
| **强化学习与智能体** RL & Agents | ⭐⭐⭐⭐ | Multi-Agent, LLM Agent, Planning, Policy Optimization, World Model, Reward Model |

> 注：`计算机视觉` 在 `research_interests.yaml` 中为独立研究领域，但相关论文当前并入 `多模态` 目录，未单独建立子目录。
> Note: Computer Vision is a standalone domain in the config; its papers are currently merged into the `多模态` directory.

完整的关键词与 arXiv 分类见 `99_System/Config/research_interests.yaml`。

---

## 🗂 笔记字段说明 · Note Frontmatter

深度分析笔记使用 YAML frontmatter 记录元数据。当前主流格式（`start-my-day` / `paper-analyze` 生成）字段如下：

| 字段 Field | 说明 Description |
|-----------|-----------------|
| `date` | 笔记生成日期 |
| `paper_id` | 论文 arXiv ID（如 `2610.20828`） |
| `title` | 论文完整英文标题 |
| `authors` | 作者（逗号分隔） |
| `domain` | 所属研究方向（`大语言模型` / `多模态` / `强化学习与智能体`） |
| `tags` | 标签（含 `论文笔记` + 主题关键词） |
| `quality_score` | 质量评分（如 `8.8/10`） |
| `related_papers` | 相关论文链接 |
| `created` / `updated` | 创建 / 更新时间 |
| `status` | 处理状态（如 `analyzed`） |

少量早期笔记沿用旧格式（`title` / `arxiv_id` / `authors` / `categories` / `published` / `tags`），字段含义一致，正在逐步统一。

---

## 🤖 自动化流程 · Automation Pipeline

知识库由以下 Claude Code skill 自动维护：

| 工具 Tool | 用途 Purpose |
|-----------|-------------|
| `start-my-day` | 每日从 arXiv 搜索近一月论文，按「相关性 40% + 热门度 30% + 新近性 20% + 质量 10%」综合评分，生成 `10_Daily/YYYY-MM-DD论文推荐.md`，并对 Top 3 自动深度分析（产出 `20_Research/Papers/` 笔记 + 配图） |
| `paper-analyze` | 对单篇论文做深度分析，生成结构化、图文并茂的笔记 |
| `extract-paper-images` | 从 arXiv 源码包 / PDF 提取论文配图，存入对应 `<Slug>/images/` |

- `start-my-day`: searches recent arXiv papers, scores them across relevance / popularity / recency / quality, generates `10_Daily/YYYY-MM-DD论文推荐.md`, and auto-analyzes the top 3 into `20_Research/Papers/` notes with figures.
- `paper-analyze`: deep analysis of a single paper into a structured, image-rich note.
- `extract-paper-images`: extracts figures from the arXiv source package or PDF into `<Slug>/images/`.

---

## ⚙️ 研究兴趣配置 · Research Interests Config

`99_System/Config/research_interests.yaml` 控制自动化流程的推荐倾向：

| 配置项 Key | 说明 Description |
|-----------|-----------------|
| `language` | 笔记语言（`zh` / `en`） |
| `semantic_scholar_api_key` | Semantic Scholar API Key（可选，用于获取引用数与热门度） |
| `research_domains.<领域>.keywords` | 该领域的关键词，用于论文相关性匹配 |
| `research_domains.<领域>.arxiv_categories` | 对应的 arXiv 分类（如 `cs.CL`） |
| `research_domains.<领域>.priority` | 优先级（1–5，越高越重要） |
| `excluded_keywords` | 排除关键词（命中则降权或剔除，如 `workshop`、`tutorial`） |

修改 `keywords` / `priority` 即可调整每日推荐的倾向与覆盖面。

---

## 📊 数据统计 · Statistics

- **深度分析笔记** In-depth notes：**292 篇**
  - 大语言模型 LLM：**195 篇**
  - 多模态 Multimodal：**24 篇**
  - 强化学习与智能体 RL & Agents：**73 篇**
- **配图文件夹** Figure folders：**304 个**
- **每日推荐** Daily recommendations：**115 篇**（2026-06-20 起，持续更新）

> 少数论文仅有配图而无深度分析笔记（推荐过但未做深度分析），或仅有笔记而无配图（纯文本分析），属正常情况。

---

## 🛠 使用方式 · Usage

1. **克隆仓库**后用 [Obsidian](https://obsidian.md/) 打开 `lingknowledge` 文件夹作为 Vault
   Clone the repo, then open the `lingknowledge` folder as a Vault in Obsidian.
2. **自定义研究兴趣**：编辑 `99_System/Config/research_interests.yaml` 中的关键词与优先级
   Customize research interests, keywords, and priorities in `99_System/Config/research_interests.yaml`.
3. **浏览每日推荐**：从 `10_Daily/` 最新日期开始，点击「详细报告」链接跳转至深度分析笔记
   Browse daily recommendations starting from the latest date in `10_Daily/`, clicking "详细报告" links to open in-depth notes.
4. **按领域检索**：在 `20_Research/Papers/<分类>/` 下按研究方向浏览，或使用 Obsidian 的标签 / 全文搜索
   Browse by research direction under `20_Research/Papers/<分类>/`, or use Obsidian's tag / full-text search.

---

## 🔧 维护指南 · Maintenance Guide

- **新增论文笔记**：遵循「命名规范」章节的 `<缩写>_<全英文标题>` 格式，笔记与 `images/` 文件夹同名、同级。
- **更新统计**：新增 / 删除笔记后，同步更新上文「数据统计」中的数量。
- **提交变更**：本仓库提交前请确认仅包含**已暂存**的文件，勿提交无关或未暂存内容。

---

## 🔗 相关链接 · Links

- [arXiv](https://arxiv.org/)
- [Semantic Scholar](https://www.semanticscholar.org/)
- [Obsidian](https://obsidian.md/)
