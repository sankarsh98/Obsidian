---
tags:
  - llm
  - technique
  - rag
---

# Retrieval-Augmented Generation (RAG)

**RAG** is a technique to ground [[Large Language Models|LLM]] responses in external, up-to-date, or proprietary data. It solves two key problems: **Knowledge Cutoff** (LLMs don't know recent events) and **Hallucination** (making things up).

## How it Works

1.  **Ingestion**: Documents (PDFs, Wikis) are split into chunks.
2.  **Embedding**: Chunks are converted into vectors using an [[Concepts/Embeddings|Embedding Model]].
3.  **Storage**: Vectors are stored in a Vector Database.
4.  **Retrieval**: When a user asks a question:
    -   The question is embedded into a vector.
    -   The database finds the most similar chunks (Top-K).
5.  **Generation**: The retrieved chunks are pasted into the LLM's prompt as context.
    > "Answer the user question using only the context below:
    > [Chunk 1]
    > [Chunk 2]
    > User Question: ..."

## Benefits
-   **Accuracy**: Reduces hallucinations by grounding answers in provided text.
-   **Updates**: No need to re-train the model to add new knowledge; just add to the vector DB.
-   **Privacy**: Can keep sensitive data in the DB and not in the model weights.

## References
-   [[Concepts/Embeddings|Embeddings]]
-   [[Concepts/Context Window|Context Window]]
-   [[Challenges/Hallucination|Hallucination]]
