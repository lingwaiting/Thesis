# 图片索引

论文：Local Sparsity Enables Unsupervised LLM Safety Detection（arXiv:2609.20129）

总计：18 张图片

## 核心图片（正文）

| 文件名 | 对应图 | 来源 |
|--------|--------|------|
| 2609.20129_fig1_local_mask.png | Figure 1：SAE 特征空间中的逐簇 mask（局部稀疏示意） | pdf-extraction |
| 2609.20129_fig_auroc_vs_fpr95_llama3.png | Figure 2（引言）：LLaMA3-8B 上 AUROC vs FPR@95 | arxiv-source |
| 2609.20129_fig_effective_dim.png | Figure 3（实验）：有效维度 d90 分布 | arxiv-source |
| 2609.20129_fig_cluster_occupancy_llama3.png | Figure 4（讨论）：LLaMA3-8B 各簇数据分布 | arxiv-source |

## 附录图片

### 校准（calibration）

| 文件名 | 对应图 | 来源 |
|--------|--------|------|
| 2609.20129_fig_cluster_distributions_llama3.png | 逐簇得分分布（LLaMA3-8B） | arxiv-source |
| 2609.20129_fig_cluster_scales_all.png | 六模型逐簇得分离散度 | arxiv-source |
| 2609.20129_fig_calib_benign_qwen2.png | 校准后良性 FPR（Qwen2-1.5B） | arxiv-source |
| 2609.20129_fig_calib_benign_ministral.png | 校准后良性 FPR（Ministral-8B） | arxiv-source |
| 2609.20129_fig_calib_benign_llama3.png | 校准后良性 FPR（LLaMA3-8B） | arxiv-source |
| 2609.20129_fig_calib_benign_qwen3.png | 校准后良性 FPR（Qwen3-8B） | arxiv-source |
| 2609.20129_fig_calib_benign_gptoss.png | 校准后良性 FPR（GPT-OSS-20B） | arxiv-source |
| 2609.20129_fig_calib_benign_gemma.png | 校准后良性 FPR（Gemma-4-26B） | arxiv-source |

### 各模型簇占用（cluster occupancy）

| 文件名 | 对应图 | 来源 |
|--------|--------|------|
| 2609.20129_fig_cluster_occupancy_qwen2.png | Qwen2-1.5B | arxiv-source |
| 2609.20129_fig_cluster_occupancy_ministral.png | Ministral-8B | arxiv-source |
| 2609.20129_fig_cluster_occupancy_qwen3.png | Qwen3-8B | arxiv-source |
| 2609.20129_fig_cluster_occupancy_gptoss.png | GPT-OSS-20B | arxiv-source |
| 2609.20129_fig_cluster_occupancy_gemma.png | Gemma-4-26B | arxiv-source |

### 有效维度（effective dimension，附录）

| 文件名 | 对应图 | 来源 |
|--------|--------|------|
| 2609.20129_fig_effective_dim_appendix.png | 两个额外模型家族的 d90 分布 | arxiv-source |

## 来源说明

- **arxiv-source**：直接从 arXiv 源码包 `src/plots/` 目录提取的作者原始 PDF 图（矢量图，转 PNG）。
- **pdf-extraction**：Figure 1 为纯 TikZ 矢量图，无独立图片文件，从编译后 PDF 第 2 页按 caption 定位裁剪得到。
