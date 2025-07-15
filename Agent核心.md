# Introduction to AI Agents
文章地址：https://www.promptingguide.ai/agents/introduction

## What is an Agent

## Why build with Agents

## Common Use Cases for AI Agents

## Agent Components

### Planning: The Brain of the Agent
任何有效的 AI 代理的核心是其**规划能力**，由大型语言模型（LLMs）提供支持。如果没有强大的规划能力，代理无法有效地自动化复杂任务。现代 LLMs 能够实现多种关键的规划功能：

- 通过思维链推理进行任务分解

- 对过去的行为和信息进行自我反思

- 通过自适应学习改进未来的决策

- 当前进展的批判性分析

### Tool Utilization: Extending the Agent's Capabilities
第二个关键组件是代理与外部工具交互的能力。一个设计良好的代理不仅需要有各种工具的访问权限，还必须理解何时以及如何适当地使用它们。工具使代理能够执行其计划的操作，将抽象策略转化为具体结果。LLM **理解工具选择和时机的能力**对于有效处理复杂任务至关重要。常见的工具包括：
- 代码解释器和执行环境
- 网络搜索和抓取工具
- 数学计算器
- 图像生成系统

### Memory Systems: Retaining and Utilizing Information
第三个关键组件是内存管理，记忆系统允许代理存储和检索从外部工具收集的信息，从而实现迭代改进并基于先前的知识进行构建。它主要有两种形式：
#### Short-term (Working) Memory
- 作为即时上下文的缓冲器
- 支持情境学习
- 足以完成大多数任务
- 有助于在任务迭代过程中保持连续性

#### Long-term Memory 
- 通过外部向量存储实现
- 支持快速检索历史信息
- 对未来任务完成具有重要价值
- 较少实施但未来发展可能至关重要的

---
---
---


# agent 要素
一般来说agent有三大核心要素，一是架构，二是反馈信息，三是prompt

## 架构：
- react
- rethink
- debate
- 角色扮演（分工合作）
  1. 串联合作：分为上下游
  2. 并联合作
  3. 串并联合作（类似神经网络）

## 反馈信息：
- 因为transformer是自回归模型，一旦输出下一个token之后就无法改动了，不可避免会有误差积累，可以通过做递归（或者Cot，Reasoning）缓解这种问题，核心是把输出重新作为输入使得模型可以不断看到自己过去的输出，这样才能更改错误的内容，现在大家做RL reasoning，其实就是把这个能力最大化。
- 反馈信息很重要否则llm就和瞎子一样，为什么现有的大模型画图能力能力很差呢就是因为大模型看不到自己画的图长什么样缺乏反馈进而做调整。最新的llm数学推理能力已经非常强了，但是画图仍然不太行，就是这方面训练语料的匮乏。让llm能充分得到和利用反馈信息很重要。

## prompt：
最近有paper研究发现rethink，debate，角色扮演这些乱七八糟的各种架构带来的提升，都不是很显著，最显著的就是prompt，prompt的设计对效果的影响非常明显。

--------
--------
--------
--------
# agent研究（Chatgpt生成）
本质上，现代 LLM agent 的发展正从单一的“生成式”角色，向“自我反思+协同决策”方向演进：在**架构**层面涌现了多种混合式范式（如 ReAct、Debate、Reflexion、ARTIST 等），在**反馈信息**层面强化了“语言化反馈+记忆”机制以对抗自回归模型的误差累积（Reflexion 等），而在**prompt**层面，多项对比实验表明，恰当的提示工程仍是性能提升的最关键因素（Rethink 等）。下文将围绕这三大要素，结合最新文献做详细梳理。

## 一、架构（Architecture）

### 1. ReAct：交替推理与行动

ReAct 将链式思考（Chain-of-Thought）与外部行动（如调用 API、检索知识库）在同一对话中交替执行，以提高决策灵活性与可解释性([arXiv][1])。在 HotpotQA、Fever 等 QA 任务中，ReAct 通过调用维基 API 来纠正链式思考的错误，显著降低了幻觉与误差传播([arXiv][2])。

