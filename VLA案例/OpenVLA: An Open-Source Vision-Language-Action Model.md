# OpenVLA: An Open-Source Vision-Language-Action Model
- 论文地址：https://arxiv.org/html/2406.09246?_immersive_translate_auto_translate=1
- 代码仓库：https://github.com/openvla/openvla

## 引言：
- 传统机器人模型采用模块化集成架构，本文提出的OpenVLA使用端到端架构，直接将 VLMs 作为语言模型词汇表中的标记进行微调，以生成机器人动作。我们的实验评估表明，这种简单但可扩展的流程显著提升了性能和泛化能力，优于先前的通才型策略（将预训练组件（如语言嵌入或视觉编码器）与从头初始化的额外模型组件相结合，在策略训练过程中学习将它们“拼接”在一起）。
- 在 VLM 训练过程中，模型使用配对或交错来自各种互联网来源的视觉和语言数据进行端到端训练，目标是对下一个文本标记进行预测。

## 模型架构：
### vision encoder（视觉编码器）
用 DINOv2 + SigLIP 深度感知世界，将图像输入映射到若干“图像块嵌入”。
### projector（投影器）
将视觉信息无缝传递给大脑，接收视觉编码器的输出嵌入，并将它们映射到语言模型的输入空间。
### LLM backbone（LLM主干）
用 Llama 2 进行统一的思考和决策，直接输出行动指令。
