# 18 JSON and JSONB in SQL

A practical reference for working with JSON data in SQL databases. All notebooks run live against SQLite's `json_extract()` / `json_each()` API, with complete PostgreSQL JSONB equivalents shown throughout. A PostgreSQL server is not required to run these notebooks.

## Dataset (healthcare theme)

**patients** — 5 rows with three JSON columns:
- `demographics` — `{age, dob, province, contact: {phone, email}, insurance: {plan, id}}`
- `clinical` — `{conditions: [...], allergies: [...], vitals: [{date, bp, weight_kg}, ...], smoker}`
- `preferences` — `{language, notifications: {email, sms}, portal_enabled}`

**lab_results** — 4 rows with `tests` JSON array: `[{test, value, unit, ref_range, flag}, ...]`

**medications** — 6 rows with `details` JSON object: `{drug, dose, frequency, prescriber, side_effects: [...]}`

## Notebooks

| Notebook | Topics covered |
|---|---|
| `json_in_sql_basics` | SQLite `json_extract(col,'$.path')` for scalars and nested fields; `json_array_length()`; WHERE on json_extract; boolean JSON values; `json_each()` to expand arrays to rows; condition prevalence via GROUP BY on expanded array; PostgreSQL `->` / `->>` equivalents; expression index for JSON field performance |
| `jsonb_postgresql` | All 13 JSONB operators (`->`, `->>`, `#>`, `#>>`, `@>`, `<@`, `?`, `?|`, `?&`, `||`, `-`, `#-`); `@>` containment simulation (nested object + array containment); 5 index strategies (GIN jsonb_ops, GIN jsonb_path_ops, B-tree expression, numeric cast, partial GIN); index selection guide |
| `querying_json` | Multi-join `json_each` expansion of lab tests array with filter on nested flag; HbA1c-only filter across array elements; `AVG()` on extracted JSON numeric values; condition prevalence aggregation with `GROUP_CONCAT`; JSONPath syntax table (`$.*`, `$[*]`, `? (@.field > val)`); `jsonb_path_query`, `jsonb_path_exists`, `jsonb_path_match` |
| `json_data_modeling` | Decision framework (use relational vs JSON); 7 JSON anti-patterns (EAV-in-JSON, frequent-update fields, arrays-as-sets, deep nesting, missing index, mixed storage, double-encoding); refactoring `conditions` array → `patient_conditions` junction table; hybrid schema rationale for each column type |
| `json_transformations` | `json_object()` building from columns; `json_group_array()` for province-based aggregation; nested `json_group_array(json_object(...))` for medications-per-patient; PostgreSQL `json_agg`, `json_object_agg`, `row_to_json`, `to_jsonb`; `json_set`/`jsonb_set` in-place updates; `json_remove`/`col - 'key'`; `json_patch`/`col || patch` merge |

## Key patterns demonstrated

```sql
-- SQLite: extract and filter nested JSON
SELECT full_name, json_extract(demographics,'$.province') AS province
FROM   patients
WHERE  json_extract(demographics,'$.age') > 50;

-- SQLite: expand JSON array to rows
SELECT p.full_name, jc.value AS condition
FROM   patients p, json_each(json_extract(p.clinical,'$.conditions')) AS jc;

-- PostgreSQL equivalent:
SELECT p.full_name, c.value AS condition
FROM   patients p, jsonb_array_elements_text(p.clinical->'conditions') AS c(value);

-- PostgreSQL: containment (GIN-indexed)
WHERE clinical @> '{"conditions":["Hypertension"]}';

-- Build nested JSON output
SELECT json_group_array(json_object('drug', json_extract(details,'$.drug'),
                                    'dose', json_extract(details,'$.dose')))
FROM   medications WHERE patient_id = 'P001';
```

## Dependencies

```
python >= 3.8
jupyter
sqlite3   (stdlib — json_extract requires SQLite >= 3.38.0)
# PostgreSQL examples: psycopg2-binary + PostgreSQL 14+ for JSONPath
```

---
*sql_methods_library - Samantha McGarrigle*