### 2. Reflexion：语言化的强化学习

Reflexion 提出了一种“语言反思”框架，代理人在每次尝试后生成对自身行为的反思（reflective text），并将其存入记忆库以影响后续决策，无需修改模型权重即可实现强化学习效果([arXiv][3], [GitHub][4])。实验显示，Reflexion 在一系列交互式任务中显著提升了成功率。

### 3. Debate：多轮辩论合成答案

多 Agent Debate 框架让多个模型实例各自提出回答并互相质疑、辩论，通过轮次合成最终结论。该方法对数学与策略推理任务有明显助益，能在多轮次中筛除逻辑漏洞([arXiv][5], [composable-models.github.io][6])。

### 4. Rethink / Single-Agent 最优提示

最新研究评估了多 Agent 讨论（如并行/串联、交互式）对推理的增益，结果表明，**精心设计的单 Agent 强提示**可达到与最佳多 Agent 框架相当的性能，提示工程仍是最经济高效的提升路径([arXiv][7], [aclanthology.org][8])。

### 5. 角色扮演（分工合作）

* **串联合作（上下游）**：将任务拆分成多个子角色（如“问题理解者→检索者→回答者”）顺序执行。
* **并联合作**：不同角色同时处理子任务，再汇总输出。
* **串并联混合**：类似神经网络多层连接，将并行与串行机制结合，以提高模块化与可扩展性。
  对此，最新的角色扮演综述系统性回顾了从早期 Persona 模型到复杂角色扮演代理的演进，以及在情感陪伴、游戏 AI、数字分身等领域的应用([arXiv][9], [arXiv][10])。

### 6. ARTIST：Agentic Reasoning 与工具集成

ARTIST（Agentic Reasoning and Tool Integration in Self-improving Transformers）提出将 RL 与工具调用在同一框架中联合训练，代理人能自主决定何时、如何调用外部工具，并通过基于结果的强化学习不断优化决策策略([arXiv][11])。

### 7. Agent 系统化调研

最新的 LLM Agent Survey 综合了解耦式体系结构、协作机制与演进路径，为研究者提供了从范式、算法到应用的全景指南([arXiv][12])。

## 二、反馈信息（Feedback Information）

LLM 的自回归机制会导致早期生成的错误在后续不断放大，无反馈时代理犹如“盲行”。以下方法正针对这一核心挑战展开：

### 1. 语言化反馈与记忆

Reflexion 利用“verbal RL”手段，将反馈以文本形式表达，并写入记忆缓冲区，为下次推理提供“回顾”和“甄别”机会，加强长期一致性([arXiv][3], [promptingguide.ai][13])。

### 2. 自我迭代（Self-Refine）

虽尚无发表论文专门命名该机制，但 OpenAI 的“Self-Refine”实例已在实践中证明：模型将自身输出作为新一轮输入，结合目标指令进行修正，从而显著提升数学推理与代码生成的准确率。

### 3. 工具调用与外部验证

ARTIST 框架中，代理人可以在推理过程中调用计算、检索、可视化等工具，并根据工具反馈调整后续思路，减少纯文本推理的盲目性([arXiv][11])。

### 4. 多轮交互与监督

在一些对话式 agent（如 BabyAGI、AutoGPT）中，代理会不断检查目标完成度并生成新的子任务列表，形成“目标→分解→执行→反馈→调整”闭环，提高任务完成质量([understandingai.org][14])。

## 三、Prompt（提示工程）

尽管多 Agent 架构持续涌现，实证研究多次表明：**Prompt 设计依旧是提升性能最显著、成本最低的手段**。关键趋势包括：

### 1. Few-Shot 与 Chain-of-Thought

通过少示例（few-shot）与链式思考（CoT）指令，引导模型显式生成推理步骤，极大改善复杂问题的准确度([arXiv][1])。

### 2. 动态提示（Dynamic Prompting）

