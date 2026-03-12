# 21 SQL Testing and Documentation

A practical reference for SQL testing patterns, data quality frameworks, dbt testing, and query documentation. All notebooks run live in SQLite with PostgreSQL and dbt patterns shown throughout. No dbt installation required.

## Dataset (mixed theme)

Insurance and healthcare tables intentionally seeded with data quality issues for meaningful test demonstrations:

- **customers** — 7 rows; C006 has NULL email
- **policies** — 10 rows; POL-009 has negative premium (−$500); POL-010 is a near-duplicate of POL-005
- **claims** — 7 rows; CLM-007 has extreme amount ($99,999)
- **lab_results** — 8 rows; LAB-005 has NULL result_value; LAB-006 is an exact duplicate of LAB-001; LAB-007 has impossible negative eGFR (−5)

Every test notebook runs against this seeded dataset — each test category has genuine violations to catch.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `testing_with_assert` | Zero-row = pass convention; `run_test()` harness; 5 assertion tests on policies (negative premiums, invalid status, NULL FK, referential integrity, date ordering); row count tests; NOT NULL completeness tests; freshness check on `MAX(created_at)`; domain range checks (eGFR ≥ 0, HbA1c 3–20, claims < $50K); exact and near-duplicate detection; accepted values tests on 3 tables; PostgreSQL `DO $$ ASSERT $$` pattern |
| `dbt_testing` | 4 built-in generic tests (unique, not_null, accepted_values, relationships) with YAML examples; full `schema.yml` for policies model; singular tests (`tests/*.sql`) with `{{ ref() }}`; custom generic test macros (`test_not_negative`, `test_accepted_range`); test configuration (severity, limit, store_failures, where, tags); all dbt test CLI commands; source freshness YAML; simulated `dbt test` run against SQLite dataset showing pass/fail per test |
| `query_documentation` | Full well-documented query with block header (model, grain, owner, dependencies) and inline `-- why` comments; step-by-step CTE documentation; `data_dictionary` table DDL and population (table, column, type, nullable, description, example, tests, owner); querying the dictionary; PostgreSQL `COMMENT ON COLUMN`; `information_schema.columns` + `pg_description` auto-generated dictionary query |
| `data_quality_checks` | DQ dashboard via `UNION ALL` (10 checks in one view, violations count per check); Z-score anomaly detection with population stddev; IQR (1.5× fence) outlier detection; referential integrity with LEFT JOIN pattern; cross-table consistency (claim > 20× premium; open claim on lapsed policy); `load_audit` table for freshness tracking; PostgreSQL `STDDEV_POP` and `PERCENTILE_CONT` equivalents |
| `testing_patterns` | TDD 5-step workflow for SQL; `run_test_suite()` Python harness with pass/fail output and `sys.exit(1)` for CI; regression baseline snapshot → compare workflow; baseline storage in PostgreSQL JSONB; GitHub Actions `dbt_ci.yml` workflow; custom Python CI runner with JSON report; 7 CI principles (fail-fast, tag:critical, store_failures, non-zero exit, CI schema, version control, ownership) |

## Key patterns

```python
# Zero-row assertion (universal SQL test pattern)
def run_test(name, sql, conn):
    rows = conn.execute(sql).fetchall()
    status = "PASS" if len(rows) == 0 else f"FAIL ({len(rows)} violations)"
    print(f"  [{status}] {name}")
    return len(rows) == 0

run_test("no_negative_premiums",
         "SELECT policy_id, premium FROM policies WHERE premium < 0",
         conn)
```

```yaml
# dbt schema.yml — built-in + custom tests
models:
  - name: policies
    columns:
      - name: policy_id
        tests: [unique, not_null]
      - name: premium
        tests: [not_null, not_negative]
      - name: status
        tests:
          - accepted_values:
              values: ['active', 'lapsed', 'cancelled']
```

```sql
-- PostgreSQL DQ dashboard view
CREATE VIEW v_dq_dashboard AS
SELECT 'negative_premiums' AS check_name, COUNT(*) AS violations
FROM   policies WHERE premium < 0
UNION ALL
SELECT 'orphan_policies', COUNT(*)
FROM   policies p LEFT JOIN customers c ON c.customer_id = p.customer_id
WHERE  c.customer_id IS NULL;
```

## Dependencies

```
python >= 3.8
jupyter
sqlite3   (stdlib — all notebooks fully runnable without PostgreSQL or dbt)
# PostgreSQL patterns: PostgreSQL 14+
# dbt patterns: dbt-postgres >= 1.5 (pip install dbt-postgres)
```

---
*sql_methods_library - Samantha McGarrigle*
