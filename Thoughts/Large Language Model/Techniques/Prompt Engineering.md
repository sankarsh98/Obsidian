---
tags:
  - llm
  - technique
  - prompting
---

# Prompt Engineering

**Prompt Engineering** is the art and science of crafting inputs (prompts) to guide [[Large Language Models|LLMs]] to generate the desired output. Since LLMs are sensitive to phrasing, slight changes can yield vastly different results.

## Core Techniques

### 1. Zero-Shot & Few-Shot Prompting
-   **Zero-Shot**: Asking the model to do a task without examples.
    > "Translate this to Spanish: Hello"
-   **Few-Shot**: Providing examples (shots) in the context.
    > "Translate to Spanish:
    > Good morning -> Buenos dias
    > Hello ->"

### 2. Chain of Thought (CoT)
Encouraging the model to "think" out loud before answering. This significantly improves performance on math and logic tasks.
-   **Zero-Shot CoT**: Simply adding "Let's think step by step" to the prompt.

### 3. Persona / Role Prompting
Assigning a role to the model to condition its style and expertise.
> "Act as a senior Python backend engineer..."

### 4. System Prompts
Instructions provided to the model that define its global behavior, distinct from the user's turn-by-turn input.

## Advanced Strategies
-   **Tree of Thoughts**: Exploring multiple reasoning paths.
-   **ReAct**: Combining **Re**asoning and **Act**ing (calling external tools).

## References
-   [[Concepts/Context Window|Context Window]]
-   [[RAG]]
