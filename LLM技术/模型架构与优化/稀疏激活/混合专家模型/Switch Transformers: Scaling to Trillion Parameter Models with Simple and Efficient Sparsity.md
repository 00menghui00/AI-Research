# Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity(2021)
- 论文地址：https://ar5iv.labs.arxiv.org/html/2101.03961?_immersive_translate_auto_translate=1

## 概述：
- 我们简化了 MoE 路由算法，并设计了通信和计算成本更低、更直观的改进模型。
- 我们提出的训练技术缓解了不稳定性问题，并且首次证明了大型稀疏模型可以用较低精度的（bfloat16）格式进行训练。
- Switch Transformers 的指导设计原则是以简单且计算高效的方式最大化 Transformer 模型的参数数量。（规模效应由openai提出，并证明扩大模型比扩充数据更有性价比）


