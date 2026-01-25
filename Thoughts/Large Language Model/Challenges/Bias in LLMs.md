---
tags:
  - llm
  - challenge
  - ethics
---

# Bias in LLMs

**Bias** in [[Large Language Models|LLMs]] occurs when the model generates outputs that exhibit prejudice, stereotyping, or unfair representation of certain groups.

## Sources of Bias
1.  **Training Data**: The internet (Common Crawl) reflects societal biases. If the data contains stereotypes (e.g., "Doctors are men, Nurses are women"), the model learns them.
2.  **Labeling Bias**: During [[RLHF]], the human annotators' own cultural or personal biases can influence the reward model.

## Manifestations
-   **Representation**: Under-representing minorities in generated stories.
-   **Stereotyping**: Associating negative traits with specific demographics.
-   **Allocational Harms**: If used for screening resumes or loans, rejecting qualified candidates based on biased patterns.

## Mitigation
-   **Data Curation**: Filtering training data.
-   **RLHF**: Explicitly penalizing biased responses during alignment.
-   **System Prompts**: Instructing the model to be neutral and inclusive.

## References
-   [[RLHF]]
-   [[Hallucination]]
