# Research

源码：

---

### **1. 核心架构与算法 (Core Architecture & Algorithms)**
*这是LLM能力上限的决定性因素，其创新通常伴随着新一代模型的诞生。*

*   **注意力机制创新**:
    *   **FlashAttention**: **爆发于 2022年中**。由 Tri Dao 提出的论文《FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness》通过深度硬件优化（利用GPU SRAM）解决了标准注意力机制的显存瓶颈，成为后续所有模型训练和推理框架的标配。
    *   **分组查询注意力 (GQA)**: **爆发于 2023年中**。由论文《GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints》提出，作为多头注意力(MHA)和多查询注意力(MQA)的折中，被 LLaMA 2 采用后迅速普及，成为平衡性能和推理效率的主流选择。
    *   **补充技术 - 滑动窗口注意力 (Sliding Window Attention, SWA)**: **爆发于 2020年 (Longformer) / 2023年 (Mistral)**。早期由 Longformer 提出，但真正由 Mistral 7B (2023) 将其与 RoPE 和 GQA 结合后发扬光大，成为在有限计算资源下实现较长上下文处理的有效方法。

*   **位置编码**:
    *   **RoPE (旋转位置编码)**: **爆发于 2022-2023年**。虽然在2021年由苏剑林提出，但真正引爆社区的是 Meta 的 **LLaMA** (2023年初) 全系列采用并取得巨大成功，使其成为开源LLM的事实标准。
    *   **ALiBi (线性偏差注意力)**: **爆发于 2022-2023年**。由 Press et al. (2022) 提出，因其良好的外推性和简单性被 MPT 系列模型采用，成为 RoPE 之外的另一个重要选择。

*   **专家混合模型 (MoE)**:
    *   **爆发于 2023年末 - 2024年初**。尽管 Google 早在2021年就发布了 Switch Transformer，但 MoE 架构的真正爆发是在 **Mistral AI 发布 Mixtral 8x7B** (2023年12月) 并开源之后。它证明了小型 MoE 模型可以匹敌大型稠密模型，引爆了开源社区对 MoE 的研究热潮。据传 GPT-4 (2023) 也采用了此架构。

*   **非 Transformer 架构探索**:
    *   **状态空间模型 (Mamba)**: **爆发于 2023年末**。由论文《Mamba: Linear-Time Sequence Modeling with Selective State Spaces》提出，其线性复杂度和与Transformer媲美的性能，使其成为挑战 Transformer 霸主地位的最有力竞争者，引发了大量后续研究。
    *   **补充技术 - RWKV**: **爆发于 2023年**。这是一个结合了 RNN 和 Transformer 思想的架构，因其独特的“时间并行”模式和低推理成本，在开源社区中拥有一批忠实拥趸，代表了非主流架构探索的一条重要路径。

---

### **2. 效率与优化 (Efficiency & Optimization)**
*这个分支决定了LLM的落地成本和普及速度，是学术界和工业界都极为关注的焦点。*

*   **模型压缩**:
    *   **量化 (Quantization)**: **爆发于 2022-2023年**。随着 `bitsandbytes` 库和论文《LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale》(2022) 的发布，在消费级硬件上运行大型模型成为可能。随后 `GPTQ` (2023)、`AWQ` (2023) 和 `GGUF` (Llama.cpp) 等更先进的4-bit量化方案层出不穷，极大推动了LLM的平民化。
    *   **知识蒸馏**: **爆发于 2023年至今**。随着 GPT-4 等超强闭源模型的出现，如何将它们的能力“蒸馏”到更小的开源模型上成为研究热点，相关论文和实践层出不穷。
    *   **补充技术 - LoRA (低秩适应)**: **爆发于 2022-2023年**。虽然 LoRA (2021) 最初用于高效微调，但其“低秩”思想也被应用于模型压缩和合并（如 `Mergekit`），成为一种轻量级的模型优化手段。

*   **高效训练**:
    *   **ZeRO (零冗余优化器)**: **爆发于 2020-2022年**。由微软 DeepSpeed 团队提出，通过分阶段切分优化器状态、梯度和参数，成为大规模模型分布式训练的标准技术。

