# 20 Data Warehousing Patterns

A practical reference for dimensional modelling, ETL, and analytical query patterns. All notebooks run live in SQLite (with PostgreSQL syntax shown throughout). No external database required.

## Dataset (Finance / Insurance theme)

**Star schema — insurance policy DWH:**
- `fact_policy` — 18+ rows: policy_key, date_key, customer_key, product_key, agent_key, premium_amount, coverage_amount, claim_amount, n_claims
- `dim_date` — 1,096 rows (2022–2024): date_key (YYYYMMDD), year, quarter, month, month_name, week_of_year, is_weekend, is_holiday
- `dim_customer` — 6 customers with SCD2 columns: effective_from, effective_to, is_current, province, risk_tier, segment
- `dim_product` — 7 products: auto, home, life, health lines; basic/standard/premium bands
- `dim_agent` — 4 agents: regions (East/West/Central), channels (direct/broker/online)
- `stg_policy_load` — staging table for ETL demo
- `etl_audit_log` — ETL run history: rows_extracted, rows_loaded, rows_rejected, status

## Notebooks

| Notebook | Topics covered |
|---|---|
| `dwh_concepts` | Star vs snowflake schema (diagram + comparison table); OLTP vs OLAP design differences; fact vs dimension properties (grain, grain examples, additivity types); surrogate key reasons (SCD2, source independence, -1 unknown pattern) |
| `fact_dimension_tables` | Star join: premium/claims/loss_ratio by year/product/region; dim_date column guide + PostgreSQL `GENERATE_SERIES` population; staging → fact load with dimension lookups; `COALESCE(key, -1)` for unknown members; `load_status` flag pattern |
| `slowly_changing_dimensions` | SCD1 overwrite + PostgreSQL `ON CONFLICT DO UPDATE IS DISTINCT FROM`; SCD2 two-step expire+insert with transaction; point-in-time query pattern; PostgreSQL 15+ `MERGE` approach; SCD3 prev-value column; full SCD1/2/3/4/6 comparison table |
| `etl_patterns` | 7-step ETL flow; `etl_audit_log` table with row counts; `INSERT OR REPLACE` / `ON CONFLICT` upsert; incremental load with high-watermark; lookback window for late-arriving data; truncate-reload vs incremental vs upsert comparison; idempotency; transactional batch loading |
| `analytical_queries` | YoY with `LAG(premium,1) OVER (PARTITION BY product_line ORDER BY year)`; same-month-prior-year with `LAG(col,12)`; running total with `ROWS UNBOUNDED PRECEDING`; 3-year moving average with `ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`; top-2 agents per region with `RANK()`; `RANK` vs `DENSE_RANK` vs `ROW_NUMBER` vs `NTILE` vs `PERCENT_RANK` |
| `dwh_performance` | Range partitioning DDL (year-based `PARTITION BY RANGE (date_key)`); partition pruning with integer date_key; BRIN vs B-tree for partitions; `mv_quarterly_summary` materialized view; `REFRESH MATERIALIZED VIEW CONCURRENTLY`; 5 query optimisation patterns (filter-early CTE, integer date_key, avoid non-sargable WHERE, aggregate-then-join, avoid SELECT *) |

## Key patterns

```sql
-- Year-over-year comparison
WITH yearly AS (
    SELECT dd.year, dp.product_line, SUM(fp.premium_amount) AS premium
    FROM   fact_policy fp
    JOIN   dim_date dd ON dd.date_key = fp.date_key
    JOIN   dim_product dp ON dp.product_key = fp.product_key
    GROUP  BY dd.year, dp.product_line
)
SELECT year, product_line, premium,
       LAG(premium) OVER (PARTITION BY product_line ORDER BY year) AS prev_year,
       ROUND(100.0*(premium - LAG(premium) OVER (PARTITION BY product_line ORDER BY year))
             / NULLIF(LAG(premium) OVER (PARTITION BY product_line ORDER BY year),0), 1) AS yoy_pct
FROM yearly;

-- Top-N per group
WITH ranked AS (
    SELECT da.region, da.agent_name,
           SUM(fp.premium_amount) AS total_premium,
           RANK() OVER (PARTITION BY da.region ORDER BY SUM(fp.premium_amount) DESC) AS rnk
    FROM   fact_policy fp JOIN dim_agent da ON da.agent_key = fp.agent_key
    GROUP  BY da.region, da.agent_name
)
SELECT * FROM ranked WHERE rnk <= 2;

-- SCD2 point-in-time lookup
SELECT * FROM dim_customer
WHERE customer_id = 'C004'
  AND effective_from <= '2023-12-31'
  AND (effective_to > '2023-12-31' OR effective_to IS NULL);
```

## Dependencies

```
python >= 3.8
jupyter
sqlite3   (stdlib — all notebooks fully runnable without PostgreSQL)
# PostgreSQL patterns: PostgreSQL 14+ (15+ for MERGE syntax)
```

---
*sql_methods_library - Samantha McGarrigle*
