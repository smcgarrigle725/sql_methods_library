# 15 Regex and Advanced SQL Pattern Matching

A practical reference for regex in SQL, focused on messy real-world healthcare data: validating and normalising phone numbers, MRNs, dosages, lab values, and extracting clinical measurements from free-text notes. All notebooks run live against SQLite with a registered Python `re` function; PostgreSQL syntax is shown throughout every section.

## Dataset (healthcare theme)

- **patients** — patient_id, full_name, dob, phone (multiple formats), email, province, mrn, postal_code
- **clinical_notes** — note_id, patient_id, note_date, note_text (free text with BP readings, HbA1c, medications)
- **medications** — med_id, patient_id, drug_name, dosage (messy: `'10mg'`, `'10 MG'`, `'2.5 mg/day'`), frequency
- **lab_results** — lab_id, patient_id, test_name, raw_value (messy: `'7.2%'`, `'>60 mL/min'`, `'<5.7%'`), result_date

## Notebooks

| Notebook | Topics covered |
|---|---|
| `regex_basics` | LIKE vs regex vs SIMILAR TO; PostgreSQL `~` / `~*` / `!~` / `!~*` operators; SQLite REGEXP function; character classes, quantifiers, anchors (`^`, `$`); phone number format matching (3 formats); MRN validation (`^MRN-[0-9]{6}$`); email and Canadian postal code validation |
| `regex_capture_groups` | `regexp_match` → `TEXT[]`; capture group indexing (1-based); `regexp_matches` with `'g'` flag for all matches; `regexp_replace` with backreferences (`\1`, `\2`); extracting BP readings (systolic/diastolic) from notes; dosage normalisation with backreference replacement; flags (`g`, `i`, `gi`, `n`) |
| `regex_in_data_cleaning` | Dosage normalisation pipeline (`'10 MG'` → `'10mg'`); lab value parsing (qualifier `<>~`, numeric, unit); phone normalisation to NNN-NNN-NNNN; preview-before-UPDATE workflow; COALESCE for NULL safety; PostgreSQL bulk UPDATE equivalents |
| `advanced_pattern_matching` | Alternation `(BID\|BD\|TID\|QID\|PRN)`; frequency term classification; `^` / `$` anchors vs `\b` word boundary; Python `\b` vs PostgreSQL `\m`/`\M`/`\y`; `\d`/`\s` not in PostgreSQL ERE; lookahead workaround (AND + `!~`); clinical negation detection (`no allergies` vs `allergic to`); greedy vs specific character class |
| `regex_performance` | Full scan vs indexed equality timing demo (1000-row table); B-tree index usage rules; `IN()` instead of alternation regex; `LIKE 'prefix%'` instead of leading-anchor regex; `pg_trgm` trigram index setup and usage; `similarity()` for fuzzy matching; functional index on regex boolean; `statement_timeout` safety net; 6-strategy ranked summary |

## Key patterns demonstrated

```sql
-- MRN validation
WHERE mrn ~ '^MRN-[0-9]{6}$'

-- Extract BP from note text
(regexp_match(note_text, '(\d+)/(\d+)\s*mmHg'))[1]::int AS systolic

-- Normalise dosage
regexp_replace(lower(regexp_replace(dosage,' ','','g')), '/\w+$', '')

-- Allergy negation workaround (no lookahead in PostgreSQL)
WHERE note_text ~* 'allerg' AND note_text !~* '\m(no|not)\s+allerg'

-- Performance: use IN() not alternation regex
WHERE province IN ('NB','ON','BC')   -- not: ~ '^(NB|ON|BC)$'
```

## Dependencies

```
python >= 3.8
jupyter
sqlite3   (stdlib)
re        (stdlib)
# For PostgreSQL examples: psycopg2-binary + pg_trgm extension
```

---
*sql_methods_library - Samantha McGarrigle*
