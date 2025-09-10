# Contrastive Language-Image Pre-training(2021 openai)
- 讲解博客：https://blog.csdn.net/qq_56591814/article/details/127421979?spm=1001.2014.3001.5502



---

## 概述：
- 模型架构：两个并行编码器，图像编码器和文本编码器
- 输入：图像-文本对
- 训练方法：对比学习
- 预训练使用的数据集对CLIP的性能（“理解”能力）有至关重要的影响
- CLIP虽然是多模态模型，但它主要是用来训练可迁移的视觉模型（经过海量图文对比学习训练出来的Image Encoder (图像编码器)可以迁移到下游的各种CV任务）
- 
