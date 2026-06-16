# Project Overview

This document provides a high-level overview of the financial sentiment classification project.

---

## Project Summary

This project implements a Natural Language Processing solution for financial sentiment classification in Russian-language news headlines and comments.

The goal is to identify whether a text fragment refers to a specific financial organization and determine the sentiment expressed toward that organization.

The project is based on the Kaggle competition:

**MEPhI M-25 ML Financial News Headlines**
https://www.kaggle.com/competitions/me-ph-i-m-25-ml-financial-news-headlines/data

---

## Problem

Financial news and public comments can contain important signals about how organizations are perceived by the market, customers, and the public.

However, manually analyzing large volumes of text is time-consuming and difficult to scale.

The task is to build a model that can automatically detect:

1. Which financial organization is mentioned in the text.
2. Whether the sentiment toward that organization is negative, neutral, or positive.

---

## Target Entities

The model predicts sentiment for the following entities:

| Entity                    | Description                    |
| ------------------------- | ------------------------------ |
| Sber                      | Russian financial organization |
| VTB                       | Russian financial organization |
| Gazprombank               | Russian financial organization |
| Alfa-Bank                 | Russian financial organization |
| Raiffeisen                | Financial organization         |
| Russian Agricultural Bank | Russian financial organization |
| Company                   | Other company mentions         |

---

## Sentiment Labels

Each entity can be assigned one of the following sentiment states:

| Label               | Meaning                                                    |
| ------------------- | ---------------------------------------------------------- |
| No relevant mention | The entity is not mentioned or is not relevant in the text |
| Negative            | The text expresses negative sentiment toward the entity    |
| Neutral             | The text expresses neutral sentiment toward the entity     |
| Positive            | The text expresses positive sentiment toward the entity    |

---

## Solution Approach

The solution uses a transformer-based model for Russian text:

`ai-forever/ruRoBERTa-large`

The model architecture consists of:

1. A shared ruRoBERTa encoder.
2. Multiple classification heads.
3. One classification head per target entity.

This approach allows the model to learn general language and financial context while making separate predictions for each organization.

---

## Main Workflow

The project follows this workflow:

1. Load the competition dataset.
2. Clean and normalize the input text.
3. Detect entity mentions using rule-based patterns.
4. Encode target labels.
5. Tokenize text using the ruRoBERTa tokenizer.
6. Train a multi-head transformer model.
7. Validate the model using 5-fold cross-validation.
8. Tune prediction thresholds using out-of-fold predictions.
9. Apply entity-aware post-processing rules.
10. Generate a Kaggle-compatible submission file.

---

## Why This Project Is Useful

This type of solution can be useful for:

* financial reputation monitoring;
* customer sentiment analysis;
* market intelligence;
* financial news analytics;
* risk monitoring;
* automated text classification systems.

The same approach can be adapted to other domains where sentiment must be detected for multiple entities inside the same text.

---

## Technical Highlights

The project demonstrates:

* transformer fine-tuning;
* PyTorch model training;
* Hugging Face Transformers usage;
* multi-head classification;
* class imbalance handling;
* cross-validation;
* macro F1-score optimization;
* threshold tuning;
* rule-based post-processing;
* clean project documentation.

---

## Repository Components

The repository contains:

| Component          | Purpose                                          |
| ------------------ | ------------------------------------------------ |
| `README.md`        | Main project documentation                       |
| `notebooks/`       | Main training and submission notebook            |
| `data/README.md`   | Dataset usage instructions                       |
| `docs/`            | Additional technical documentation               |
| `reports/`         | Result summary and optional leaderboard evidence |
| `assets/`          | Visual diagrams used in the README               |
| `requirements.txt` | Python dependencies                              |
| `.gitignore`       | Rules for excluding generated or large files     |

---

## Summary

This project presents a complete machine learning workflow, from dataset preparation to model training, evaluation, post-processing, and documented submission generation.

It is designed to be understandable for both technical readers and non-technical reviewers interested in the project goals, methodology, and results.
