# gemini-fullstack-langgraph-quickstart 谷歌开源的一个deepresearch示例架构
由于谷歌调用自家大模型gemini的web检索功能，自己如果想构建deepresearch的网络检索功能需要重构，不同的大模型对于网络检索功能调用方式不同

## 架构分析：使用langgraph框架


### 一、 核心架构：基于LangGraph的“反思-行动”循环图 (ReAct Loop Graph)

这个项目的后端架构不是一个简单的“输入->处理->输出”线性流程，而是一个**有状态的、循环的图（Stateful, Cyclic Graph）**。其设计哲学模仿了人类专家进行深度研究的过程：**初步探索 -> 综合反思 -> 发现不足 -> 补充研究 -> ... -> 最终总结**。

这个图的核心由以下几个部分构成：

1.  **状态对象 (State Object - `OverallState`)**: 这是整个研究任务的“中央数据库”或“工作台”。它是一个Python字典（TypedDict），在整个流程中传递和更新，包含了所有关键信息：
    *   `messages`: 用户的原始问题。
    *   `search_query`: 生成的搜索查询词列表。
    *   `web_research_result`: 每次网页搜索后LLM生成的摘要。
    *   `sources_gathered`: 收集到的所有引用来源（URL和标题）。
    *   `research_loop_count`: 当前进行了多少轮研究。
    *   `is_sufficient`: 一个布尔值，标记当前信息是否足以回答问题。
    *   ...等等。

2.  **节点 (Nodes)**: 每个节点都是一个Python函数，代表研究过程中的一个具体**行动（Action）**。
    *   `generate_query`: **规划节点** - 负责理解用户意图并生成初步的搜索策略。
    *   `web_research`: **执行节点** - 负责执行搜索并初步处理信息。
    *   `reflection`: **反思节点** - 负责评估现有信息，发现知识缺口。
    *   `finalize_answer`: **总结节点** - 负责整合所有信息，生成最终报告。

3.  **边 (Edges)**: 边定义了节点之间的流转关系，分为两种：
    *   **普通边 (`add_edge`)**: 定义了固定的、单向的流程，例如搜索 (`web_research`) 之后必然是反思 (`reflection`)。
    *   **条件边 (`add_conditional_edges`)**: 实现了智能体的“决策”能力。它会根据某个节点的输出（例如`reflection`的结果）来决定下一步走向何方，从而实现循环。

### 二、 工作流程与技术细节详解


#### **第1步: 规划 - 生成初始搜索查询 (`generate_query`)**

*   **输入**: 用户的原始问题 (`messages`)。
*   **核心技术**:
    1.  **LLM调用**: 使用 `ChatGoogleGenerativeAI` 初始化一个轻量级、高速度的LLM（如`gemini-1.5-flash-latest`）。
    2.  **结构化输出 (Structured Output)**: 这是关键技术点。通过 `.with_structured_output(SearchQueryList)`，它强制LLM必须返回一个符合 `SearchQueryList` Pydantic模型的JSON对象，而不是自由文本。这保证了输出的查询词列表可以被程序稳定地解析和使用。
    3.  **提示工程 (Prompt Engineering)**: `query_writer_instructions` 这个提示词模板会指导LLM如何生成高质量的查询，比如告诉它当前日期、用户问题，并要求它生成指定数量（`number_of_initial_queries`）的查询。
*   **输出**: 一个包含多个搜索查询字符串的列表，更新到 `OverallState` 的 `search_query` 字段。

#### **第2步: 执行 - 并行化网页搜索 (`web_research`)**

*   **输入**: `generate_query` 节点生成的单个搜索查询。
*   **核心技术**:
    1.  **并行化 (`Send`)**: `continue_to_web_research` 函数利用LangGraph的 `Send` 指令，为上一步生成的**每一个**查询词都启动一个独立的、并行的 `web_research` 任务。这极大地缩短了信息收集时间。
    2.  **原生工具调用 (Native Tool Calling)**: 这里调用的是 `genai_client`（Google的Python客户端），而不是LangChain的封装。因为它能直接访问Gemini模型**内置的`google_search`工具**。在API请求中加入 `"tools": [{"google_search": {}}]`，就授权LLM在需要时自主执行Google搜索。
    3.  **信息溯源与引用 (Grounding & Citation)**: 这是另一个亮点。调用Google原生工具后，返回的结果中包含了 `grounding_metadata`。代码通过 `resolve_urls` 和 `get_citations` 函数解析这些元数据，提取出LLM生成内容所依据的**确切来源URL和网页标题**。
    4.  **引用标记**: `insert_citation_markers` 函数会将 `[1]`, `[2]` 这样的引用标记插入到LLM生成的摘要文本中，实现了内容和来源的精确对应。
*   **输出**: 一段带引用标记的文本摘要和一份详细的来源列表，这些结果会被聚合到 `OverallState` 中。

#### **第3步: 反思 - 评估信息并发现缺口 (`reflection`)**

*   **输入**: 所有并行搜索任务生成的摘要 (`web_research_result`)。
*   **核心技术**:
    1.  **综合分析**: `reflection_instructions` 提示词会要求一个更强大的LLM（`reflection_model`）阅读所有现有摘要。
    2.  **批判性思维**: LLM被要求扮演批判性思考者的角色，回答两个核心问题：
        *   `is_sufficient`: 基于现有信息，能否全面回答用户的原始问题？
        *   `knowledge_gap` & `follow_up_queries`: 如果不能，还缺少哪些信息（知识缺口）？并基于此生成一组新的、更具探索性的**追问查询**。
    3.  **结构化输出**: 同样地，这里使用 `.with_structured_output(Reflection)` 强制LLM返回一个包含布尔值和查询列表的JSON对象，确保决策的可靠性。
*   **输出**: 一个包含决策（是否足够）和新行动计划（追问查询）的结构化对象。

#### **第4步: 决策 - 循环或终止 (`evaluate_research`)**

*   **输入**: `reflection` 节点的输出。
*   **核心技术**: **条件路由 (Conditional Routing)**。这是一个逻辑判断节点，不起到处理作用。
    *   **终止条件**: 如果 `is_sufficient` 为 `True`，或者 `research_loop_count` 达到了预设的最大值 (`max_research_loops`)，流程将被导向 `finalize_answer`。
    *   **循环条件**: 否则，流程将带着 `reflection` 生成的**新查询词**，再次并行地跳转回 `web_research` 节点，开始新一轮的“执行-反思”循环。
*   **输出**: 一个字符串，值为下一个节点的名称（`"finalize_answer"` 或 `"web_research"`）。

#### **第5步: 总结 - 生成最终报告 (`finalize_answer`)**

*   **输入**: 整个流程中积累的所有摘要和来源 (`OverallState`)。
*   **核心技术**:
    1.  **最终整合**: `answer_instructions` 提示词指示一个高质量的LLM（`answer_model`）作为“最终报告撰写人”。
    2.  **综合撰写**: LLM被要求基于提供的所有摘要片段，撰写一份逻辑连贯、结构清晰、内容全面的最终答案。
    3.  **引用还原**: 代码会遍历最终生成的文本，将之前为了节省token而使用的短链接标记替换回完整的原始URL，并整理出去重后的引用列表。
*   **输出**: 一份包含完整答案和规范引用列表的最终报告，作为API的最终响应。

