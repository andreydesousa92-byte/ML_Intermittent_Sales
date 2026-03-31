# Intermittent Sales Classification

A machine learning template for predicting whether a customer will place a purchase order in the following month. Designed for B2B sales environments with irregular demand patterns, where proactive outreach can improve commercial efficiency.

---

## Business Context

In B2B sales, customer demand is often inconsistent. Some customers order every month, others go quiet for extended periods. Without a systematic approach, sales teams either contact everyone — wasting effort — or rely on intuition, missing real opportunities.

This project addresses that problem by generating a monthly ranked contact list with purchase probability scores, allowing the sales team to prioritize outreach based on data rather than guesswork.

---

## What This Project Does

Starting from customer-level behavioral features engineered from transactional sales history, the pipeline:

1. Trains and compares four models .: Logistic Regression, Random Forest, XGBoost, and Deep Learning
2. Evaluates models using Precision-Recall analysis aligned with the business context
3. Exports the best model to Google Drive
4. Scores new customer data and outputs a ranked contact list with priority tiers

---

## Repository Structure
```
├── model_training.ipynb    — feature engineering, model training, evaluation and export
├── model_scoring.ipynb     — loads saved model and scores new customer data
├── sample_input.csv        — example input file with required feature schema
└── README.md
```

---

## How to Use

### Step 1 — Train the model

1. Open `model_training.ipynb` in Google Colab
2. Replace the data source URLs with your own sales data keeping the column names and same patterns as the example
3. Run all cells — the notebook will train all models, compare them, and save the best one to your Google Drive at `Intermitent_sales_model_ML/`

### Step 2 — Score new customers

1. Open `model_scoring.ipynb` in Google Colab
2. Mount the same Google Drive used in Step 1
3. Prepare your input file following the schema in `sample_input.csv`
4. Run all cells — the notebook outputs a ranked contact list and saves it as `customer_contact_list.csv`

---

## Input Data Schema

Your scoring input must contain the following columns, engineered from raw transactional data:

| Column | Description |
|---|---|
| `keyId` | Unique customer identifier |
| `Discount` | Whether the customer received a discount in the current month (0 or 1) |
| `Sales_Orders_Lag_1` | Number of orders placed 1 month ago |
| `Sales_Orders_Lag_2` | Number of orders placed 2 months ago |
| `Sales_Orders_Lag_3` | Number of orders placed 3 months ago |
| `months_since_last_sale` | Months elapsed since the customer last placed an order |
| `Activity_Ratio` | Cumulative share of months with at least one order |
| `Activity_Rate_3M` | Share of the last 3 months with at least one order |
| `Activity_Recency_Interaction` | Activity ratio weighted by recency |
| `Season_Activity_Interaction` | Seasonal signal combined with activity ratio |
| `Num_SalesOrders_L3M` | Total number of orders in the last 3 months |

See `sample_input.csv` for a populated example.

---

## Output

The scoring notebook produces a ranked DataFrame with the following columns:

| Column | Description |
|---|---|
| `keyId` | Customer identifier |
| `purchase_probability` | Predicted probability of placing an order next month |
| `contact_flag` | 1 if probability is above the decision threshold, 0 otherwise |
| `priority` | Priority tier — Very High, High, Medium, or Low |
| `recommended_action` | Suggested sales action for each customer |

---

## Threshold

The default decision threshold is **0.43**, selected based on Precision-Recall analysis of the Northwind dataset. This threshold captures approximately 65–84% of real buyers depending on the run, with precision meaningfully above the random baseline.

Adjust `THRESHOLD` in both notebooks if your business context requires a different tradeoff between recall and precision.

---

## Adapting to Your Own Data

This project was built with the public Northwind dataset as a demonstration. To use it with your own data:

1. Replace the data source URLs in `model_training.ipynb` with your own sales history
2. Ensure your data produces the features listed in the input schema above
3. Re-run `model_training.ipynb` to train and export a model on your data
4. Use `model_scoring.ipynb` with your own monthly customer extracts

The feature engineering logic in `model_training.ipynb` can serve as a reference for building the required features from raw transactional data.

---

## Known Limitations

- **Small dataset** — the Northwind dataset contains approximately 1,000 training samples. Results may vary between runs due to the stochastic nature of the Deep Learning model.
- **Short validation window** — the validation set covers approximately 4 months of data, which limits the statistical confidence of the evaluation metrics.
- **Feature engineering required** — the scoring notebook expects pre-engineered features. Users are responsible for replicating the feature logic on their own data.
- **Model selection** — Deep Learning was selected as the production model on this dataset. With a larger dataset, model performance and rankings may shift significantly.

---

## Dataset

[Northwind Traders](https://github.com/andreydesousa92-byte/ML_Intermittent_Sales) — a public fictional dataset representing a gourmet food supplier, commonly used for SQL and analytics demonstrations.