# Gemini 2.5 Pro Capable of Winning Gold at IMO 2025

- 论文地址：https://arxiv.org/html/2507.15855v2#S3

## 观点：
通过使用自我验证流程和精心设计的提示最终解决了IMO的复杂数学推理问题，本文中说明了开发最佳策略以充分发挥强大 LLMs 在复杂推理任务中的全部潜力的重要性。

## At a high level, our pipeline proceeds as follows :

* Step 1: Initial solution generation with the prompt in Section 3.1;
* Step 2: Self-improvement;
* Step 3: Verifying the solution with the prompt in Section 3.2 and generating a bug report; go to Step 4 or Step 6 (see below for explanations);
* Step 4: Review of the bug report;
* Step 5: Correcting or improving the solution based on the bug report; go to Step 3;
* Step 6: Accept or Reject.

## Verifier 
验证器在我们的流程中扮演着重要角色。它的功能是逐步仔细审查一个解决方案，找出其中存在的问题（如果有）。我们强调数学严谨性，并将问题分为严重错误和论证缺口。严重错误是指明显错误或存在明显逻辑谬误的内容，而论证缺口可以是主要的或次要的。一个无法修复的主要论证缺口会导致整个证明崩溃，而轻微的论证缺口甚至可能没有明确定义：一个轻微的缺口有时可能被视为简洁的论证。
