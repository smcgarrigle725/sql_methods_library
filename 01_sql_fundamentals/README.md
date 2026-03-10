# 01 SQL Fundamentals

Core SQL — the building blocks used in every query. All notebooks use **SQLite** via Python's built-in `sqlite3` module. No database server is required. PostgreSQL syntax differences are noted inline throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `select_basics` | SELECT, column aliases, CASE expressions, DISTINCT, WHERE, ORDER BY, LIMIT, NULL handling with COALESCE |
| `filtering_where` | Comparison operators, BETWEEN, IN/NOT IN, LIKE, IS NULL, AND/OR/NOT precedence, HAVING |
| `data_types_casting` | SQLite type affinity, PostgreSQL strict typing, CAST, integer division, date strings, NULLIF, COALESCE |
| `string_functions` | TRIM, UPPER/LOWER, LENGTH, SUBSTR, INSTR, REPLACE, concatenation, GROUP_CONCAT / STRING_AGG |
| `date_time_functions` | STRFTIME, JULIANDAY, date arithmetic, length of stay, age calculation, period bucketing, timezone notes |
| `order_by_limit` | Single/multi-column ORDER BY, LIMIT, OFFSET, keyset pagination, NULL sort order, custom sort sequences |

## Dataset

Healthcare theme: `patients`, `encounters`, `lab_results`, `providers`, `admissions`, `prescriptions` — all simulated in-memory SQLite.

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