将中间推理或外部反馈拼接回 prompt，实现逐步强化；或利用模板化、分层提示来控制角色和流程，提高长文本任务的连贯性和一致性([react-lm.github.io][15])。

### 3. 对比实验

Rethink 等研究系统地对比了多种提示策略，结果表明，通过“角色设定+分阶段指令”即可在不增加模型数量的情况下，达到与 Debate 等多 Agent 方法相当的效果([arXiv][7], [aclanthology.org][8])。

### 4. Prompt 池与自动搜索

最新工作开始探索使用强化学习或遗传算法自动搜索最优提示，以减少手动调参成本并提高跨任务泛化能力。

---

**参考文献（部分）**

* Shunyu Yao et al., “ReAct: Synergizing Reasoning and Acting in Language Models,” 2022 ([arXiv][1], [arXiv][2])
* Noah Shinn et al., “Reflexion: Language Agents with Verbal Reinforcement Learning,” 2023 ([arXiv][3], [GitHub][4])
* Improving Factuality and Reasoning via Debate Methods, 2023 ([arXiv][5], [composable-models.github.io][6])
* XXX et al., “Rethinking the Bounds of LLM Reasoning: Are Multi-Agent Discussions Beneficial?” 2024 ([arXiv][7], [aclanthology.org][8])
* “From Persona to Personalization: A Survey on Role-Playing LLM Agents,” 2024 ([arXiv][9], [arXiv][10])
* ARTIST: Agentic Reasoning and Tool Integration, 2025 ([arXiv][11])
* “Large Language Model Agent: A Survey on LLM Agent Systems,” 2025 ([arXiv][12])
* “BabyAGI and AutoGPT: Agentic Behavior via Repeated Prompting,” 2025 ([understandingai.org][14])

[1]: https://arxiv.org/abs/2210.03629?utm_source=chatgpt.com "ReAct: Synergizing Reasoning and Acting in Language Models"
[2]: https://arxiv.org/pdf/2210.03629?utm_source=chatgpt.com "[PDF] ReAct: Synergizing Reasoning and Acting in Language Models - arXiv"
[3]: https://arxiv.org/abs/2303.11366?utm_source=chatgpt.com "Reflexion: Language Agents with Verbal Reinforcement Learning"
[4]: https://github.com/noahshinn/reflexion?utm_source=chatgpt.com "[NeurIPS 2023] Reflexion: Language Agents with Verbal ... - GitHub"
[5]: https://arxiv.org/abs/2305.14325?utm_source=chatgpt.com "Improving Factuality and Reasoning in Language Models ... - arXiv"
[6]: https://composable-models.github.io/llm_debate/?utm_source=chatgpt.com "Improving Factuality and Reasoning in Language Models with ..."
[7]: https://arxiv.org/abs/2402.18272?utm_source=chatgpt.com "Rethinking the Bounds of LLM Reasoning: Are Multi-Agent ... - arXiv"
[8]: https://aclanthology.org/2024.acl-long.331/?utm_source=chatgpt.com "Rethinking the Bounds of LLM Reasoning: Are Multi-Agent ..."
[9]: https://arxiv.org/abs/2404.18231?utm_source=chatgpt.com "From Persona to Personalization: A Survey on Role-Playing ... - arXiv"
[10]: https://arxiv.org/html/2407.11484v4?utm_source=chatgpt.com "The Oscars of AI Theater: A Survey on Role-Playing with Language ..."
[11]: https://arxiv.org/html/2505.01441v1?utm_source=chatgpt.com "Agentic Reasoning and Tool Integration for LLMs via Reinforcement ..."
[12]: https://arxiv.org/abs/2503.21460?utm_source=chatgpt.com "[2503.21460] Large Language Model Agent: A Survey on ... - arXiv"
[13]: https://www.promptingguide.ai/techniques/reflexion?utm_source=chatgpt.com "Reflexion | Prompt Engineering Guide<!-- -->"
[14]: https://www.understandingai.org/p/reinforcement-learning-explained?utm_source=chatgpt.com "Reinforcement learning, explained with a minimum of math and jargon"
[15]: https://react-lm.github.io/?utm_source=chatgpt.com "ReAct: Synergizing Reasoning and Acting in Language Models"

