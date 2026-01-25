---
tags:
  - llm
  - architecture
  - map-of-content
---

# LLM Architecture

The architecture of [[Large Language Models|LLMs]] is predominantly based on the **Transformer**, a deep learning model introduced in 2017. This folder contains detailed notes on the specific components that make these models work.

## Core Components

-   **[[Architecture/Transformer|Transformer Architecture]]**: The high-level overview of the Encoder/Decoder structure.
-   **[[Architecture/Attention Mechanism|Self-Attention Mechanism]]**: The "brain" of the model that connects words based on relevance.
-   **[[Tokenization]]**: How text is chopped up into numbers before entering the model.
-   **[[Embeddings]]**: The vector representation of meanings.
-   **[[Context Window]]**: The working memory of the model.

## Evolution of Architectures
-   **Encoder-Only**: [[BERT]] (Good for understanding).
-   **Decoder-Only**: [[GPT]] (Good for generation).
-   **Encoder-Decoder**: [[T5]] (Good for translation).

## Deep Dives
-   [[Architecture/Transformer]]
-   [[Architecture/Attention Mechanism]]
-   [[Architecture/Tokenization]]
-   [[Concepts/Embeddings]]