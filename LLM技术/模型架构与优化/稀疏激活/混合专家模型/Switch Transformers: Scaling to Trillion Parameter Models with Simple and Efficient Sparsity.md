# Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity(2021)
- 论文地址：https://ar5iv.labs.arxiv.org/html/2101.03961?_immersive_translate_auto_translate=1

## 概述：
- 我们简化了 MoE 路由算法，并设计了通信和计算成本更低、更直观的改进模型。
- 我们提出的训练技术缓解了不稳定性问题，并且首次证明了大型稀疏模型可以用较低精度的（bfloat16）格式进行训练。
- Switch Transformers 的指导设计原则是以简单且计算高效的方式最大化 Transformer 模型的参数数量。（规模效应由openai提出，并证明扩大模型比扩充数据更有性价比）
- 我们的假设是，参数数量，无论总计算量如何，都是可单独扩展的重要维度。（我们可以将参数量 (N) 和 单样本计算量 (FLOPs/sample) 进行解耦 (decouple)。我们可以做到让参数量 (N) 疯狂增长，但处理每个样本（token）所需的计算量却保持不变。）
- 分布式训练：我们的工作重点在于 TPU 架构，但这类模型也可以在 GPU 集群上进行类似训练。在我们的分布式训练设置中，我们的稀疏激活层在不同的设备上分割唯一的权重。因此，模型的权重随着设备数量的增加而增加，同时每个设备上的内存和计算开销保持可控。

## MoE架构：
- Expert：现代基于 Transformer 的 MoE 架构（如 Switch Transformer, Mixtral）中，“专家” (Expert) 指的就是一个标准的前馈网络 (Feed-Forward Network, FFN)。
- 稀疏激活：对于每一个传入的 token，系统并不会激活并使用所有的专家 FFN，而是通过一个路由机制（Router），只选择一个或少数几个（例如，Switch Transformer 中只选1个，Mixtral 中选2个）最相关的专家 FFN 来参与计算。

### 针对FFN做稀疏激活的原因：
- 参数量占比高: 在一个标准的 Transformer 模型中，FFN 层的参数量通常占到总参数量的 2/3 甚至更多。因此，对 FFN 进行“稀疏化”是提升模型参数/计算比（即“性价比”）最有效的地方，能带来最大的收益。
- 计算的独立性: FFN 层对序列中的每个 token 是独立计算的。Token "A" 在 FFN 中的计算与 Token "B" 无关。这种天然的独立性使得为每个 token 单独选择专家的“路由”机制变得非常简单和高效，极易并行化。
- 功能上的合理性: 我们可以直观地将不同的专家 FFN 理解为模型学习到的不同“知识领域”或“处理技能”。例如，一个专家可能擅长处理语法结构，另一个专家擅长处理事实性知识，还有一个专家擅长处理代码逻辑。让路由器根据 token 的内容为其分配合适的专家，在功能上非常合理。