----
----
----
----


# LLM 智能体研究架构（Chatgpt DeepResearch）

* **ReAct（交替推理与行动）**：该范式让模型在同一对话中交替生成链式推理（Chain-of-Thought）文本和外部行动指令（如 API 调用、网页检索等），从而实现推理过程的灵活修正。ReAct 的基本思想是用推理轨迹指导行动方案的更新，用行动反馈丰富推理输入。例如，在 HotpotQA、FEVER 等复杂问答任务中，ReAct 通过调用维基百科 API 获取外部知识，及时纠正链式思考的错误，显著降低了幻觉现象和误差传播。实验显示，ReAct 在交互式决策（如 ALFWorld、WebShop）任务上优于传统的模仿学习和强化学习方法，成功率提升可达 34% 以上。

* **Reflexion（语言化反思）**：该框架提出用“语言反馈”代替模型权重更新来强化智能体行为。每轮尝试后，模型对自己的轨迹进行文字化反思，并将反思结果写入记忆库，为后续决策提供历史“回顾”依据。Reflexion 在不训练模型参数的前提下，通过内部自评和记忆逐步优化行为，显著提高了多步交互任务的完成率。以编程任务为例，Reflexion 在 HumanEval 代码测试上将 GPT-4 的通过率从 80% 提升到 91%。提示工程指南也总结了其优势：在 AlfWorld、HotpotQA 等任务中，引入反思后模型表现均显著优于基线。

* **多轮辩论（Debate）**：该方法让多个模型实例分别提出候选答案，并相互质疑辩论，通过多轮讨论筛选和生成最终结论。这种“思想市场”式的机制可以将不同实例的推理过程公开展示，从而排查单一模型可能出现的逻辑漏洞。实验证明，多代理辩论能显著增强数学推理和战略推理能力，同时提高生成内容的事实正确性，减少虚假答案和幻觉。该思路无需修改基础模型，也可直接套用在现有大型模型上，通过约束相同的提示和流程即可实施辩论评估。

* **Rethink（单模型优化提示）**：最新研究重新审视了多智能体讨论（并行、串联或混合）对推理性能的增益。结果发现，通过**精心设计的单模型提示**，往往可达到与最佳多智能体框架相当的效果。具体地，在带有示例演示（few-shot）时，单一强提示模型的性能几乎与多轮讨论模型不相上下；只有在无演示提示下，多智能体讨论才略胜一筹。这表明，与其构建复杂的多模型系统，不如投资于构造合理的角色设定和分阶段指令，仍是最经济高效的提升策略。

* **角色分工（分串联与并联）**：为提高任务可解释性和模块化，研究者探索将复杂任务拆分给不同子角色协作处理。一种常见做法是**串联合作（Pipeline）**：如“问题解析→知识检索→答案生成”的顺序流程，每个角色专注于子步骤；也有**并行合作**：多个角色同时处理同一问题的不同方面，再汇总各自的产出；或者两者混合，如多层并行后串联决策。最新综述系统性回顾了角色扮演智能体的发展历程与应用场景，涵盖从早期简单 Persona 模型到当前可在情感陪伴、游戏 AI、数字分身等领域中模拟复杂人物角色的高级代理。这些工作强调了在指令中明确分工和人格设定的重要性，以增强智能体的专业化与泛化能力。

* **ARTIST（Agentic Reasoning and Tool Integration）**：该框架首次提出将**强化学习**与工具调用联合训练，让模型自主决定何时、如何使用外部工具。在 ARTIST 中，代理人在多轮推理过程中可以调用计算、检索、可视化等工具，并根据工具返回的结果通过基于结果的 RL 不断优化决策策略。实验证明，在数学推理和多轮函数调用等任务上，ARTIST 代理相较基线模型可获得高达 22% 的绝对提升；深入分析显示，RL 驱动下的代理能实现更深入的推理和更有效的工具利用。该工作开辟了将代理训练（learning-to-plan）与工具使用（tool-augmented reasoning）紧密结合的研究新方向。

