# Qwen3 Technical Report(2025)
- 论文地址：https://ar5iv.labs.arxiv.org/html/2505.09388?_immersive_translate_auto_translate=1


## Introduction：
- Qwen3 密集模型的架构与 Qwen2.5（qwen2.5）相似，包括使用分组查询注意力（GQA，gqa）、SwiGLU（glu）、旋转位置编码（RoPE，rope）以及预规范化 RMSNorm（rmsnorm）。此外，我们移除了 Qwen2（qwen2）中使用的 QKV 偏置，并在注意力机制中引入了 QK-Norm（pmlr-v202-dehghani23a），以确保 Qwen3 的稳定训练。
