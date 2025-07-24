# Gemini 2.5 Pro Capable of Winning Gold at IMO 2025

- 论文地址：https://arxiv.org/html/2507.15855v2#S3

## 观点：
通过使用自我验证流程和精心设计的提示最终解决了IMO的复杂数学推理问题，本文中说明了开发最佳策略以充分发挥强大 LLMs 在复杂推理任务中的全部潜力的重要性。

## At a high level, our pipeline proceeds as follows :

• Step 1: Initial solution generation with the prompt in Section 3.1;
• Step 2: Self-improvement;
• Step 3: Verifying the solution with the prompt in Section 3.2 and generating a bug report; go to Step 4 or Step 6 (see below for explanations);
• Step 4: Review of the bug report;
• Step 5: Correcting or improving the solution based on the bug report; go to Step 3;
• Step 6: Accept or Reject.