* **智能体体系结构调研**：2025 年最新发表的综合性综述系统梳理了大型语言模型智能体的设计范式和协作机制。该工作从方法论维度出发，对比了不同架构基础、协同方案与演进路径，并探讨了评估方法、应用领域及现实挑战。该 survey 包括近 329 篇论文，给出结构化分类，帮助研究者理解各种智能体设计原则之间的联系，为未来智能体架构研究指明了方向。

## 二、反馈信息

* **语言化反馈与记忆**：针对自回归生成易放大早期错误的问题，引入**语言反馈机制**成为新趋势。Reflexion 框架提出使用“语言反思”取代显式学习：代理在每次交互后将收到的反馈总结成自然语言文本（reflective text），并将其写入长期记忆。下次推理时，模型可回顾历史反思内容，以识别和避免此前犯过的错，从而提高长期一致性和学习效率。比如，采用 Reflexion 的代理在复杂任务中能显著加快错误纠正速度，在人类评测和自动指标上均显著优于传统不具备记忆的链式思考方法。

* **自我迭代（Self-Refine）**：这一思路让模型将自身输出作为新的输入，结合目标指令不断修改答案。相关研究表明，利用自我反馈的迭代优化过程，可以在无需额外训练数据或模型微调的情况下显著提升结果质量。例如，“Self-Refine”方法对 ChatGPT/GPT-4 给出的初始答案进行自评和修改，在数学推理与代码生成任务上平均性能提升约 20%。这种纯提示级的迭代机制已被广泛用于增强模型精度和鲁棒性。

* **工具调用与外部验证**：引入外部工具和验证机制，可有效降低单纯文本推理的盲目性。ARTIST 框架让代理可以在推理链中随时调用计算器、搜索引擎、代码执行等工具，并根据工具输出调整后续推理策略。类似地，OpenAI 在 “深度检索 (Deep Research)” 代理中训练模型自主浏览网页和使用 Python 工具，对复杂问题进行查证和计算。通过异步调用外部工具并反馈结果，这些系统能够主动验证答案，从而大幅减少基于模型内知识的错误。

* **多轮交互与监督**：一些对话式智能体系统（如 BabyAGI、AutoGPT）在完成任务过程中形成闭环：代理会持续监控目标完成情况，并根据需要生成新的子任务列表，然后循环执行。例如，BabyAGI 框架最初让 GPT-4 根据目标自动生成 To-Do 列表，之后逐步执行任务并更新列表。但正如实验证明的那样，这种连环提示容易导致错误累积：GPT-4 在执行过程中如果在早期出现失误，后续推理往往无法自动纠正，最终导致整个任务失败。这一现象凸显了监督反馈和错误修正对长期任务迭代的重要性，也促使研究者探索更可靠的自我评估和校正机制。

## 三、提示工程（Prompt Engineering）

尽管多智能体架构不断涌现，实证研究反复证实：**有效提示设计仍是提升性能最直接、成本最低的途径**。关键趋势包括：

* **少样本与链式思考**：通过在提示中添加示例（few-shot）并要求模型输出中插入推理步骤（CoT），可显著提高复杂推理题的答题正确率。这类方法让模型显式展现思考过程，从而提高算术、逻辑推理和常识推断的准确度。许多研究和实战证明，在需要多步推理的场景中，连贯的链式思考提示能有效降低模型犯错。

* **动态提示**：在对话或多步骤任务中，将中间推理结果或外部反馈动态地拼接回提示，能持续强化模型的执行过程。例如，代理可在收到工具返回后将其结果附加到新提示中以修正下一步思路。此外，利用模板化和分层式提示来控制角色身份和流程，也有助于维护长文本任务的连贯性和一致性。

