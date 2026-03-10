# 06 Data Cleaning in SQL

Transforming messy, real-world data into analysis-ready form entirely within SQL. All notebooks use a deliberately dirty healthcare intake dataset (`intake_raw`, `lab_raw`, `provider_raw`) created in-memory with **SQLite**. PostgreSQL alternatives and extensions are noted throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `null_handling` | IS NULL / IS NOT NULL, COALESCE, NULLIF, IFNULL, empty string vs NULL, NULL in arithmetic and concatenation |
| `deduplication` | Identifying duplicates, ROW_NUMBER dedup (keep first vs most recent), DISTINCT, dedup impact summary, near-duplicate considerations |
| `type_conversion_coercion` | CAST syntax, stripping $/, from monetary strings, the lexicographic sort trap, SQLite type affinity, PostgreSQL strictness |
| `string_cleaning` | TRIM, UPPER/LOWER, REPLACE chains, INSTR, SUBSTR, extracting units from lab results, parsing name formats, PostgreSQL REGEXP_REPLACE |
| `date_standardisation` | ISO 8601 validation with DATE(), format detection with GLOB, DD/MM/YYYY → YYYY-MM-DD conversion, STRFTIME, JULIANDAY age calculation |
| `case_when_transformations` | Searched vs simple CASE, recoding free-text, binning continuous values, multi-flag quality indicators, CASE in ORDER BY, conditional COUNT |

## Dataset

Purposely messy records simulating real healthcare intake data:
- Mixed case names (`'aroha'`, `'FATIMA'`, `'  Liam '`)
- Inconsistent date formats (`'1985-03-12'`, `'04/11/1972'`, `'Jan 30 1955'`, `'18-03-2023'`)
- Messy sex codes (`'Male'`, `'female'`, `'m'`, `'F'`, `NULL`)
- Non-standard province values (`'Ontario'`, `'bc'`, `'AB '`)
- Monetary strings (`'$3,200.00'`, `'1850'`, `'-1'`)
- Duplicate rows (record 8 = duplicate of record 2)
- Fully NULL/blank record (record 13)
- Test/sentinel record (record 14)

## Dependencies

```
python >= 3.8   (sqlite3 is in the standard library)
pandas
jupyter
```

---
*sql_methods_library - Samantha McGarrigle*
