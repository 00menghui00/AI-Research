# DAPO: An Open-Source LLM Reinforcement Learning System at Scale(2025,字节跳动)
- 论文地址：https://arxiv.org/html/2503.14476?_immersive_translate_auto_translate=1


## 概述：

### 引入了四项技术：
1. Clip-Higher, which promotes the diversity of the system and avoids entropy collapse;
 Clip-Higher，它促进了系统的多样性并避免了熵崩溃；
2. Dynamic Sampling, which improves training efficiency and stability;
 动态采样，提高训练效率和稳定性；
3. Token-Level Policy Gradient Loss, which is critical in long-CoT RL scenarios;
 Token 级别的策略梯度损失，在长 CoT RL 场景中至关重要；
4. Overlong Reward Shaping, which reduces reward noise and stabilizes training.
 过长奖励塑形，减少奖励噪声并稳定训练。

---
---

# Dynamic Sampling 技术

## 1. **问题背景：梯度消失（gradient-decreasing problem）**

* 在 **GRPO** 这样的 group-based RL 方法里，一个 prompt 会采样多个输出 $\{o_i\}_{i=1}^G$，再根据 reward 计算 advantage。
* 如果某个 prompt 的所有输出都完全正确（accuracy = 1），那么：

  * 所有 reward 相同 → advantage 为 0；
  * 导致这个 prompt 对梯度更新**没有贡献**。
* 如果 accuracy = 0（所有输出全错），也一样没有有效梯度信号。

📌 结果：

* 有效的 prompt 数量在训练中不断减少（很多 prompt 都“饱和”到 0 或 1）；
* batch 里的有效样本数下降 → 梯度信号弱、方差大、对噪声更敏感；
* 训练效率和稳定性变差。

---

## 2. **Dynamic Sampling 的核心思路**

> 在 batch 构建时，**只保留那些 accuracy 在 (0,1) 之间的 prompt**，即“有正确也有错误”的 prompt。

* **做法**：

  * 对每个 batch，先采样一堆数据；
  * 过滤掉 accuracy = 0 或 1 的 prompt；
  * 如果 batch 不够，就继续采样，直到 batch 被有效样本填满。
* **约束条件**：

  * 不能全错（数量 = 0）；
  * 不能全对（数量 = G）；
  * 必须介于中间。

这样，保证了每个 prompt 都能产生 **非零 advantage**，对训练有贡献。

---

## 3. **为什么不会降低效率？**

* 直觉上，好像要“多采样”才能凑够 batch → 会更慢。
* 但实际上：

  * 生成过程主要花时间在生成 **长尾样本（难样本）** 上；
  * 而过滤掉全对/全错的 prompt 并不会显著增加额外开销；
  * 实验发现，反而加快了收敛，因为所有样本都在“有效学习”。

---

## 4. **效果**

* 保持了一个稳定的有效样本数；
* 减少梯度方差，缓解训练不稳定；
* 提高 sample efficiency（同样的训练步数里学到更多）。


---
---

# Clip-Higher 

---

## 1. **问题背景：熵崩溃（Entropy Collapse）**

* 在 **naive PPO 或 GRPO** 中训练时，随着训练进行：

  * 策略越来越确定性（概率集中在少数几个 token 上）
  * 不同输出组（group）的样本几乎相同
  * **熵下降**很快 → 探索受限 → 模型过早收敛 → 扩展能力受阻

* 换句话说，模型变得“太自信”，几乎只输出自己认为最好的动作，而不再尝试新动作。

---

## 2. **为什么传统 PPO 的裁剪会导致问题**

* PPO 使用 **比例裁剪**（importance ratio clip）：

  $$
  r = \frac{\pi_\theta(o|q)}{\pi_{\theta_\text{old}}(o|q)}, \quad r \text{裁剪在}[1-\epsilon, 1+\epsilon]
  $$
* 作用：

  * 防止概率变化过大 → 保证训练稳定
* 问题出现了：

  * 对**高概率动作（exploitation token）**：上限不严格 → 可以继续增强 → 没问题
  * 对**低概率动作（exploration token）**：上限太小 → 很难增加概率 → 探索受限

---

### **举例说明**

假设 $\epsilon = 0.2$（默认值），系统想增加 token 的概率（$\hat{A} > 0$）：

| token | $\pi_\text{old}$ | 上限 $ \pi_\text{old}(1+\epsilon)$ | 实际可能增长             |
| ----- | ---------------- | -------------------------------- | ------------------ |
| 高概率 A | 0.9              | 0.9×1.2 = 1.08                   | 可以涨到 0.95 或 0.99 ✅ |
| 低概率 B | 0.01             | 0.01×1.2 = 0.012                 | 目标 0.02 太大，增长受限 ❌  |

> 结论：低概率 token 的增长空间太小 → 探索受限 → 熵崩溃

* 实验发现：被上限裁剪的 token 平均概率低于 0.2 → 支持了这一点

---

## 3. **Clip-Higher 的策略**

* **核心思想**：解耦上下限，让低概率 token 有更多提升空间

* 方法：

  * 上限 $\epsilon_\text{high}$ 调大 → 给低概率 token 更大增长空间
  * 下限 $\epsilon_\text{low}$ 保持不变 → 避免概率被压到 0

* 更新公式（简化理解）：

$$
L = \min\Big(r \hat{A}, \text{clip}(r, 1-\epsilon_\text{low}, 1+\epsilon_\text{high}) \hat{A}\Big)
$$

* 约束条件：

$$
0 < |\{o_i \mid o_i \text{正确}\}| < G
$$

意思是：避免全错或全对的 prompt → 保证每个 batch 都有有效梯度

---

## 4. **效果与直观理解**

