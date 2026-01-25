---
tags:
  - llm
  - training
---

# Pre-training

**Pre-training** is the most computationally expensive and critical phase of creating an [[Large Language Models|LLM]]. It turns a randomly initialized neural network into a "base model" that understands language structure and possesses general world knowledge.

## The Objective
The model is trained on a massive corpus of text (trillions of tokens) with a simple self-supervised objective: **predict the next token**.

$$ P(w_t | w_{t-1}, w_{t-2}, \dots) $$

By forcing the model to predict what comes next, it implicitly learns:
-   Syntax and Grammar.
-   Facts about the world (e.g., "The capital of France is...").
-   Reasoning patterns (e.g., "If A > B and B > C, then...").

## Data Sources
-   **Web Crawls**: Common Crawl (filtered for quality).
-   **Code**: GitHub, StackOverflow (improves reasoning abilities).
-   **Books**: Project Gutenberg, copyrighted libraries (for long-form narrative).
-   **Papers**: ArXiv, Wikipedia.

## Compute Requirements
-   Requires clusters of thousands of GPUs (e.g., A100s, H100s) or TPUs.
-   Takes months to run.
-   Cost runs into tens or hundreds of millions of dollars.

## The Result: Base Model
The output is a **Base Model** (e.g., Llama-2-Base, GPT-3 Base).
-   **Capabilities**: Can complete text, write stories, generate code.
-   **Limitations**: It is *not* a chat assistant. If you ask "What is the capital of France?", it might complete it with "and its population is..." rather than answering the question. It simulates the document, not a dialogue.

## References
-   [[Fine-tuning]] (The next step)
-   [[Architecture/Transformer|Transformer Architecture]]
