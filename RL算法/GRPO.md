# Group Relative Policy Optimization (GRPO)
- 介绍文章：https://www.oxen.ai/blog/why-grpo-is-important-and-how-it-works
- 提出论文：https://arxiv.org/html/2402.03300?_immersive_translate_auto_translate=1

## 概述：
1. GRPO（训练deepseek）相比PPO（训练chatgpt）显著降低计算需求（gpu算力需求）。（在 PPO 中，策略模型和值模型都有需要通过反向传播进行训练的参数。反向传播需要大量的内存。GRPO 省略了值模型。）
2. 
