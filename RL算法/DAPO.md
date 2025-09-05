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
* **公式 (11) 的约束条件**：

  $$
  0 < \big| \{o_i \mid is\_equivalent(a, o_i)\} \big| < G
  $$

  表示：

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

