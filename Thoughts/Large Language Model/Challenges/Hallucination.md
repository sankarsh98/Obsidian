---
tags:
  - llm
  - challenge
  - risk
---

# Hallucination

**Hallucination** in LLMs refers to the generation of text that is grammatically correct and fluent but factually incorrect or nonsensical.

## Why do they hallucinate?
-   **Probabilistic Nature**: LLMs are "stochastic parrots." They predict the *next likely word* based on statistical patterns, not truth.
-   **Data Gaps**: If the model hasn't seen the specific fact often enough, it may conflate similar concepts.
-   **Pressure to Answer**: Models are often trained to be helpful, which can bias them to invent an answer rather than saying "I don't know."

## Types of Hallucinations
1.  **Fact Fabrication**: Inventing historical events, bios, or citations (e.g., fake court cases).
2.  **Faithfulness Error**: Summarizing a document but adding details that weren't in the source.

## Mitigation Strategies
-   **[[RAG|RAG]]**: Grounding the model in retrieved evidence.
-   **Temperature Reduction**: Setting temperature to 0 makes the model more deterministic and less "creative."
-   **Chain of Thought**: Asking the model to reason first can reduce logical errors.
-   **Verification**: Using a second model or search tool to verify claims.

## References
-   [[Bias in LLMs]]
-   [[RAG]]