* **对比实验与单模型最优提示**：如前所述，多项研究（如 Rethink 等）表明：通过“角色设定 + 分阶段指令”的设计，同一个模型在多步推理任务上即可达到与多智能体讨论类似的性能。这进一步验证了提示工程的重要性——与其部署多个模型，不如投入精力打磨单模型提示。

* **自动提示优化**：最新工作开始探索**自动搜索最佳提示**以减少手动调参。有研究提出将提示搜索视为强化学习问题，例如 StablePrompt 通过在线 RL（Adaptive PPO）平衡稳定性与搜索空间，自主生成高质量提示。在多项分类、问答和文本生成任务上，StablePrompt 都取得了优于传统提示的效果。未来，这类自动化提示优化方法将进一步提升提示设计的效率和泛化能力。

## 对比分析表

以下表格总结了上述代表性方法的核心特性、应用场景与实验效果：

| 方法                                       | 核心创新                                    | 应用场景与效果                                                                                                  | 参考文献 |
| :--------------------------------------- | :-------------------------------------- | :------------------------------------------------------------------------------------------------------- | :--- |
| **ReAct** (Yao et al., 2022)             | 在同一次对话中交替生成链式推理文本和行动指令，模型可动态调用外部知识源     | 在复杂 QA 和交互决策任务中，通过调用维基百科 API 等外部工具纠正推理错误，大幅降低幻觉发生，准确率显著提升                                                |      |
| **Reflexion** (Shinn et al., 2023)       | 引入“语言化反馈”：代理将每次失败的原因口头总结并存入记忆，对后续决策提供反馈 | 适用于需要反复试验的任务，如 sequential decision-making、编程测试等。实验中 Reflexion 在 HumanEval 代码评测中将 GPT-4 的通过率从 80% 提升到 91% |      |
| **Multi-Agent Debate** (Du et al., 2023) | 多个模型实例分别给出答案并轮流辩论，通过对抗讨论提高推理质量          | 在数学、策略推理和开放式生成中表现优异：可显著提升数学题目正确率，减少错误和虚假信息输出                                                             |      |
| **Self-Refine** (Madaan et al., 2023)    | 让模型自我评价并迭代改进输出，无需额外训练数据或模型微调            | 广泛应用于文本优化、代码生成、数学推理等多种任务：研究表明，Self-Refine 平均可提升约 20% 的任务性能                                               |      |
| **Rethink（强提示单模型）** (Wang et al., 2024)  | 通过设计强提示（角色设定 + 分阶段指令）来增强单模型推理能力         | 多种推理任务：单一模型凭借精心构造的提示，在有示例的情况下能达到多模型讨论方法的性能；在无示例时，多模型略优                                                   |      |
| **ARTIST** (Singh et al., 2025)          | 将 RL 和工具调用联合训练，使模型自主决定何时使用何种外部工具        | 主要测试在数学和多轮函数调用任务：ARTIST 在这些基准上实现了最高可达 22% 的绝对性能提升，表明其有效促进更深层次的推理和工具利用                                    |      |

**注**：以上方法在实际应用中可相互结合，例如结合 ReAct 的推理+工具思路与 Reflexion 的记忆反馈机制，可以进一步提升智能体的表现。此外，许多开源框架（如 LangChain、AutoGPT/BabyAGI 等）已实现类似思路，可供研究者在真实系统中验证这些策略的效果。

----
----
----
----
----

# AI Agent 前沿研究洞察（manus生成）

## 核心要素一：架构 (Architecture)

Agent 的架构决定了其思考、决策和行动的方式。单一的 LLM 调用已无法满足复杂任务的需求，因此研究者们设计了多种高级架构来增强 Agent 的能力。

### 1. 单 Agent 架构 (Single-Agent Architecture)

这类架构由单个 LLM 作为核心驱动，通过迭代循环来完成任务。

