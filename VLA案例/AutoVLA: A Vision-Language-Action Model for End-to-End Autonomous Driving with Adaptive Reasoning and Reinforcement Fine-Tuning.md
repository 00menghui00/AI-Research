# AutoVLA: A Vision-Language-Action Model for End-to-End Autonomous Driving with Adaptive Reasoning and Reinforcement Fine-Tuning
- 论文地址：https://arxiv.org/html/2506.13757?_immersive_translate_auto_translate=1

## 引言
- 自动驾驶系统通常采用模块化范式，将驾驶任务分解为不同的子模块，例如感知、预测和规划。虽然这种设计能够实现结构化开发，但它可能导致错误累积和模块间缺乏联合优化，从而造成性能次优。端到端的自动驾驶随着统一模型架构的兴起而备受关注，该架构将原始传感器输入直接映射到最终驾驶动作。
- 在本文中，我们提出了 AutoVLA，一种新型 VLA 模型，它将推理和行动生成统一于单一的自回归生成模型中，用于端到端自动驾驶。
