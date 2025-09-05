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

