# 09 Query Optimization

Query performance fundamentals using PostgreSQL as the primary dialect, with SQLite used for live `EXPLAIN QUERY PLAN` demonstrations. The ecological dataset (field sites, species observations, water quality samples) provides a realistic multi-table schema for demonstrating index effects, join plans, and query rewrites.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `explain_analyze` | Reading `EXPLAIN QUERY PLAN` in SQLite; annotated PostgreSQL `EXPLAIN ANALYZE` output (Seq Scan, Index Scan, Hash Join, Sort); key plan node fields (cost, rows, loops, width); diagnostic checklist; EXPLAIN options (BUFFERS, FORMAT JSON, TIMING OFF) |
| `index_strategies` | B-tree, Hash, GIN, GiST, BRIN index types; EXPLAIN QUERY PLAN before/after index creation; composite index leftmost-prefix rule (5 test cases); partial indexes on at-risk species; covering indexes with INCLUDE; low-cardinality index anti-pattern |
| `join_optimisation` | Hash Join, Nested Loop, Merge Join algorithms; SQLite join plans with FK indexes; fan-out from joining before aggregating (demonstrated and fixed); PostgreSQL join algorithm hints (`enable_hashjoin`, `join_collapse_limit`); join order forcing |
| `avoiding_full_scans` | Patterns that prevent index use: function on column, arithmetic on column, leading-wildcard LIKE, implicit type mismatch; EXPLAIN before/after for each; `IN (subquery)` vs JOIN; OR on different columns; functional indexes as a workaround |
| `query_refactoring` | Correlated scalar subquery vs CTE pre-aggregation (performance and correctness comparison); CTE materialisation behaviour in PostgreSQL 11 vs 12+; `MATERIALIZED` / `NOT MATERIALIZED` hints; temp table vs CTE decision guide; deep-nesting refactor to flat CTEs; SELECT * anti-pattern |

## Dataset

Ecological monitoring schema (in-memory SQLite):
- `sites` (10 rows) — 9 active monitoring sites across 5 Canadian regions (Atlantic, Great Lakes, Boreal, Pacific, St Lawrence)
- `species` (15 rows) — Birds, Fish, Mammals, Reptiles; 3 at-risk species flagged
- `field_crew` (5 rows) — Biologists, Technicians, Student (certified flag)
- `observations` (32 rows) — site × species × crew × date; count_individuals, life_stage, method
- `water_quality` (15 rows) — pH, dissolved O2, turbidity, temperature, conductivity per site per date

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
