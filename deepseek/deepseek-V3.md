### Insights into DeepSeek-V3: Scaling Challenges and Reflections on Hardware for AI Architectures
原文：https://arxiv.org/html/2505.09343?_immersive_translate_auto_translate=1

#### 重要概念：
1.  Multi‑Head Latent Attention (MLA，多头潜在注意力)
2.  Mixture of Experts（MoE，专家混合）
3.  FP8 Mixed-Precision Training（混合精度训练）：使用低精度（如 FP16、BF16、FP8）训练可显著降低显存占用和内存带宽需求，从而提高计算吞吐量与训练速度，但同时必须应对数值范围与舍入误差带来的不稳定风险。FP16/BF16 在大多数视觉和语言模型上对最终精度影响通常小于 1%，而 FP8 则需借助动态缩放、随机舍入等技巧才能保证收敛与精度
