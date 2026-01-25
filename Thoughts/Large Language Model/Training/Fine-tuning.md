---
tags:
  - llm
  - training
  - fine-tuning
---

# Fine-tuning

**Fine-tuning** is the process of taking a [[Pre-training|Pre-trained Base Model]] and adapting it to a specific task or behavior.

## Supervised Fine-Tuning (SFT) / Instruction Tuning
This is the standard method to turn a Base Model into a Chat Assistant.
-   **Dataset**: A smaller, high-quality dataset of `(Instruction, Response)` pairs.
-   **Process**: The model is trained to generate the *Response* given the *Instruction*.
-   **Outcome**: The model learns to follow orders, answer questions, and adopt a specific persona (e.g., "You are a helpful assistant").

## Parameter-Efficient Fine-Tuning (PEFT)
Full fine-tuning updates all billions of parameters, which is expensive. PEFT updates only a small subset or adds small adapter layers.

### LoRA (Low-Rank Adaptation)
-   Instead of updating the massive weight matrix $W$, LoRA learns two small matrices $A$ and $B$ such that $\Delta W = BA$.
-   **Benefits**: Reduces memory usage by 90%+. Allows running fine-tuning on consumer GPUs.
-   **QLoRA**: Quantized LoRA. Uses 4-bit quantization to further reduce memory.

## Domain Adaptation
Fine-tuning on a specific corpus (e.g., medical journals, legal texts) to make the model an expert in that field, often using the same "next token prediction" objective as pre-training but on focused data.

## References
-   [[Pre-training]]
-   [[RLHF]]
