# HERA: Improving Long Document Summarization using Large Language Models with Context Packaging and Reordering
- 论文地址：https://arxiv.org/html/2502.00448?_immersive_translate_auto_translate=1


## 一、Introduction：
- 提高 LLMs 在长文档摘要中性能的关键是如何提取有用信息并按正确的叙述顺序排列。
- 我们提出了 HERA，一个通过上下文打包和重排生成长文档摘要的框架。文档按段落被划分为小文本片段，然后我们检索关于一个事件的片段，并使用 LLM 对它们进行语义排序以形成输入。之后，LLM 使用打包的上下文生成该事件的摘要。最后，将几个重要事件的摘要结合起来作为整个文档的摘要。打包的上下文仅保留与事件相关的內容，防止 LLMs 被无关信息误导，而重排的上下文则促使 LLMs 更准确地理解信息。（先做**检索**然后做排序打包最终生成摘要）

## 二、Approach：
### Context Packaging：
- 段落分割 → 生成句子级 局部摘要。
- LLM 检索和排序 → 找出与事件相关的片段。
- 过长摘要拆分 → 保证检索高效。
- 片段打包 → 构成事件级上下文。

### Context Reordering：
- 使用 NAON 重排片段包 → 保证逻辑顺序。
- 用 LLM 生成事件摘要 → 保留事件核心信息。
- 用 LLM 聚合事件摘要 → 形成完整文档摘要。
