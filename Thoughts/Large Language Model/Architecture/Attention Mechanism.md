---
tags:
  - llm
  - architecture
  - mechanism
---

# Self-Attention Mechanism

**Self-Attention** is the mechanism that enables the [[Architecture/Transformer|Transformer]] to understand the relationship between words in a sequence. It allows the model to assign different "weights" or importance to different parts of the input when processing a specific word.

## The Analogy: Query, Key, Value
The mechanism is often explained using a retrieval analogy involving three vectors generated for each token:

1.  **Query ($Q$)**: "What am I looking for?" (The current token focusing on others).
2.  **Key ($K$)**: "What do I contain?" (The other tokens identifying themselves).
3.  **Value ($V$)**: "What is my actual content?" (The information to be extracted).

## How it Works

1.  **Compatibility**: For a specific word (Query), we calculate a score against every other word (Key) via a dot product ($Q \cdot K^T$). High score = high relevance.
2.  **Scaling**: The scores are scaled (divided by $\sqrt{d_k}$) to stabilize gradients.
3.  **Softmax**: Apply [[Softmax]] to turn scores into probabilities (weights) that sum to 1.
4.  **Weighted Sum**: Multiply these weights by the **Values** ($V$).
    -   If a word is irrelevant, its weight is near 0, so its Value is ignored.
    -   If a word is relevant, its Value is emphasized.

$$ 
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V 
$$ 

## Multi-Head Attention
Instead of doing this once, the model does it $h$ times in parallel with different weight matrices.
-   **Why?** It allows the model to focus on different *types* of relationships simultaneously (e.g., Head 1 tracks grammatical subject-verb, Head 2 tracks pronoun antecedents).
-   The outputs of all heads are concatenated and projected back to the original dimension.

## Masked Self-Attention
Used in the **Decoder** to prevent cheating. When predicting the token at position $t$, the model should not be allowed to see tokens at $t+1$. The attention scores for future tokens are set to $-\infty$ before the softmax, effectively zeroing them out.
