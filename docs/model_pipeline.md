# Model Pipeline

This document explains the technical pipeline used in the financial sentiment classification project.

---

## Pipeline Overview

The goal of the pipeline is to transform raw Russian-language financial text into entity-level sentiment predictions.

The complete workflow is:

```text
Raw text
   ↓
Text cleaning
   ↓
Entity normalization
   ↓
Entity detection
   ↓
Label encoding
   ↓
Tokenization
   ↓
ruRoBERTa encoder
   ↓
Multi-head classification
   ↓
Cross-validation
   ↓
Threshold tuning
   ↓
Entity-aware post-processing
   ↓
Kaggle submission
```

---

## 1. Data Loading

The notebook loads three files from the local `data/` directory:

| File                    | Purpose                                      |
| ----------------------- | -------------------------------------------- |
| `train.csv`             | Training data with text and sentiment labels |
| `test.csv`              | Test data used for final prediction          |
| `sample_submission.csv` | Required Kaggle submission format            |

The raw dataset files are not included in the repository and must be downloaded from Kaggle.

---

## 2. Text Cleaning

The text cleaning step removes elements that are unlikely to improve model performance:

* URLs;
* emojis;
* excessive whitespace.

The text is not heavily normalized because transformer models usually benefit from preserving natural sentence structure and context.

The cleaned text is stored as:

```text
clean_text
```

This version is used as model input.

---

## 3. Entity Normalization

A second version of the text is created for entity detection.

This version is lowercased and normalized to make rule-based matching more reliable.

The normalized text is stored as:

```text
entity_text
```

This version is not used directly as model input. It is used for regular expression-based entity detection.

---

## 4. Entity Detection

Regular expressions are used to detect whether specific financial organizations are mentioned in the text.

The target bank entities are:

* Sber
* VTB
* Gazprombank
* Alfa-Bank
* Raiffeisen
* Russian Agricultural Bank

Entity detection is used later during post-processing to reduce false positive predictions.

For example, if the model predicts sentiment for a bank that is not detected in the text, the corresponding prediction can be removed.

---

## 5. Label Encoding

Each target entity is represented as a four-class classification problem.

| Encoded Label | Meaning             |
| ------------- | ------------------- |
| 0             | No relevant mention |
| 1             | Negative sentiment  |
| 2             | Neutral sentiment   |
| 3             | Positive sentiment  |

This encoding allows each classification head to predict one sentiment state for each target entity.

---

## 6. Binary Target Conversion

The Kaggle submission format requires binary columns for each entity-sentiment pair.

For example:

```text
sber_n
sber_0
sber_p
```

These columns represent:

| Column   | Meaning                        |
| -------- | ------------------------------ |
| `sber_n` | Negative sentiment toward Sber |
| `sber_0` | Neutral sentiment toward Sber  |
| `sber_p` | Positive sentiment toward Sber |

The notebook includes helper functions to convert between four-class labels and the required binary submission format.

---

## 7. Tokenization

The text is tokenized using the tokenizer associated with:

```text
ai-forever/ruRoBERTa-large
```

The tokenizer converts raw text into numerical token IDs that can be processed by the transformer model.

The main tokenization settings are:

| Parameter               | Value                |
| ----------------------- | -------------------- |
| Maximum sequence length | 128                  |
| Padding                 | Fixed-length padding |
| Truncation              | Enabled              |

---

## 8. Model Architecture

The model uses a shared ruRoBERTa encoder followed by multiple classification heads.

The shared encoder processes the input text and produces contextual token representations.

The `[CLS]` representation is used as a sentence-level representation and passed to the classification heads.

Each head predicts sentiment for one target entity.

```text
Input text
   ↓
Tokenizer
   ↓
ruRoBERTa encoder
   ↓
[CLS] representation
   ↓
Entity-specific classification heads
   ↓
NULL / Negative / Neutral / Positive
```

---

## 9. Training Strategy

The model is trained using PyTorch.

The main training settings are:

| Parameter                   | Value |
| --------------------------- | ----- |
| Optimizer                   | AdamW |
| Learning rate               | 2e-5  |
| Weight decay                | 0.01  |
| Batch size                  | 4     |
| Gradient accumulation steps | 4     |
| Effective batch size        | 16    |
| Epochs                      | 5     |
| Dropout                     | 0.20  |
| Early stopping patience     | 3     |

Gradient accumulation is used because large transformer models require significant GPU memory.

---

## 10. Class Imbalance Handling

The dataset contains imbalanced sentiment labels.

To reduce the impact of imbalance, the solution uses class-weighted cross-entropy loss.

Class weights are computed separately for each target entity based on label frequency.

This helps the model pay more attention to underrepresented classes.

---

## 11. Cross-validation

The model is trained using 5-fold stratified cross-validation.

Cross-validation provides:

* more reliable validation scores;
* out-of-fold predictions;
* better threshold tuning;
* more stable test predictions through fold averaging.

The final test prediction is obtained by averaging predictions across all folds.

---

## 12. Threshold Tuning

The model outputs class probabilities.

These probabilities are converted into binary submission columns using threshold tuning.

Instead of using one fixed threshold for all columns, the solution finds the best threshold for each entity-sentiment column using out-of-fold predictions.

This helps improve macro F1-score, especially for rare sentiment labels.

---

## 13. Single-sentiment Enforcement

For each target entity, only one sentiment should be active at a time.

If multiple sentiment columns are activated for the same entity, the solution keeps the sentiment with the highest model score and removes the others.

This improves logical consistency in the final predictions.

---

## 14. Entity-aware Post-processing

The solution applies entity-aware post-processing rules.

If a bank entity is not detected in the text, the corresponding sentiment columns are set to zero.

This reduces false positives where the model predicts sentiment for an organization that is not explicitly mentioned.

---

## 15. Submission Generation

The final predictions are converted into the exact structure required by Kaggle.

The generated file is:

```text
outputs/submission.csv
```

The notebook checks that:

* the number of rows is correct;
* the column order matches `sample_submission.csv`;
* all prediction values are binary;
* each entity has at most one active sentiment column per row.

---

## Summary

The pipeline combines transformer-based NLP modeling with practical competition-oriented improvements such as cross-validation, threshold tuning, class imbalance handling, and rule-based post-processing.

The result is a complete, reproducible workflow for entity-level financial sentiment classification.
