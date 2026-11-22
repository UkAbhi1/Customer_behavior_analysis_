# 📊 Customer Shopping Behavior Analytics

An end-to-end analytics project that takes a retail customer file from raw CSV to an interactive executive dashboard: **Python (pandas)** for cleaning and feature engineering, **PostgreSQL** for storage and SQL analysis, and **Power BI** for visualization.

**Business objective:** understand who buys what, and how subscriptions, discounts and shipping choices relate to spend, so management can decide where to focus retention, promotion and assortment effort.

## 1. Dataset

| Item | Detail |
|---|---|
| Domain | Retail / customer shopping behavior |
| File format | CSV (`customer_shopping_behavior.csv`) |
| Records | 3,900 customer transactions |
| Columns | 18 raw attributes → 19 in the database (1 dropped, 2 engineered) |
| Key attributes | `customer_id`, `age`, `gender`, `item_purchased`, `category`, `purchase_amount`, `location`, `size`, `color`, `season`, `review_rating`, `subscription_status`, `shipping_type`, `discount_applied`, `previous_purchases`, `payment_method`, `frequency_of_purchases` |

Profile highlights: average purchase amount $59.76 (range $20–$100), average review rating 3.75, ages 18–70, 4 categories, 25 items, 6 shipping types.

## 2. Tools & Technologies

| Tool | Purpose |
|---|---|
| Python, pandas | Data profiling, cleaning, feature engineering |
| Jupyter Notebook (VS Code) | Interactive development |
| SQLAlchemy, psycopg2 | Loading the DataFrame into PostgreSQL |
| PostgreSQL, pgAdmin | Storage and SQL business analysis |
| Power BI | Interactive dashboard |

## 3. Workflow

`Raw CSV → Python ETL → PostgreSQL (customer_behavior.customer) → SQL analysis (Q1–Q10) → Power BI dashboard`

## 4. Data Cleaning & Feature Engineering

- Profiled with `df.info()`, `df.describe()` and `df.isna().sum()`: only `Review Rating` had nulls (**37**, 0.95%).
- Filled missing ratings with the **median of each product category**.
- Standardized column names to `snake_case`; renamed `Purchase Amount (USD)` to `purchase_amount`.
- Confirmed `promo_code_used` is identical to `discount_applied` for all 3,900 rows, then dropped it.
- Engineered `age_group` (quartile bins: Young Adult 18–31, Adult 32–44, Middle Aged 45–57, Senior 58–70) and `purchase_frequency_days` (numeric mapping of purchase cadence).
- Loaded the result to PostgreSQL with `df.to_sql('customer', engine, if_exists='replace', index=False)`.

**Outcome:** 3,900 rows in, 3,900 rows out; zero nulls.

## 5. SQL Analysis

Ten business questions are answered in `Customer_behavior_query.sql`:

| Q | Question | Technique |
|---|---|---|
| 1 | Revenue: male vs. female | SUM + GROUP BY |
| 2 | Discounted purchases above the average amount | Scalar subquery |
| 3 | Top 5 products by average rating | AVG, ROUND, `::numeric` |
| 4 | Average purchase: Standard vs. Express | Filtered aggregation |
| 5 | Do subscribers spend more? | COUNT / AVG / SUM |
| 6 | Top 5 products by discount rate | Conditional aggregation |
| 7 | New / Returning / Loyal segments | CTE + CASE |
| 8 | Top 3 products per category | CTE + `ROW_NUMBER()` |
| 9 | Are repeat buyers likely to subscribe? | Filtered COUNT |
| 10 | Revenue by age group | CTE + CASE |

**Verified result (Q4):** Standard averages **$58.46**, Express **$60.48**.

## 6. Power BI Dashboard

One page (1920 × 1200) connected to the PostgreSQL table (`Get Data → PostgreSQL database`, server `localhost:5432`, database `customer_behavior`).

- **KPI cards:** Number of Customer, Avg Purchase Amount, Avg Review rating
- **Slicers:** Subscription Status, Gender, Category, Shipping Type
- **Charts:** Customer by Subscription Status (donut), Revenue by Category, Sales by Category, two age-group bar charts

![Dashboard preview](screenshots/dashboard_preview.png)

## 7. Key Insights & Recommendations

| Area | Finding | Recommendation |
|---|---|---|
| Segmentation | 68% of customers are male (2,652 vs. 1,248); loyalty tiers in Q7 are too coarse | Re-cut tiers on quartiles; size the female-customer opportunity |
| Subscription | Only 27% subscribe (1,053 of 3,900) | Target non-subscribed repeat buyers; track subscription rate |
| Discounts | 43% of purchases are discounted, all promo-code driven | Set promo guardrails; test who needs an incentive |
| Shipping | Express ($60.48) beats Standard ($58.46); Free Shipping is most used (17.3%) | Compare all six options before changing policy |
| Categories | Clothing is 44.5% of transactions; Accessories is second | Protect top items; cross-sell into weaker categories |

## 8. Known Issues & Planned Fixes

- `'Quartely'` typo in the frequency mapping leaves *Quarterly* rows as NaN in `purchase_frequency_days`.
- Q6 uses integer division; use `100.0 *` to keep decimals.
- Q7 and Q10 thresholds do not match the data distribution or the Python `age_group`.
- Two dashboard titles are mislabeled (category slicer, second age chart).
- Database password is hard-coded in the notebook; move it to an environment variable.

## 9. Project Deliverables

| Deliverable | File |
|---|---|
| Python notebook | `Customer_behavior_analysis_pynb.ipynb` |
| SQL scripts | `Customer_behavior_query.sql` |
| Power BI dashboard | `Customer_behavior_pattern.pbix` |
| Technical case study | `Customer_Behavior_Analytics_Case_Study.pdf` |
| Leadership presentation | `Customer_Behavior_Leadership_Deck_v2.pptx` |

## 10. Repository Structure

```text
Customer-Shopping-Behavior-Analytics/
│
├── dataset/
│   └── customer_shopping_behavior.csv
│
├── python/
│   └── Customer_behavior_analysis_pynb.ipynb
│
├── sql/
│   └── Customer_behavior_query.sql
│
├── powerbi/
│   └── Customer_behavior_pattern.pbix
│
├── reports/
│   └── Customer_Behavior_Analytics_Case_Study.pdf
│
├── presentation/
│   └── Customer_Behavior_Leadership_Deck_v2.pptx
│
├── screenshots/
│   └── dashboard_preview.png
│
└── README.md
```

## 11. How to Run

**Prerequisites:** Python 3.x, Jupyter, PostgreSQL with pgAdmin, Power BI Desktop.

1. Clone the repository and install dependencies:
   ```bash
   git clone https://github.com/your-username/Customer-Shopping-Behavior-Analytics.git
   cd Customer-Shopping-Behavior-Analytics
   pip install pandas psycopg2-binary sqlalchemy
   ```
2. In pgAdmin, create a database named `customer_behavior`.
3. Open the notebook, set your PostgreSQL credentials (preferably via environment variables) and run all cells. This creates the `customer` table.
4. Open `Customer_behavior_query.sql` in the pgAdmin Query Tool and run the statements one at a time.
5. Open the `.pbix` file in Power BI Desktop, update the PostgreSQL connection if needed, and refresh.

## 12. Conclusion

This project covers the full analytics lifecycle: data preparation in Python, relational storage and SQL analysis in PostgreSQL, and interactive reporting in Power BI, ending in concrete recommendations on subscriptions, discounts, shipping and product mix.

## 13. Author

**[Abhishek Ukirde]**

Aspiring Data Analyst | Python | SQL | Power BI | Data Visualization
