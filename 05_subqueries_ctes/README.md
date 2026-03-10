# 05 Subqueries and CTEs

Composing queries from smaller, named pieces — from simple scalar lookups to recursive hierarchy traversal. All notebooks use **SQLite** via Python's `sqlite3` module with a healthcare-themed dataset. PostgreSQL syntax differences and performance notes are included throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `scalar_subqueries` | Single-value subqueries in SELECT, WHERE, HAVING; zero-row NULL case; repeated subquery inefficiency |
| `correlated_subqueries` | Per-row execution, EXISTS/NOT EXISTS semi-join and anti-join, the NOT IN NULL trap, greatest-N-per-group |
| `derived_tables` | Subqueries in FROM, filtering on aggregates, filtering on window function results, pre-aggregating before joins, multi-layer nesting |
| `basic_ctes` | WITH clause syntax, single and multiple CTEs, chaining CTEs, reusing a CTE twice, CTE vs derived table readability |
| `recursive_ctes` | Anchor + recursive member structure, top-down org chart traversal, bottom-up ancestor lookup, integer and date sequence generation, gap-free date spine |

## Dataset

Healthcare theme: `patients`, `providers` (with self-referencing `manager_id`), `departments`, `encounters`, `diagnoses`, `lab_results` — all simulated in-memory SQLite.

**Key patterns demonstrated:**
- Per-patient average cost comparison (correlated subquery vs window function)
- NOT EXISTS anti-join for patients with no encounters
- Pre-aggregating encounters and lab results before joining
- Multi-CTE pipeline: base → enrich → classify → summarise
- Recursive org chart from provider hierarchy
- Recursive date spine for gap-free monthly reporting

## Dependencies

```
python >= 3.8   (sqlite3 is in the standard library; recursive CTEs require 3.8.3+)
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
