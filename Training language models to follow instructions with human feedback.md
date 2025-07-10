# Training language models to follow instructions with human feedback
论文地址：https://ar5iv.labs.arxiv.org/html/2203.02155?_immersive_translate_auto_translate=1

------------
------------
------------

## 引入
- 扩大语言模型的规模并不必然使其更好地理解用户意图。例如，大型语言模型可能会生成不真实、具有攻击性或对用户毫无帮助的输出。换句话说，这些模型与用户意图并不一致。
- 通过人工构建基础数据集对基础模型进行监督训练，然后通过人类打分排序的数据集对上一步训练好的模型进一步训练得到奖励模型。随后使用强化学习策略，让 InstructGPT 模型不断地生成答案，并由这个“奖励模型裁判”实时打分。InstructGPT 的目标就是调整自己，尽量生成能让裁判打出高分的答案。（监督学习、强化学习）
