---
tags:
  - llm
  - concepts
  - limitations
---

# Context Window

The **Context Window** is the limit on the amount of text (measured in [[Tokenization|tokens]]) that an LLM can consider at one time. It includes the prompt, the user's input, the conversation history, and the model's generated output.

## Why is it limited?
The computational cost of the [[Architecture/Attention Mechanism|Self-Attention Mechanism]] scales **quadratically** with the sequence length ($O(n^2)$). Doubling the context length requires 4x the compute and memory for attention.

## Evolution
-   **GPT-2**: 1,024 tokens.
-   **GPT-3**: 2,048 tokens.
-   **GPT-4**: 8k - 32k tokens.
-   **GPT-4 Turbo**: 128k tokens.
-   **Claude 3**: 200k tokens.
-   **Gemini 1.5**: 1 Million+ tokens.

## Handling Long Contexts
When content exceeds the window, we must use strategies to manage it:
1.  **Truncation**: Cutting off the oldest or least relevant parts.
2.  **Summarization**: Periodically summarizing history to free up space.
3.  **[[RAG|RAG]]**: Storing vast knowledge in a database and retrieving only the relevant chunks for the current window.
4.  **Sliding Window**: Moving a window over the text (common in processing, less so in generation).

## Lost in the Middle
Research shows that models are best at retrieving information from the beginning and end of their context window, often degrading in performance for information buried in the middle.
