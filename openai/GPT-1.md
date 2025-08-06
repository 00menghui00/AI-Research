# Improving Language Understanding by Generative Pre-Training
- https://github.com/openai/finetune-transformer-lm/tree/master

## 概述：
- 预训练-微调（Pre-training and Fine-tuning）范式：我们证明，通过在一个多样化的未标记文本语料库上对语言模型进行生成式预训练，然后在每个特定任务上进行判别式微调，可以在这些任务上实现巨大提升。
- 与以往的方法相比，我们在微调过程中利用了任务感知的输入转换，以实现有效的迁移，同时只需对模型架构进行最小的改动（不用改动模型主体即可对不同类型的任务进行处理）。
