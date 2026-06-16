# Kaggle Competition Summary

This document summarizes the Kaggle competition used as the basis for this project.

---

## Competition

**MEPhI M-25 ML Financial News Headlines**

Dataset page:
https://www.kaggle.com/competitions/me-ph-i-m-25-ml-financial-news-headlines/data

The competition focuses on sentiment classification in Russian-language financial news headlines and comments.

---

## Task Overview

The goal of the competition is to analyze a text fragment and identify the sentiment expressed toward specific financial organizations.

For each input text, the model must determine:

1. Whether a target organization is mentioned or relevant.
2. Whether the sentiment toward that organization is negative, neutral, or positive.

This makes the task more complex than standard sentiment classification because one text can contain multiple entities, and each entity may require a separate sentiment prediction.

---

## Input Data

The competition provides text data related to financial news and comments.

The main input column is:

| Column | Description                                                  |
| ------ | ------------------------------------------------------------ |
| `text` | Russian-language text fragment used for sentiment prediction |

Each row may refer to one or more financial organizations.

---

## Target Entities

The model predicts sentiment for the following targets:

| Target       | Description               |
| ------------ | ------------------------- |
| `sber`       | Sber                      |
| `vtb`        | VTB                       |
| `gazprom`    | Gazprombank               |
| `alfabank`   | Alfa-Bank                 |
| `raiffeisen` | Raiffeisen                |
| `rshb`       | Russian Agricultural Bank |
| `company`    | Other company mentions    |

---

## Sentiment Classes

For each target entity, sentiment can be represented as:

| Original Label | Meaning                               |
| -------------- | ------------------------------------- |
| `-1`           | Negative sentiment                    |
| `0`            | Neutral sentiment                     |
| `1`            | Positive sentiment                    |
| Missing value  | No relevant mention or not applicable |

In the notebook, these labels are converted into four internal classes:

| Encoded Class | Meaning             |
| ------------- | ------------------- |
| `0`           | No relevant mention |
| `1`           | Negative sentiment  |
| `2`           | Neutral sentiment   |
| `3`           | Positive sentiment  |

---

## Submission Format

The final Kaggle submission uses binary columns for each entity-sentiment pair.

For example, for `sber`, the output columns are:

| Column   | Meaning                        |
| -------- | ------------------------------ |
| `sber_n` | Negative sentiment toward Sber |
| `sber_0` | Neutral sentiment toward Sber  |
| `sber_p` | Positive sentiment toward Sber |

The same structure is repeated for all target entities.

The final submission file must include:

```text
id
sber_n
sber_0
sber_p
vtb_n
vtb_0
vtb_p
gazprom_n
gazprom_0
gazprom_p
alfabank_n
alfabank_0
alfabank_p
raiffeisen_n
raiffeisen_0
raiffeisen_p
rshb_n
rshb_0
rshb_p
company_n
company_0
company_p
```

---

## Evaluation Metric

The competition uses **macro-averaged F1-score**.

Macro F1-score calculates the F1-score independently for each target column and then averages the results.

This metric is important for this task because:

* target labels are imbalanced;
* some entities appear more often than others;
* some sentiment classes are rare;
* performance on minority labels still matters.

As a result, optimizing only for frequent classes is not enough.

---

## Main Challenges

The task includes several practical NLP challenges:

| Challenge                   | Explanation                                                           |
| --------------------------- | --------------------------------------------------------------------- |
| Multi-entity classification | One text may mention more than one organization                       |
| Entity-specific sentiment   | Different organizations in the same text may have different sentiment |
| Class imbalance             | Some labels appear much less often than others                        |
| Russian-language text       | The model must understand Russian financial language                  |
| Submission constraints      | Predictions must follow a binary entity-sentiment format              |
| False positives             | The model may predict sentiment for entities that are not mentioned   |

---

## Solution Strategy

The project solution addresses these challenges using:

* a Russian-language transformer model;
* multi-head classification;
* class-weighted loss;
* 5-fold stratified cross-validation;
* out-of-fold threshold tuning;
* entity-aware post-processing;
* validation of the final submission format.

The selected base model is:

```text
ai-forever/ruRoBERTa-large
```

This model is suitable because it is designed for Russian-language text and can capture contextual meaning better than traditional bag-of-words or shallow machine learning approaches.

---

## Output

The notebook generates the final prediction file:

```text
outputs/submission.csv
```

This file is designed to match the format required by the Kaggle competition.

---

## Notes on Data Usage

The raw competition data is not included in this repository.

To reproduce the project, users must download the dataset directly from Kaggle and place the required files inside the local `data/` directory.

Required files:

```text
data/train.csv
data/test.csv
data/sample_submission.csv
```

---

## Summary

This Kaggle competition provides a realistic financial NLP task involving entity-level sentiment classification.

The project demonstrates how to build a complete machine learning pipeline for this task, including data preparation, model training, validation, post-processing, and submission generation.
