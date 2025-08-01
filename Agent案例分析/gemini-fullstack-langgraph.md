# gemini-fullstack-langgraph-quickstart 谷歌开源的一个deepresearch示例架构
由于谷歌调用自家大模型gemini的web检索功能，自己如果想构建deepresearch的网络检索功能需要重构，不同的大模型对于网络检索功能调用方式不同

## 架构分析：使用langgraph框架
### 节点
- generate_query: 规划节点 - 负责理解用户意图并生成初步的搜索策略。
- web_research: 执行节点 - 负责执行搜索并初步处理信息。
- reflection: 反思节点 - 负责评估现有信息，发现知识缺口。
- finalize_answer: 总结节点 - 负责整合所有信息，生成最终报告。
### 边
- 普通边 (add_edge): 定义了固定的、单向的流程，例如搜索 (web_research) 之后必然是反思 (reflection)。
- 条件边 (add_conditional_edges): 实现了智能体的“决策”能力。它会根据某个节点的输出（例如reflection的结果）来决定下一步走向何方，从而实现循环。
