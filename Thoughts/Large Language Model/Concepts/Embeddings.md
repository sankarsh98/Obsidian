---
tags:
  - llm
  - concepts
  - vector-database
---

# Embeddings

**Embeddings** are the bridge between discrete text (tokens) and the continuous mathematics of neural networks. An embedding is a dense vector (a list of floating-point numbers) representing the semantic meaning of a token or text chunk.

## Key Concept: Semantic Space
-   Words with similar meanings are mapped to points close to each other in this high-dimensional space.
-   **King - Man + Woman $\approx$ Queen**: The famous analogy showing that vector arithmetic captures semantic relationships.

## Types of Embeddings

### 1. Token Embeddings
-   The initial layer of an LLM.
-   Converts the integer Token ID (e.g., `5301`) into a vector (e.g., `[0.1, -0.5, ...]` of size $d_{model}$).
-   These are learned during [[Pre-training]].

### 2. Positional Embeddings
-   Added to Token Embeddings to indicate order.
-   Can be **Absolute** (Sinusoidal in original Transformer) or **Relative** (RoPE - Rotary Positional Embeddings, used in [[LLaMA]]).

### 3. Sentence/Document Embeddings
-   Represent entire chunks of text as a single vector.
-   Used heavily in **[[RAG|Retrieval Augmented Generation]]**.
-   Created by averaging token embeddings or using the `[CLS]` token output (in BERT).

## Vector Databases
To search embeddings efficiently, we use Vector Databases (e.g., Pinecone, Milvus, Weaviate).
-   **Search**: Find "Nearest Neighbors" using metrics like:
    -   **Cosine Similarity**: Measures the angle between vectors (1 = identical direction).
    -   **Euclidean Distance**: Measures straight-line distance.

## References
-   [[Tokenization]]
-   [[Architecture/Transformer|Transformer Architecture]]
-   [[RAG]]
