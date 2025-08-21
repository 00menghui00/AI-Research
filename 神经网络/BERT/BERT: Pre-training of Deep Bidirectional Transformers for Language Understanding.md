# BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding(2018)
- 论文地址：https://ar5iv.labs.arxiv.org/html/1810.04805?_immersive_translate_auto_translate=1
- 代码仓库：https://github.com/google-research/bert

## 概述：
1. BERT与GPT本质差异：BERT使用双向自注意力，可以捕捉完整信息。
2. BERT引入了两个特殊标记：[CLS] (Classification Token)和[SEP] (Separator Token)，[CLS]是整个输入序列的“总代表”或“摘要官”。BERT 在经过复杂的计算后，会将关于整个序列的综合信息都汇集到这个 [CLS] 标记上。所以当需要对整个句子做一个判断时，只要看这个“总代表”的最终状态就行了。[SEP] 就像是句子之间的“分隔符”。如果输入是“问题”和“段落”，BERT 就会看到这样的格式：[CLS] 问题 [SEP] 段落 [SEP]。这能帮助 BERT 明确地知道哪里是第一句话的结尾，哪里是第二句话的开头。
