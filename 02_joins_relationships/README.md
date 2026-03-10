# 02 Joins and Relationships

How SQL combines data across tables. This folder covers every join type from basic INNER JOIN through self joins, cartesian products, and set operations. All notebooks use SQLite with a consistent healthcare schema across all six notebooks.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `inner_join` | INNER JOIN mechanics, ON clause, table aliases, column qualification, non-equi joins, aggregating after a join |
| `left_right_outer_joins` | LEFT/RIGHT JOIN, anti-join pattern (LEFT JOIN + IS NULL), ON vs WHERE filter placement, NULL in unmatched rows |
| `full_outer_cross_joins` | FULL OUTER JOIN (SQLite emulation + PostgreSQL syntax), CROSS JOIN, cartesian products, reporting grids, combination generation |
| `self_joins` | Self-referencing tables, provider hierarchy traversal, peer matching, referral network, finding duplicates |
| `multi_table_joins` | Chaining 4–6 tables, CTE-based decomposition, cardinality checking before aggregation, readability strategies |
| `set_operations` | UNION / UNION ALL, INTERSECT, EXCEPT, column alignment rules, EXCEPT vs anti-join comparison |

## Dataset

Healthcare schema shared across all notebooks:
- `patients` — 11 patients (one with no encounters, to demonstrate outer join behaviour)
- `providers` — 7 providers with supervisor hierarchy (one with no encounters)
- `departments` — 7 departments linked to head providers
- `encounters` — 14 encounters (two with NULL diagnosis codes)
- `diagnoses` — 11 ICD-10 code lookup entries
- `referrals` — 5 referrals (provider-to-provider, self-join demo)

All data simulated in-memory SQLite — no server required.

## Dependencies

```
python >= 3.8   (sqlite3 is in the standard library)
pandas
jupyter
```

## Key concepts per notebook

- **inner_join:** Only matching rows — NULLs and non-matching rows are silently excluded
- **left_right_outer_joins:** LEFT JOIN + IS NULL is the standard anti-join pattern; ON vs WHERE placement changes results for outer joins
- **full_outer_cross_joins:** SQLite FULL OUTER emulation = LEFT JOIN UNION ALL anti-join from right side
- **self_joins:** Same table aliased twice; use `a.id < b.id` to avoid duplicate pairs
- **multi_table_joins:** Check row counts at each join step before aggregating
- **set_operations:** UNION ALL is almost always preferred over UNION; column names come from first SELECT

---
*sql_methods_library - Samantha McGarrigle*
