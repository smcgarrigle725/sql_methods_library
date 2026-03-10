# 04 Window Functions

Window functions compute values across related rows without collapsing the result set — one of the most powerful and frequently tested capabilities in SQL. All notebooks use **SQLite 3.25+** (which supports window functions natively) with a finance-themed dataset. PostgreSQL differences and extensions are noted throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `window_basics` | OVER(), PARTITION BY, ORDER BY in window, frame concept, window vs GROUP BY, WINDOW clause |
| `ranking_functions` | ROW_NUMBER, RANK, DENSE_RANK, NTILE, PERCENT_RANK, CUME_DIST, top-N per group pattern |
| `lag_lead` | LAG/LEAD syntax, offset and default parameters, period-over-period change, days between events, MoM reporting |
| `running_totals_moving_averages` | ROWS vs RANGE frame modes, cumulative totals, N-row rolling averages, centred windows, forward-looking frames |
| `first_last_nth_value` | FIRST_VALUE stability, LAST_VALUE frame trap, NTH_VALUE NULL behaviour, first/last price comparison |
| `gaps_and_islands` | Gap detection with LAG, island detection with double ROW_NUMBER, island boundary summary, session detection |

## Dataset

Finance theme: `customers`, `accounts`, `transactions`, `trades`, `daily_balances`, `login_events` — all simulated in-memory SQLite.

**Key patterns demonstrated:**
- Running account balance using cumulative SUM
- Month-over-month deposit change with LAG
- Top-2 trades per account with ROW_NUMBER
- Session detection with 20-minute gap threshold
- Island grouping for consecutive transaction type runs

## SQLite version note

Window functions require SQLite ≥ 3.25.0 (released September 2018). Check your version with `SELECT sqlite_version()`. All Python distributions since ~3.7 ship with a compatible SQLite.

## Dependencies

```
python >= 3.7   (sqlite3 3.25+ included)
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
