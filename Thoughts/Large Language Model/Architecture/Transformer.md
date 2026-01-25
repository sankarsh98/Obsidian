---
tags:
  - llm
  - architecture
  - deep-learning
---

# The Transformer Architecture

The **Transformer** is the foundational architecture for modern [[Large Language Models|LLMs]]. Introduced in the 2017 paper *"Attention Is All You Need"* by Vaswani et al., it departed from Recurrent Neural Networks (RNNs) by processing data in parallel rather than sequentially.

## Core Innovations

### 1. [[Architecture/Attention Mechanism|Self-Attention]]
The key differentiator. It allows the model to weigh the importance of different tokens in a sequence relative to each other, regardless of their distance. This solves the "long-term dependency" problem of RNNs.

### 2. Parallelization
Unlike RNNs/LSTMs, which process tokens one by one ($t_1, t_2, \dots$), Transformers process the entire sequence simultaneously. This allows for massive parallelization on GPUs, enabling the training of much larger models on much larger datasets.

### 3. Positional Encodings
Since the model processes tokens in parallel, it has no inherent sense of order (unlike an RNN). **Positional Encodings** are vectors added to the [[Concepts/Embeddings|Token Embeddings]] to give the model information about the relative or absolute position of the tokens in the sequence.

---

## Components

A full Transformer consists of two main blocks:

### Encoder
-   **Function**: Processes the input to create a context-rich representation.
-   **Structure**: Stack of layers containing Self-Attention and Feed-Forward Networks.
-   **Bidirectional**: Can "see" tokens to the left and right.
-   **Usage**: [[BERT]], Classification, Named Entity Recognition.

### Decoder
-   **Function**: Generates output token by token.
-   **Structure**: Similar to Encoder, but with "Masked" Self-Attention (cannot see future tokens).
-   **Unidirectional**: Auto-regressive (predicts next token based on previous ones).
-   **Usage**: [[GPT]], [[LLaMA]], Text Generation.

### Encoder-Decoder
-   **Function**: Combines both. Encoder processes input; Decoder generates output.
-   **Usage**: [[T5]], Translation (Seq2Seq tasks).

---

## Key Layers within a Block

1.  **Multi-Head Attention**: Runs multiple "heads" of attention in parallel to capture different types of relationships.
2.  **Add & Norm**: Residual connections (adding input to output) followed by Layer Normalization. Crucial for gradient flow in deep networks.
3.  **Feed-Forward Network (FFN)**: A simple fully connected network applied to each position independently.

---

## References
-   [[Architecture/Attention Mechanism|Attention Mechanism]]
-   [[Architecture/Tokenization|Tokenization]]
-   [[Concepts/Embeddings|Embeddings]]
