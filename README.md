# Dry Maize Price Forecasting in Kenya

## Project Overview

This project develops a **machine learning–based time series forecasting solution** to predict **average weekly dry maize prices** in selected counties in Kenya. The forecasts support **agriBORA’s integrated storage, credit, and market intelligence platform**, helping farmers make informed decisions on **when to sell**, maximize earnings, and manage post-harvest risks.

The solution uses **historical market price data**, robust **feature engineering**, and a **tuned LightGBM regression model** to generate **rolling two-week-ahead forecasts** over a six-week horizon.

---

## Target Counties

* **Kiambu**
* **Kirinyaga**
* **Mombasa**
* **Nairobi**
* **Uasin-Gishu**

---

## Forecasting Task

* **Commodity:** Dry Maize
* **Frequency:** Weekly (average prices)
* **Forecast Horizon:** 6 consecutive weeks
* **Forecasting Style:** Recursive multi-step forecasting (2 weeks at a time)

---

## Evaluation Metrics

The competition uses **multi-metric evaluation**, with equal weighting:

* **Mean Absolute Error (MAE)** – measures average absolute prediction error (robust to outliers)
* **Root Mean Square Error (RMSE)** – penalizes large errors more strongly

### Leaderboard Score

```text
Score = 0.5 × MAE + 0.5 × RMSE
```

> ⚠️ **Important:** For submission purposes, the **same predicted value** must appear in both `Target_MAE` and `Target_RMSE` columns.

---

## Project Structure

```text
maize-price-forecasting/
│
├── Market Prices Search.csv      # Raw historical market data
├── maize_price_submission.csv    # Final submission file
├── maize_Prices.py          # Full training, validation & forecasting script
└── README.md                     # Project documentation
```

---

## Methodology

### 1. Data Preprocessing

* Filtered for **maize-related commodities**
* Converted retail prices from strings (e.g. `120/Kg`) to numeric values
* Parsed dates into proper `datetime` format

### 2. Weekly Aggregation

Daily prices are aggregated into **weekly average prices** (Monday-based weeks) to reduce noise and improve forecast stability.

### 3. Feature Engineering

**Lag Features**

* Price at 1, 2, and 3 weeks before

**Rolling Statistics**

* 4-week rolling mean
* 4-week rolling standard deviation

**Calendar Features**

* Week of year
* Month
* Year

**Categorical Encoding**

* One-hot encoding for counties

---

## Model Choice

### LightGBM Regressor

LightGBM was selected because it:

* Handles non-linear relationships well
* Performs strongly on tabular time-series data
* Is efficient and scalable

To prevent overfitting and unnecessary splits, the model uses:

* Shallow trees
* Minimum samples per leaf (`min_data_in_leaf`)

---

## Validation Strategy

Since this is a **time-series problem**, a **time-based split** is used instead of random sampling:

* **Training set:** First ~85% of historical weeks
* **Validation set:** Most recent ~15% of weeks

Validation performance is reported using **MAE and RMSE**, aligned with the competition evaluation.

---

## Hyperparameter Tuning

A focused manual grid search is performed on:

* `max_depth`
* `num_leaves`

Models are compared using the **competition score formula**:

```text
(MAE + RMSE) / 2
```

The best-performing configuration is selected and retrained on **all available data** before forecasting.

---

## Forecasting Approach

The project uses **recursive forecasting**:

1. Predict week *t+1*
2. Use that prediction as an input feature
3. Predict week *t+2*
4. Repeat until all 6 weeks are forecast

This approach closely mimics real-world forecasting conditions.

---

## Submission Format

The final submission file is a **single CSV** with the following structure:

| ID              | Target_RMSE | Target_MAE |
| --------------- | ----------: | ---------: |
| Kiambu_Week_50  |      123.45 |     123.45 |
| Nairobi_Week_50 |      118.20 |     118.20 |

**ID format:**

```text
County_Week_XX
```

* **5 counties × 6 weeks = 30 rows total**
* Identical values in `Target_RMSE` and `Target_MAE`

---

## How to Run the Project

1. **Install dependencies**

```bash
pip install pandas numpy lightgbm scikit-learn
```

2. **Place the data file**

```text
Market Prices Search.csv
```

3. **Run the script**

```bash
python maize_forecasting.py
```

4. **Output**

```text
maize_price_submission.csv
```

---

## Practical Impact

This solution enables:

* Better **price timing decisions** for farmers
* Improved **market intelligence** for agriBORA
* Stronger linkage between **storage, credit, and price signals**

The methodology is easily extendable to other crops and regions.

---

## Future Improvements

* County-specific ensemble models
* Log-price modeling for RMSE reduction
* Inclusion of supply volume or wholesale prices
* Walk-forward cross-validation
* Deployment as a REST API (FastAPI)

---

## Author

**Zachary Monari**
ZachTechs Projects
Focus: Data-driven engineering, machine learning, and agri-tech applications

---

## License

This project is for **educational and competition use**. Data ownership and usage remain subject to the original data providers.
