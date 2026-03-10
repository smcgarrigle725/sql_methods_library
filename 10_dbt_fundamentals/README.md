# 10 dbt Fundamentals

A complete practical reference for dbt (data build tool) concepts. Because dbt requires a live database connection and cannot run against in-memory SQLite, each notebook uses Python to write, display, and explain realistic dbt project files (SQL models, YAML configuration, Jinja templates) and annotate CLI output — exactly how dbt is learned and used on the job.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `dbt_project_structure` | Project directory layout; `dbt_project.yml` (name, paths, layer materializations, vars); `profiles.yml` (dev/prod targets, `env_var()`, threads); full dbt CLI command reference (run, test, build, docs, compile, snapshot, source freshness) |
| `models_and_materializations` | The four materializations (view, table, incremental, ephemeral); config block syntax; compiled SQL for view and table models (with inlined ephemeral CTE); materialization decision matrix; PostgreSQL index declarations in config |
| `sources_and_refs` | `_sources.yml` structure (database, schema, freshness thresholds, `loaded_at_field`); source-level and column-level schema tests; how `ref()` and `source()` compile in dev vs prod; DAG selection syntax (`+`, `*`, `tag:`, `config.`, `source:`); `dbt source freshness` |
| `tests_and_documentation` | Generic tests (unique, not_null, accepted_values, relationships); dbt-utils and dbt-expectations packages; singular tests (SQL returning zero rows); `severity: error` vs `warn`; `store_failures: true`; model and column `description:` blocks; `meta:` owner/SLA fields; `dbt docs generate` + `dbt docs serve` |
| `jinja_and_macros` | Jinja syntax in dbt (`{{ }}`, `{% %}`, `{# #}`); `var()`, `env_var()`, `target.name`, `target.type`, `this`; `if/else` and `for` loop examples; custom macros (`cents_to_dollars`, `safe_divide`); runtime variable overrides with `--vars` |
| `incremental_models` | `is_incremental()` pattern; first-run vs incremental-run behaviour; `{{ this }}`; incremental strategies (append, merge, delete+insert); `unique_key`; late-arrival lookback windows; `--full-refresh` lifecycle; `on_schema_change` config |

## Design note

All notebooks are self-contained: they print realistic dbt file contents and annotated CLI output, and explain each concept inline. No database connection or dbt installation is required to read and learn from them. To run dbt against a real database, follow the setup instructions in `dbt_project_structure`.

## Dependencies

```
python >= 3.8
jupyter
# Optional (to actually run dbt):
pip install dbt-postgres
```

---
*sql_methods_library - Samantha McGarrigle*
