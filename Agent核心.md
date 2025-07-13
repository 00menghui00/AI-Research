# agent
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
