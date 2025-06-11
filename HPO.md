### 超参数调优（Hyperparameter Optimization, HPO）

#### 在深度学习的 HPO 领域，优化算法可归结为两种任务类型：
1. 单目标优化（Single-Objective HPO）：仅关注一个性能指标（如最小化验证误差或最大化准确率），使用传统凸优化或各类全局黑盒算法完成。
2. 多目标优化（Multi-Objective HPO）：同时平衡多个冲突指标（如精度 vs. 推理速度），目标是获得一组 Pareto 最优解。

#### 算法类别又可分为：
1. 元启发式全局搜索（Metaheuristic-Based HPO）：包括遗传算法（GA）、粒子群优化（PSO）、差分进化（DE）、蚁群算法（ACO）等，擅长非凸、非可微的黑盒空间探索 
2. 代理模型优化（Surrogate-Based HPO）：以贝叶斯优化（BO）为代表，借助高斯过程或神经网络等代理模型，在评价代价高的深度网络超参空间中，通过智能采样高效逼近最优解 
3. 多目标扩展：上述方法均可扩展至多目标情境，如 NSGA-II（基于 GA）、MO-PSO、Multi-Objective BO 等，用于构建 Pareto 前沿 
