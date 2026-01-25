---
tags:
  - llm
  - training
  - alignment
---

# Reinforcement Learning from Human Feedback (RLHF)

**RLHF** is the alignment stage used to make LLMs safer, more helpful, and more honest. It aligns the model's objective with complex human values that are hard to define mathematically.

## The 3-Step Process

### 1. Supervised Fine-Tuning (SFT)
Start with a model that already follows instructions (the [[Fine-tuning|SFT model]]).

### 2. Reward Modeling (RM)
-   **Data Collection**: Humans are presented with a prompt and several model outputs. They rank them from best to worst.
-   **Training**: A separate neural network (the Reward Model) is trained to predict these rankings (outputting a scalar score).
-   **Goal**: The RM becomes a proxy for human preference.

### 3. Proximal Policy Optimization (PPO)
-   **Reinforcement Learning**: The LLM acts as the "Agent". Generating text is the "Action".
-   **Optimization**: The model generates text, the Reward Model scores it, and PPO updates the LLM's weights to maximize this score.
-   **KL Divergence Penalty**: A constraint is added to prevent the model from drifting too far from the original SFT model (preventing "reward hacking" or gibberish).

## Alternatives
-   **DPO (Direct Preference Optimization)**: Optimizes the policy directly from the preference data without training a separate Reward Model. It is mathematically equivalent to RLHF but more stable and computationally efficient.

## References
-   [[Fine-tuning]]
-   [[Challenges/Hallucination|Hallucination]]
-   [[Challenges/Bias in LLMs|Bias in LLMs]]
