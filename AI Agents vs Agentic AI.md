### AI Agents vs. Agentic AI: A Conceptual Taxonomy, Applications and Challenges
原文：https://arxiv.org/html/2505.10468?_immersive_translate_auto_translate=1

#### 核心概念：
1. AI Agents（AI 智能体）：modular systems driven and enabled by LLMs and LIMs for task-specific automation. Generative AI is positioned as a precursor providing the foundation, with AI agents advancing through tool integration, prompt engineering, and reasoning enhancements.
2. Agentic AI systems（自主AI系统）：represent a paradigm shift marked by multi-agent collaboration, dynamic task decomposition, persistent memory, and coordinated autonomy.
3. 幻觉（hallucination）：LLM 在缺乏或模糊信息时生成错误但自信的内容；
4. 脆弱性（brittleness）：模型在稍有偏离训练分布时性能骤降；
5. 涌现行为（emergent behavior）：简单规则或交互中自发产生的复杂模式；
6. 协调失败（coordination failure）：多智能体因决策或信息不一致导致整体次优结果；
7. ReAct 循环（ReAct loops）：将推理（Reasoning）与行动（Acting）交织，用于增强 LLM 交互；
8. 检索增强生成（Retrieval-Augmented Generation, RAG）：在生成前检索外部知识库以提高准确性；
9. 自动化协调层（automation coordination layers）：在大规模多 Agent 系统中负责任务分配、资源管理与冲突解决的中间件；
10. 因果建模（causal modeling）：构建并利用因果关系图或结构模型来实现更稳健的推断与决策；
11. 多智能体系统 (Multi-Agent Systems, MAS)：MAS 是由多个自治智能体（agent）构成的系统，这些智能体能够感知环境、作出决策、相互通信，并为实现共同或竞争目标协作或博弈；
12. 专家系统 (Expert Systems)：专家系统是一类早期 AI 应用，旨在模拟人类专家的决策能力，通过显式的“知识库（facts & rules）+ 推理引擎”进行推理和解释；
13. synthetic world simulations（合成世界仿真）：指在计算机中创建高度逼真、可交互的虚拟环境，用于训练 AI、测试系统或研究复杂现象。它们将数字世界与真实世界的物理、行为、心理等多层面要素高度还原与模拟；
14. Agent-to-Agent (A2A) ：谷歌在 2025 年引入了代理到代理（A2A）协议，这是一个提议的标准，旨在实现不同框架和供应商之间代理的无缝互操作性。该协议围绕五个核心原则构建：拥抱自主能力、基于现有标准构建、默认安全交互、支持长期运行任务、确保模态无关性。这些指南旨在为响应式、可扩展的自主基础设施奠定基础

#### 重要观点：
1. 随着领域从生成式代理向越来越自主的代理式 AI 系统发展，明确 AI 代理和代理式 AI 之间的技术和概念边界变得至关重要。虽然这两种范式都基于 LLMs 并扩展了生成系统的能力，但它们体现了根本不同的架构、交互模型和自主程度。AI 代理通常被设计为单一实体系统，通过利用外部工具、应用顺序推理以及整合实时信息来执行目标导向的任务，以完成定义明确的功能。相比之下，代理式 AI 系统由多个专业化的代理组成，这些代理在更广泛的流程中协调、沟通并动态分配子任务，以实现共同目标。这种架构差异突出了在可扩展性、适应性和应用范围上的明显且重要的区别。理解并形式化这两种范式（AI Agents 和 Agentic AI）之间的分类学具有科学意义，原因如下。首先，它通过使计算框架与问题复杂性相一致，从而实现更精确的系统设计，确保 AI Agents 用于模块化、工具辅助的任务，而 Agentic AI 则用于协调多智能体操作。此外，它还允许进行适当的基准测试和评估：为执行单个任务而设计的智能体与为完成复杂、协调任务而设计的分布式智能体系统，在性能指标、安全协议和资源需求上存在显著差异。此外，清晰的分类学通过防止在设计原则上的误用（例如，在为单智能体执行设计的系统中假设智能体间协作）来减少开发效率低下。如果没有这种清晰度，开发者和从业者将面临低估需要智能体协调的复杂场景的风险，以及过度设计可以用单个 AI Agent 解决的简单应用的风险。
2. 
