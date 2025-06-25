# Deep Reinforcement Learning Doesn't Work Yet
原文地址：https://www.alexirpan.com/2018/02/14/rl-hard.html

## 概念扫盲

### 1. Arcade Learning Environment (ALE)
   ALE是一个软件接口，它提供了一个统一的平台，允许强化学习算法与Atari 2600游戏进行交互。研究人员可以通过ALE获取游戏的原始像素画面、得分、游戏状态等信息，并向游戏发送动作指令。

### 2.DQN
  DQN的创新之处在于，它使用深度神经网络来近似Q函数，从而能够处理高维度的输入（例如游戏画面的原始像素），并输出每个可能动作的Q值。Q-learning 是一种经典的强化学习算法，它通过学习一个Q函数（Q-value function），来估计在给定状态下采取某个动作所能获得的未来累积奖励。Q函数通常用表格来表示（Q-table），但在状态空间或动作空间非常大的情况下，这种方法变得不可行。
