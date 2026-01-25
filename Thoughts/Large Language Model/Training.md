---
tags:
  - llm
  - training
  - map-of-content
---

# Training LLMs

Training a Large Language Model is a multi-stage process that transforms raw data into a helpful assistant.

## The Pipeline

1.  **[[Pre-training]]**
    -   The massive initial phase.
    -   Objective: Predict the next token.
    -   Result: Base Model.

2.  **[[Fine-tuning]]**
    -   Adapting the base model to follow instructions.
    -   **SFT**: Supervised Fine-Tuning.
    -   **PEFT**: Parameter-Efficient Fine-Tuning (e.g., LoRA).

3.  **[[RLHF|Alignment (RLHF)]]**
    -   Aligning the model with human values.
    -   Reward Modeling and PPO.

## Key Concepts
-   **Loss Function**: How the model measures its error (typically Cross-Entropy Loss).
-   **Overfitting**: Memorizing the training data instead of learning patterns.

## Deep Dives
-   [[Training/Pre-training]]
-   [[Training/Fine-tuning]]
-   [[Training/RLHF]]