*   **高效推理 (Inference Optimization)**:
    *   **KV 缓存优化**: **持续爆发 (2022年至今)**。KV 缓存是推理的基础，但对其优化是持续的热点。**Multi-Query/Grouped-Query Attention** (2023) 从架构上减少了KV缓存大小。PagedAttention (vLLM, 2023) 则从系统层面优化了显存管理，大幅提升吞吐量。
    *   **投机性解码 (Speculative Decoding)**: **爆发于 2023年**。通过用一个小的“草稿模型”快速生成候选序列，再由大模型一次性验证，显著加速了推理速度，成为重要的推理优化方向。
    *   **补充技术 - 模型编译 (Model Compilation)**: **爆发于 2023年至今**。以 **TensorRT-LLM** (NVIDIA) 为代表，通过将模型编译成高度优化的引擎，充分利用硬件特性，实现极致的推理性能，是生产环境部署的关键。

---

### **3. 对齐与安全 (Alignment & Safety)**
*这个分支决定了LLM能否成为负责任、可信赖的AI，是其社会价值的基石。*

*   **人类反馈强化学习 (RLHF)**: **爆发于 2022年末**。由 OpenAI 的 **InstructGPT** (2022) 和 **ChatGPT** (2022) 的巨大成功所引爆，成为训练出有用、听话的对话模型的“秘密武器”。
*   **直接偏好优化 (DPO/RPO)**: **爆发于 2023年末**。由斯坦福大学的论文《Direct Preference Optimization: Your Language Model is Secretly a Reward Model》提出，因其省去了训练奖励模型的步骤、训练过程更稳定，迅速成为替代 RLHF 的主流对齐方法。RPO (Rejection Sampling Preference Optimization) 是其变体。
*   **可解释性**: **持续缓慢发展**。这是一个长期且困难的领域，虽然一直有研究，但尚未出现像其他分支那样的“爆发性”突破。
*   **红队测试与对抗性攻击**: **爆发于 2023年**。随着LLM的广泛应用，其安全问题日益凸显。如何通过“越狱提示”(Jailbreak Prompts)等方式进行系统性攻击和防御，成为学术界和工业界共同关注的热点。

---

### **4. 扩展能力与多模态 (Extended Capabilities & Multimodality)**
*这个分支旨在打破LLM的“文本囚笼”，使其成为更通用的信息处理和交互入口。*

*   **多模态学习**: **爆发于 2023-2024年**。**GPT-4V(ision)** (2023) 的发布是关键节点，展示了顶级的图文理解能力。随后 Google 的 **Gemini** (2023) 和 OpenAI 的 **GPT-4o** (2024) 将实时音视频交互的多模态能力推向了新的高峰。
*   **工具使用与智能体 (Tool Use & Agents)**: **爆发于 2023年**。OpenAI 的 **ChatGPT Plugins/Function Calling** (2023) 功能，以及开源界的 **AutoGPT**、**BabyAGI** 等项目，引爆了对 LLM Agent 的研究。ReAct (2022) 等论文则为 Agent 的“思考-行动”循环提供了理论框架。
*   **长上下文处理**: **爆发于 2023年**。Anthropic 的 **Claude** (100K 上下文) 和论文 **Positional Interpolation** (2023) 开启了长上下文的“军备竞赛”。后续的 **Magic Needle** 测试和 **Gemini 1.5 Pro** (1M 上下文) 将这一能力推向了新的极限。

---

### **5. 数据工程与合成 (Data Engineering & Synthesis)**
*数据是模型的“食粮”，这个分支决定了模型能力的“天花板”和“品味”。*

*   **数据清洗与去偏**: **持续进行，重要性于 2023年凸显**。随着开源模型的发展，社区发现高质量、经过精心清洗和配比的数据集（如 **SlimPajama**, **Dolma**）是训练出强大模型的基础，数据工程的重要性被提到了前所未有的高度。
*   **课程学习**: **爆发于 2023-2024年**。微软的 **Phi** 系列模型论文明确指出，使用教科书质量的数据、遵循精心设计的“课程”进行训练，可以用极小的数据量训练出强大的小模型，引发了对数据质量和训练策略的重新思考。
*   **数据合成**: **爆发于 2023年至今**。微软的 **Orca** (2023) 和 Google 的 **Self-Instruct** 等工作，系统性地展示了如何使用 GPT-4 等强大模型生成海量、高质量的指令微调数据，被认为是解决高质量数据稀缺问题的关键路径。
