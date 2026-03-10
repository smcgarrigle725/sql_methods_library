# 03 Aggregation and Grouping

Collapsing rows into summaries — the backbone of every financial report, dashboard, and analytical query. All notebooks use **SQLite** via Python's `sqlite3` module with a finance-themed dataset. PostgreSQL extensions (FILTER, ROLLUP, CUBE, GROUPING SETS, statistical aggregates) are documented as reference syntax throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `group_by_having` | GROUP BY mechanics, logical execution order, WHERE vs HAVING, GROUP BY on expressions, multi-condition HAVING |
| `aggregate_functions` | COUNT variants, SUM/AVG/MIN/MAX, NULL behaviour, AVG denominator, MIN/MAX on text and dates, PostgreSQL statistical aggregates |
| `conditional_aggregation` | CASE WHEN inside aggregates, counting subsets, conditional SUM, pivot tables, monthly breakdowns, PostgreSQL FILTER clause |
| `rollup_grouping_sets` | ROLLUP subtotals, CUBE all-combinations, GROUPING SETS, SQLite UNION ALL workaround, GROUPING() function |
| `null_in_aggregation` | COUNT(*) vs COUNT(col), AVG denominator shift, SUM of all-NULL group, NULL groups in GROUP BY, flagged-rate calculation traps |

## Dataset

Finance theme: `customers`, `accounts`, `transactions`, `loans` — all simulated in-memory SQLite.

**Key columns used:**
- `transactions.amount` — positive (credit) and negative (debit), with NULLs in `flagged` and `category`
- `transactions.txn_type` — Deposit, Withdrawal, Fee, Interest, Transfer
- `loans.principal`, `loans.rate_pct`, `loans.status` — Current, Delinquent, Paid Off, Default
- `customers.segment` — Retail, SME, Wealth
- `accounts.account_type` — Chequing, Savings, Investment, Loan

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