*   **ReAct (Reason + Act)**: 这是目前最基础且应用最广泛的 Agent 架构之一。它将思维链 (Chain of Thought) 与实际行动相结合。 Agent 首先进行**推理**（Thought），生成一个行动计划，然后执行**行动**（Action），例如调用一个工具（如搜索引擎、计算器）。 接着，它会**观察**（Observation）行动的结果，并将结果作为新的输入，进行下一轮的“思考-行动”循环，直到任务完成。 这种交错进行推理和行动的模式，使得 Agent 能够动态调整计划并处理复杂任务。
*   **ReThink/Reflexion (反思/反射)**: 这类架构在 ReAct 的基础上增加了**自我反思和修正**的能力。在执行完一个或一系列动作后，Agent 会对自己的行为轨迹和结果进行评估。如果发现错误或有更优路径，它会生成一段“反思”文本，并在后续的规划中利用这些反思来修正自己的行为。 这极大地提升了 Agent 从失败中学习并提高任务完成率的能力。
*   **Pre-Act (预行动)**: 作为 ReAct 的一种演进，Pre-Act 强调在行动前进行更详尽的**多步规划和推理**。 它会预先生成一个包含多个步骤的执行计划，并在每一步执行后，根据新的工具输出增量式地更新和完善后续计划，直到任务完成。 实验表明，这种方法在任务导向的 Agent 中，尤其是在使用经过微调的较小模型时，能显著提升动作的准确率和最终目标的完成率。
*   **LATS (Language Agent Tree Search)**: 该架构将 Agent 的探索过程构建成一棵树。每个节点代表一个思考或行动的状态。Agent 会在树中进行探索，利用 LLM 对不同路径（未来的可能性）进行评估和展望 (foresight)，并根据评估结果进行剪枝，从而更高效地找到最优解。

### 2. 多 Agent 架构 (Multi-Agent Architecture)

当单个 Agent 的能力达到瓶颈时，引入多个 Agent 进行协作成为必然趋势。 多 Agent 系统通过分工、辩论和协作，能够解决远超单个 Agent 能力的复杂问题。

*   **角色扮演 (分工合作)**: 这是最直接的多 Agent 模式。不同的 Agent 被赋予不同的角色和专长（例如，一个 Agent 负责规划，一个负责执行，一个负责审查）。
    *   **串联合作 (Hierarchical/Supervisor-Worker)**: 类似公司管理结构，一个“主管” Agent 负责将复杂任务分解成子任务，然后分配给多个“工人” Agent 并行处理。 主管 Agent 负责监督进度和整合结果。这种架构扩展性强，性能稳定，即使增加不相关的工具或领域，也不易出现性能下降。
    *   **并联合作 (Debate/Critique)**: 多个 Agent 针对同一个问题，从不同角度独立提出解决方案。 然后，它们进入一个“辩论”环节，相互审视和批判对方的方案，并根据辩论结果迭代优化自己的答案，最终通过投票或由一个“裁判” Agent 选出最佳方案。 这种方式能有效减少单个模型的偏见和知识盲点，提升结果的准确性和鲁棒性。
    *   **混合架构 (Mixture-of-Agents)**: 结合了多种协作模式，例如，一个系统可能包含多个并行的专业团队（每个团队内部又是串联结构），共同完成一个宏大项目。 Ema 公司的“生成式工作流引擎”就是一个例子，它能将大任务分解，智能地分配给多个 Agent，并动态监控和调整。

## 核心要素二：反馈信息 (Feedback)

反馈是 Agent 学习、适应和自我修正的关键。没有反馈，Agent 就像一个静态的程序，无法纠正错误或适应新情况。

*   **内部反馈 (Intrinsic Self-Correction)**: 这是指 Agent 在不借助外部工具或人类的情况下，仅凭自身能力进行反思和修正。
    *   **工作原理**: 通过特定的提示词引导，LLM 可以对自己的输出进行验证和打分。例如，在完成一次推理后，可以要求模型：“请检查你之前的回答，一步一步地分析是否存在逻辑错误。”
    *   **当前研究现状**: 关于 LLM 是否真的具备有效的内在自我修正能力，学术界存在争议。一些早期研究持悲观态度，认为 LLM 尚不具备可靠的自我修正能力，有时修正后效果甚至更差。 然而，更新的研究发现，在**零温度（zero temperature）采样**和**公平提示（fair prompts）**等特定条件下，LLM 确实能展现出一定程度的、虽然微小但持续的性能提升。 最近，一种名为 **ProCo** 的框架通过“掩盖关键条件再预测”的方式，显著增强了模型在没有外部反馈的情况下识别和纠正错误的能力。

