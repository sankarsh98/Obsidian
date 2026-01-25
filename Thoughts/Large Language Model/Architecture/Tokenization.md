---
tags:
  - llm
  - nlp
  - preprocessing
---

# Tokenization

**Tokenization** is the first step in the [[Large Language Models|LLM]] pipeline. It involves breaking down raw text into smaller units called **tokens**, which are then converted into numerical IDs for the model to process.

## Why not just use words?
-   **Vocabulary Size**: English has massive vocabulary. A word-level model would need a huge embedding matrix.
-   **Unknown Words (OOV)**: New words or typos would be impossible to represent.
-   **Morphology**: "Walk", "Walked", "Walking" share a root meaning that word-level tokenization might miss.

## Common Algorithms

### 1. Byte-Pair Encoding (BPE)
-   **Used by**: [[GPT]], [[RoBERTa]].
-   **Mechanism**: Iteratively merges the most frequent pair of adjacent characters/bytes.
-   **Result**: Common words are single tokens (`apple`); rare words are broken into subwords (`ap`, `ple`).

### 2. WordPiece
-   **Used by**: [[BERT]].
-   **Mechanism**: Similar to BPE but maximizes the likelihood of the training data rather than just frequency. Uses `##` prefix to denote subwords (e.g., `play`, `##ing`).

### 3. SentencePiece
-   **Used by**: [[LLaMA]], [[T5]].
-   **Mechanism**: Treats the input as a raw stream of unicode characters (including spaces). Language agnostic (doesn't rely on space delimiters).

## The Token-to-Word Ratio
-   Roughly, **1000 tokens $\approx$ 750 words** in English.
-   This ratio varies by language. Complex scripts or agglutinative languages might use more tokens per sentence.

## Impact on LLMs
-   **Context Window**: Defined in tokens. Efficient tokenization allows more text in the same window.
-   **Math/Code**: Tokenizers often struggle with numbers (e.g., separating digits `123` vs `1`, `2`, `3`) which affects arithmetic performance.

## References
-   [[Concepts/Embeddings|Embeddings]] (Next step after tokenization)
