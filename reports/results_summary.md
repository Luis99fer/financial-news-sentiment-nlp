# Results Summary

This document summarizes the main results and technical decisions of the financial sentiment classification project.

---

## Project Result

The final solution achieved a strong result in the Kaggle competition **MEPhI M-25 ML Financial News Headlines**.

The model was designed to classify sentiment in Russian-language financial news headlines and comments. For each text, the system predicts whether a target financial organization is mentioned and whether the sentiment toward that organization is negative, neutral, or positive.

The final output is a Kaggle-compatible `submission.csv` file.

---

## Evaluation Metric

The competition uses **macro-averaged F1-score** as the main evaluation metric.

Macro F1-score is useful for this task because the target labels are imbalanced. Some sentiment classes and entity-sentiment combinations appear much less frequently than others, so the metric gives equal importance to each target column instead of favoring only the most frequent labels.

---

## Main Model

The final solution uses:

| Component           | Description                           |
| ------------------- | ------------------------------------- |
| Base model          | `ai-forever/ruRoBERTa-large`          |
| Model type          | Transformer-based NLP model           |
| Framework           | PyTorch                               |
| Library             | Hugging Face Transformers             |
| Task                | Multi-entity sentiment classification |
| Validation strategy | 5-fold stratified cross-validation    |
| Optimization metric | Macro F1-score                        |

---

## Key Techniques

The following techniques contributed to the final result:

* Fine-tuning `ai-forever/ruRoBERTa-large`
* Multi-head classification architecture
* Class-weighted cross-entropy loss
* 5-fold cross-validation
* Out-of-fold prediction analysis
* Per-column threshold tuning
* Entity-aware post-processing
* Kaggle submission format validation

---

## Model Architecture Summary

The model uses a shared ruRoBERTa encoder followed by multiple classification heads.

The shared encoder extracts contextual representations from the input text. Each classification head predicts the sentiment label for one target entity.

Each entity is classified into one of four possible classes:

| Class | Meaning             |
| ----- | ------------------- |
| 0     | No relevant mention |
| 1     | Negative sentiment  |
| 2     | Neutral sentiment   |
| 3     | Positive sentiment  |

The predictions are later converted into the binary format required by the competition.

---

## Post-processing Summary

The post-processing stage applies entity-aware rules to reduce false positive predictions.

For example, if a target bank is not detected in the text using rule-based entity matching, the corresponding sentiment predictions for that bank are removed.

This helps improve prediction consistency and reduces cases where the model assigns sentiment to an organization that is not explicitly mentioned.

---

## Output Files

The notebook generates the following output:

```text
outputs/submission.csv
```

The file follows the structure required by Kaggle and contains binary prediction columns for each entity-sentiment pair.

Model checkpoints and generated output files are not included in the repository because they can be large and are generated during execution.

---

## Reproducibility Notes

To reproduce the result:

1. Download the dataset from Kaggle.
2. Place the files in the `data/` directory.
3. Install the dependencies from `requirements.txt`.
4. Run the notebook located at:

```text
notebooks/financial_sentiment_ruroberta_kaggle.ipynb
```

The notebook will train the model, tune thresholds, apply post-processing, and generate the final submission file.

---

## Leaderboard Evidence

A screenshot of the Kaggle leaderboard result can be added to this directory as:

```text
reports/leaderboard_result.png
```

If available, include the screenshot to provide visual evidence of the final competition performance.

---

## Summary

This project demonstrates a complete machine learning workflow for financial sentiment analysis, including data preparation, transformer fine-tuning, validation, threshold optimization, post-processing, and documented result generation.

The final solution is suitable for a professional portfolio because it combines applied NLP, deep learning, software organization, and clear technical documentation.
