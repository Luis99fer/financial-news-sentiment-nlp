# Data

This directory is used to store the dataset files required to run the notebook.

The dataset comes from the Kaggle competition:

**MEPhI M-25 ML Financial News Headlines**
https://www.kaggle.com/competitions/me-ph-i-m-25-ml-financial-news-headlines/data

---

## Required Files

To reproduce this project, download the competition data from Kaggle and place the following files in this directory:

```text
data/
├── train.csv
├── test.csv
└── sample_submission.csv
```

---

## File Descriptions

| File                    | Description                                                             |
| ----------------------- | ----------------------------------------------------------------------- |
| `train.csv`             | Training dataset containing text samples and labeled sentiment targets. |
| `test.csv`              | Test dataset containing text samples used for prediction.               |
| `sample_submission.csv` | Required Kaggle submission format.                                      |

---

## Why the Data Is Not Included

The raw dataset files are not included in this repository because they are provided through Kaggle and may be subject to competition data usage rules.

This repository includes the code, documentation, and project structure required to reproduce the solution after downloading the dataset from Kaggle.

---

## Expected Local Structure

After downloading the files, the local project structure should look like this:

```text
financial-news-sentiment-nlp/
├── data/
│   ├── README.md
│   ├── train.csv
│   ├── test.csv
│   └── sample_submission.csv
│
└── notebooks/
    └── financial_sentiment_ruroberta_kaggle.ipynb
```

The notebook automatically looks for the dataset files in the `data/` directory.

---

## Usage Notes

Do not commit the raw dataset files to GitHub.

The `.gitignore` file is configured to ignore CSV files inside this directory while keeping this `README.md` file tracked.

Expected Git behavior:

| File                         | GitHub Status |
| ---------------------------- | ------------- |
| `data/README.md`             | Included      |
| `data/train.csv`             | Ignored       |
| `data/test.csv`              | Ignored       |
| `data/sample_submission.csv` | Ignored       |

---

## Reproducibility

To reproduce the project:

1. Download the dataset from Kaggle.
2. Place the required files inside this `data/` directory.
3. Run the notebook located at:

```text
notebooks/financial_sentiment_ruroberta_kaggle.ipynb
```

The notebook will load the local data files and generate the final Kaggle submission file.