*   **外部反馈 (External Feedback)**: 这是指 Agent 从外部环境、工具或人类那里获取的信息。
    *   **工具反馈**: 这是最常见的外部反馈。Agent 调用 API 或执行代码后，会得到一个返回结果（成功、失败、错误信息、数据等）。 Agent 必须理解这些反馈，以决定下一步行动。例如，代码执行失败的报错信息就是一种强烈的负反馈，指导 Agent 修正代码。
    *   **环境反馈**: 在与虚拟或物理环境交互时，环境的状态变化就是一种反馈。例如，在玩游戏时，得分增加是正反馈，生命值减少是负反馈。
    *   **人类在环 (Human-in-the-Loop)**: 在关键节点或当 Agent 遇到困难时，可以暂停并请求人类输入。 这种高质量的反馈能有效校准 Agent 的方向，确保结果与用户意图一致。
    *   **模型反馈**: 在多 Agent 系统中，一个 Agent 的输出可以成为另一个 Agent 的输入和反馈。 例如，一个“审查” Agent 可以对“写作” Agent 的草稿提供修改意见。

## 核心要素三：提示 (Prompt) / 上下文工程 (Context Engineering)

你提到的“prompt 设计对效果影响最明显”的观点非常正确，并且这个概念正在演进为一个更广阔的领域：**上下文工程 (Context Engineering)**。

*   **从 Prompt Engineering 到 Context Engineering**:
    *   **Prompt Engineering** 侧重于设计单次输入的指令文本。
    *   **Context Engineering** 则是一个更宏大的概念，它指的是**设计和构建一个动态系统，该系统能在恰当的时机，以恰当的格式，为 LLM 提供解决任务所需的一切信息和工具**。 失败的 Agent 任务，很多时候不是模型能力的失败，而是上下文供给的失败。

*   **上下文的关键组成部分**:
    1.  **系统指令 (System Prompt)**: 定义 Agent 的核心身份、目标、行为准则和高级别工作流程。这是 Agent 的“宪法”。
    2.  **任务指令 (User Prompt)**: 用户下达的具体任务。
    3.  **工具与文档 (Tools & Documentation)**: 清晰地定义 Agent 可以使用的工具（API、函数），并提供详尽的说明文档。工具设计的优劣直接决定 Agent 的能力上限。
    4.  **记忆 (Memory)**: 包括短期记忆（当前对话历史）和长期记忆（跨对话的知识库），让 Agent 能够持续学习和保持个性化。
    5.  **检索增强生成 (RAG)**: 通过从外部知识库检索相关信息，为 Agent 提供解决问题所需的实时、准确的数据，这是上下文供给的核心技术之一。
    6.  **示例 (Examples)**: 在 Prompt 中提供一两个高质量的“思考-行动”范例（即 Few-shot Prompting），能极大地帮助模型理解任务要求和输出格式。

*   **前沿研究趋势**:
    *   **提示词与架构的协同进化**: 最新的研究不再将 Prompt 和架构割裂开看。例如，多 Agent 辩论的成功，很大程度上依赖于为每个辩论角色精心设计的 Prompt。
    *   **自动化上下文选择**: 未来的研究方向之一是让 Agent 能够根据任务自动从庞大的可用工具和知识库中，智能地选择最相关的上下文注入到其“工作记忆”中，避免无关信息造成的“上下文过载”。

总而言之，AI Agent 的前沿研究正朝着**更精巧的协作架构、更闭环的反馈机制、以及更智能的上下文供给**这三个方向深度发展。这三者相辅相成，共同构成了通往更强大、更自主的通用人工智能的基石。
