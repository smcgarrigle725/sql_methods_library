# 08 Analytical Patterns

Core SQL patterns for product analytics, financial reporting, and behavioural analysis. These patterns appear across data science and data engineering interviews and are directly applicable to healthcare, finance, and insurance roles. All notebooks use **SQLite** (via Python's `sqlite3`) with a finance-themed schema; PostgreSQL syntax differences and idiomatic alternatives are noted throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `cohort_analysis` | Defining cohorts by acquisition period, building monthly retention tables (long and wide), cohort size denominators, JULIANDAY-based period offsets |
| `funnel_conversion` | Funnel step volumes with COUNT DISTINCT, step-by-step and overall conversion rates, drop-off analysis by channel, time-to-convert, strict vs unordered funnels |
| `retention_curves` | Monthly activity flags, rolling retention by cohort, classic vs rolling retention definitions, churn identification with 90-day inactivity threshold |
| `sessionisation` | Gap-based session detection with LAG, new-session flag via gap threshold, session ID assignment with cumulative SUM, session metrics (length, events, bounce rate) |
| `pivot_unpivot` | CASE-based pivot (counts and sums), segment x month crosstabs, UNION ALL unpivot, PostgreSQL CROSSTAB and LATERAL VALUES patterns |
| `time_series_aggregation` | Recursive CTE date spine for gap filling, 3-month moving average, cumulative totals, YoY comparison with LAG PARTITION BY month, MTD/QTD/YTD patterns |

## Dataset

Finance schema built in-memory with SQLite:
- `customers` (15 rows) — segment (Retail/Wealth/SME), province, signup_date, active flag
- `accounts` (20 rows) — account_type (Chequing/Savings/Investment/Loan), status, credit_limit
- `transactions` (27 rows) — txn_date, txn_type (Deposit/Withdrawal/Fee), amount, category
- `product_events` (32 rows) — funnel and session events: page_view, product_view, application_start, application_submit, account_opened; channel (web/mobile)

**Designed for:** customers spanning 3 cohort quarters (Q1/Q2/Q3 2023), deliberate gaps in monthly transaction data (for date spine demonstration), multi-step product funnel with drop-off at various stages, and session data with gaps > 60 minutes to trigger session boundaries.

## Dependencies

```
python >= 3.8   (sqlite3 is in the standard library)
pandas
jupyter
```

## Running notebooks

```bash
pip install pandas jupyter
jupyter notebook
```

---
*sql_methods_library - Samantha McGarrigle*
