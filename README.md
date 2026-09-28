# 📰 Fake News Classifier — Streamlit

A Streamlit web application that classifies news text as **REAL** or **FAKE** using the trained NLP model from the accompanying notebook.

## Project structure

```text
fake-news-classifier/
├── app.py
├── fake_news_lr_model.joblib
├── fake_news_tfidf_vectorizer.joblib
├── requirements.txt
└── README.md
```

## 1. Add your trained model files

Place these two files in the same folder as `app.py`:

```text
fake_news_lr_model.joblib
fake_news_tfidf_vectorizer.joblib
```

The original Kaggle CSV files are **not required at prediction time**.

## 2. Run locally

Create and activate a virtual environment:

### Windows

```bash
python -m venv .venv
.venv\Scripts\activate
```

### macOS/Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run:

```bash
streamlit run app.py
```

The app will normally open at:

```text
http://localhost:8501
```

## 3. Prediction pipeline

The application mirrors the training notebook's preprocessing:

```text
Headline + Article
        ↓
Reuters dateline cleanup
        ↓
Lowercase / character cleaning
        ↓
Tokenization
        ↓
Stopword removal
        ↓
Lemmatization
        ↓
Fitted TF-IDF vectorizer
        ↓
Logistic Regression
        ↓
REAL / FAKE + probability
```

The model convention is:

```text
0 = FAKE
1 = REAL
```

The app combines the headline and article before preprocessing.

## 4. Training configuration

According to the training notebook:

- TF-IDF
- `max_features=5000`
- `ngram_range=(1, 2)`
- `min_df=5`
- Logistic Regression
- `max_iter=1000`
- `random_state=42`

The notebook reported these held-out test results for Logistic Regression:

| Metric | Score |
|---|---:|
| Accuracy | 98.04% |
| Precision | 97.47% |
| Recall | 98.96% |
| F1 Score | 98.21% |

These are results on the notebook's test split, not a guarantee of performance on new real-world news.

## 5. Leakage handling

The training notebook identified the `subject` column as a source of data leakage because subject categories were strongly associated with the class. The model therefore does not use `subject`.

The notebook also identified Reuters datelines as a major leakage signal. The app removes leading Reuters-style datelines and uses `reuters`, `ap`, `afp`, and `said` in its stopword list, matching the training preprocessing.

Do not change preprocessing without retraining the model.

## 6. Important limitation

This application is a **text classification model**, not a fact-checking system.

A prediction of `REAL` does not mean the app has independently verified every claim in the article. Likewise, `FAKE` does not establish that every claim is false.

## 7. GitHub deployment

Create a GitHub repository, for example:

```text
fake-news-classifier
```

Upload:

```text
app.py
fake_news_lr_model.joblib
fake_news_tfidf_vectorizer.joblib
requirements.txt
README.md
```

Do not upload:

```text
.venv/
__pycache__/
```

The original Kaggle dataset is not needed for the deployed prediction app.

## 8. Streamlit Community Cloud deployment

1. Sign in to Streamlit Community Cloud with GitHub.
2. Create a new app.
3. Select your repository.
4. Select the `main` branch.
5. Set the app file to:

```text
app.py
```

6. Deploy.

Streamlit will install the dependencies from `requirements.txt` and load the two `.joblib` files from the repository.

## 9. Troubleshooting

### Model file not found

Check that these files are in the repository root:

```text
fake_news_lr_model.joblib
fake_news_tfidf_vectorizer.joblib
```

The names must match exactly.

### NLTK resource error

The app attempts to download the required NLTK resources automatically at startup.

### `.joblib` loading/version error

A saved scikit-learn model can be sensitive to library versions. If the model fails to load after deployment, use a compatible scikit-learn version—the same version used when the model was trained/saved—and pin that version in `requirements.txt`.

## 10. Possible future improvements

- Example news buttons
- Prediction history
- Batch CSV prediction
- Model explanation using Logistic Regression coefficients
- Confusion matrix / model performance page
- URL-based article extraction
- External fact-checking
- Multiple model comparison
- Improved UI and branding
