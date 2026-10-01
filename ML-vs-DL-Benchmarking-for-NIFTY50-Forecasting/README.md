# ML vs DL Benchmarking for NIFTY 50 Forecasting

A benchmark of **classical machine learning** and **deep learning** models for predicting the next-day **NIFTY 50** price (`Open`, `High`, `Low`, `Close`) from a sliding window of past prices.

Notebook: [`NIFTY50_ML_vs_DL_Benchmarking.ipynb`](NIFTY50_ML_vs_DL_Benchmarking.ipynb)

## Dataset

`data.csv`: daily NIFTY 50 OHLC prices, **6,315 rows from 2000-01-03 to 2025-05-26**, no missing values.

## Approach

1. **Windowing**: for each target column and each window size (30, 45, 60, 90, 120, 150, 200, 250 days), build `(X, y)` pairs where `X` is the previous *n* values and `y` is the next value. That gives 4 columns x 8 windows = **32 datasets**.
2. **Models** (13 per dataset, **416 trained models** in total):
   - **ML:** Linear Regression, Ridge, Lasso, Random Forest, Gradient Boosting, SVR, KNN, XGBoost, LightGBM
   - **DL (Keras):** SimpleRNN, LSTM, GRU, Bidirectional LSTM (50 units, Adam, MSE loss, 50 epochs, batch size 8)
3. **Evaluation**: 90/10 train/test split; **MAE** and **RMSE** on train and test.
4. **Analysis**: rank all models by test MAE, then look at which window size, target column and model family dominate the top 50.

## Key findings

| Rank | Model | Window | Target | Test MAE | Test RMSE |
|-----:|-------|-------:|--------|---------:|----------:|
| 1 | Linear Regression / Ridge | 30 days | High | 46.97 | 81.34 |
| 3 | KNN | 90 days | High | 48.79 | 74.20 |
| 4 | KNN | 250 days | High | 49.07 | 73.11 |

- **Best model families:** KNN, Ridge, Linear Regression and Random Forest dominate the top 50 (LightGBM appears less often).
- **Deep learning:** no RNN / LSTM / GRU / BiLSTM model made the top 50 in this experiment.
- **Best window sizes:** 30, 90 and 120 days are the most represented.
- **Best target:** models predicting **`High`** dominate the top 50.

## Run it

```bash
pip install -r requirements.txt
jupyter notebook NIFTY50_ML_vs_DL_Benchmarking.ipynb
```

Keep `data.csv` in the same folder as the notebook. Training all 416 models takes a while (the DL models are the slow part); a GPU runtime such as Google Colab helps.

## Notes and limitations

- The train/test split is **random** (`train_test_split`) and the windows overlap heavily, so test samples are very close to training samples. A **chronological split** (train on the past, test on the future) or walk-forward validation would give a more realistic estimate, especially for the deep learning models.
- Models are fit on **raw prices**, which are non-stationary, so scaling or predicting returns could change the ranking.
- Past prices alone do not make a trading strategy; this is a modelling benchmark, not financial advice.

## Next steps

- Hyperparameter tuning of the top ML models and window sizes
- Chronological / walk-forward evaluation
- Feature scaling and return-based targets for the DL models
