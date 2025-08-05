# Transformer
- 论文地址：https://arxiv.org/html/1706.03762?_immersive_translate_auto_translate=1
- 文章讲解：https://www.ibm.com/think/topics/transformer-model

## 概述：
- Transformer 神经网络能够精细地辨别数据序列中每个部分如何影响和关联其他部分的能力，也赋予了它们许多多模态应用。Transformer 的核心特征是其自注意力机制，正是这一机制赋予了 Transformer 强大的能力，使其能够检测输入序列中各部分之间的关系（或依赖）。与它之前的 RNN 和 CNN 架构不同，Transformer 架构仅使用注意力层和标准的前馈层。
- 注意力机制则可以同时检查整个序列，并决定如何以及何时关注该序列的特定时间步。除了显著提高了理解长距离依赖的能力，Transformer 的这一特性还允许并行化：能够同时执行许多计算步骤，而不是以串行方式。（并行处理能力为Transformer大规模训练打开了机会）


## 注意力机制：
注意力机制本质上是一种算法，用于确定 AI 模型在任何特定时刻应该“关注”数据序列的哪些部分。

### 自注意力机制（Self-Attention）工作原理