* **上限调大 $\epsilon_\text{high}$** → 低概率探索 token 可以被提升 → 增加策略熵 → 输出更丰富多样
* **下限不变 $\epsilon_\text{low}$** → 防止某些 token 的概率被压到 0 → 保留采样空间
* 实验结果：

  * 平均被上限裁剪的概率增加
  * 模型生成的样本更加多样化
  * 避免早期确定性策略 → 改善探索能力

---



# Token-level Policy Gradient Loss

---

## 1️⃣ 传统 GRPO 的做法（Sample-level Loss）

* 步骤：

  1. 对于每个样本（一个完整回答），先把 **该样本内所有 token 的 loss 平均**。
  2. 再把所有样本的 loss 平均。

👉 等价于：**一个样本 = 一个权重**。不管长短，最后只贡献 1 份。

### 问题：

* **长回答的 token 被稀释**

  * 如果一个回答 1000 个 token，另一个只有 10 个 token，它们在 loss 里权重一样大。
  * → 1000 个 token 的平均值被“压缩”掉，很多信号丢失。
* **副作用**：

  1. **高质量长回答**：模型学不到里面复杂的推理模式（因为被平均稀释了）。
  2. **低质量长回答**：里面的“废话/乱语/重复”也得不到足够惩罚，结果导致：

     * **熵升高**（模型分布更混乱）。
     * **回答越来越长**（灌水现象）。

（对应文中 Figure 4(a): 熵曲线升高，Figure 4(b): 回答长度变长。）

---

## 2️⃣ Token-level Policy Gradient Loss 的改进

他们提出：与其按“样本”平均，不如直接按 **token 级别计算 loss**。

公式核心思想：

$$
\mathcal{J}_{DAPO}(\theta) = \mathbb{E}_{(q,a)\sim \mathcal{D}, \{o_i\}\sim \pi_\theta^{old}} \left[
\frac{1}{\sum_{i=1}^G |o_i|} \sum_{i=1}^G \sum_{t=1}^{|o_i|} \min \Big(
r_{i,t}(\theta) \hat{A}_{i,t}, \; clip(r_{i,t}(\theta), 1-\epsilon_{low}, 1+\epsilon_{high}) \hat{A}_{i,t}
\Big)\right]
$$

* **逐 token 计算优势项 $\hat{A}_{i,t}$**。
* **clip 操作**：跟 PPO 一样，避免更新过大。
* **归一化**：对所有 token 做归一化，而不是先对每个样本内部平均。

### 改进效果：

1. **长回答的 token 得到应有的权重**

   * 如果一个长回答里有 100 个合理 token，每个 token 都能推动模型学习。
2. **垃圾 token 会被显式惩罚**

   * 如果长回答里 900 个 token 是重复或乱码，这 900 个 token loss 会被累积，显著压制模型继续生成这类模式。
3. **训练更均衡**

   * 无论回答长短，token 信号都能公平反映到梯度里。

---
---

# Hide and Seek: Overlong Reward Shaping
---

## 1️⃣ 背景：为什么有“过长样本”问题

在 RLHF 或 DAPO 训练里，模型需要生成回答。但为了计算和训练稳定性，必须设定一个 **最大生成长度 Lmax**，超过的部分会 **截断 (truncate)**。

* **问题**：
  如果模型生成了一个 **合理但太长**的回答，它会被截断。
  → 默认做法是给截断样本一个 **惩罚奖励 (punitive reward)**，相当于告诉模型“这是坏的”。

* **后果**：

  * 一个可能非常合理的推理过程，**仅因为太长就被判错**。
  * 这会给模型带来 **奖励噪声 (reward noise)**，模型搞不清楚“是推理错了，还是只是写长了”。
  * 导致训练不稳定，性能下降。

---

## 2️⃣ 解决思路一：Overlong Filtering（过滤策略）

* **做法**：
  对被截断的样本，**直接不计入 loss**（mask 掉），既不奖励也不惩罚。
* **效果**：

  * 避免了奖励噪声，训练更稳定。
  * 图 Figure 5 显示：

    * AIME 上准确率更高。
    * 熵 (entropy) 曲线下降，模型生成更稳定。

👉 但是：过滤只是“绕开问题”，没引导模型真正避免过长输出。

---

## 3️⃣ 解决思路二：Soft Overlong Punishment（软惩罚机制）

他们提出了一个 **长度感知 (length-aware) 的惩罚函数**，而不是“一刀切的截断就惩罚”。

奖励公式（Eq. 13）：

$$
R_{\text{length}}(y) =
\begin{cases} 
0, & |y| \leq L_{max} - L_{cache} \\[6pt]
\dfrac{L_{max} - |y|}{L_{cache}}, & L_{max}-L_{cache} < |y| \leq L_{max}-1 \\[6pt]
-1, & |y| \geq L_{max}
\end{cases}
$$

* **解释**：

  * 如果输出 **没到 Lmax − Lcache**（还算正常长度），不给惩罚。
  * 如果输出进入 **惩罚区间**（接近 Lmax），惩罚值随长度逐步增大。
  * 如果 **超过 Lmax**（被截断），直接给一个强惩罚 (−1)。

### 好处

* 模型能“理解”：**太长会逐渐变坏**，不是“突然变坏”。
* 避免了对“稍微超长但合理”的回答给予过度惩罚。
* 同时起到引导作用：模型会学会尽量在合理长度内完成推理。

---

## 4️⃣ 总结

* **问题**：截断样本默认惩罚 → 引入奖励噪声 → 模型学坏。
* **方案 1**：Overlong Filtering → 不算截断样本的 loss，训练更稳。
* **方案 2**：Soft Overlong Punishment → 按长度动态惩罚，既减少噪声，又能引导模型收敛到合理长度。


