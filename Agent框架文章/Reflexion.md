# Reflexion框架
- 文章地址：https://www.promptingguide.ai/techniques/reflexion
- 论文地址：https://arxiv.org/html/2303.11366?_immersive_translate_auto_translate=1

## Reflexion
- Reflexion 是一个通过语言反馈强化基于语言的智能体的框架。根据 Shinn 等人 (2023) 的研究，"Reflexion 是一种新的‘语言’强化范式，将策略参数化为智能体的记忆编码与 LLM 参数选择相结合。"
- 从高层次来看，Reflexion 将环境中的反馈（无论是自由形式的语言还是标量）转换为语言反馈，也称为自我反思，作为 LLM 智能体在下一集中的上下文。这有助于智能体快速有效地从过去的错误中学习，从而在许多高级任务上提高性能。

### 架构

<img width="939" height="673" alt="image" src="https://github.com/user-attachments/assets/952aa550-0f61-44ed-aaa9-e9f56e1e35eb" />

#### Reflexion 由三个不同的模型组成





# 概念理解
1.  **通过语言反馈强化**:
    *   传统的强化学习（Reinforcement Learning）通常使用数值奖励（比如+1或-1）来告诉AI它的行为是好是坏。
    *   Reflexion的不同之处在于，它使用**自然语言**作为反馈。AI在完成任务后，会进行自我反思，生成一段文字来评估自己的行为哪里做得好，哪里做得不好，以及下次应该如何改进。这种文字形式的反馈就是“语言反馈”。

2.  **将策略参数化为智能体的记忆编码与 LLM 参数选择相结合**:
    *   **策略 (Policy)**：在AI领域，这指的是智能体决定下一步该做什么的规则或方法。
    *   **参数化 (Parameterize)**：意味着将这个策略用一组可调整的参数来表示。
    *   **记忆编码 (Memory Encoding)**：Reflexion框架会将过去的经验和自我反思的语言反馈存储起来，形成一个“记忆”。这个记忆不是简单的信息堆砌，而是经过编码、可供AI检索和学习的结构化信息。
    *   **LLM 参数选择 (LLM Parameter Selection)**：这指的是大型语言模型本身内部的参数，它们决定了模型如何生成文本。
    *   **相结合**: 这句话的核心意思是，Reflexion智能体的决策（策略）不仅仅依赖于LLM本身的能力，还**动态地**结合了它从过去经验和自我反思中学到的“记忆”。

