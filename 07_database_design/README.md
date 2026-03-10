# 07 Database Design

Core principles for designing relational databases that are correct, maintainable, and performant. This folder covers the full design lifecycle from conceptual modelling through to physical implementation details. All notebooks use **SQLite** (via Python's `sqlite3`) with a PostgreSQL-focused healthcare schema; PostgreSQL syntax and behaviour differences are noted throughout.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `entity_relationship_modelling` | Entities, attributes, relationships, crow's foot cardinality notation, one-to-many, many-to-many bridge tables, self-referencing relationships, conceptual vs logical vs physical design |
| `normalisation` | 1NF (atomic values, no repeating groups), 2NF (eliminating partial dependencies), 3NF (eliminating transitive dependencies), BCNF, insert/update/delete anomalies, when to denormalise |
| `keys_constraints` | PRIMARY KEY (simple and composite), FOREIGN KEY with ON DELETE/UPDATE actions, UNIQUE, CHECK, NOT NULL, DEFAULT, referential integrity enforcement, PRAGMA foreign_keys in SQLite |
| `indexes` | B-tree indexes, composite indexes and column order (leftmost prefix rule), partial indexes, covering indexes (INCLUDE), EXPLAIN QUERY PLAN, when to index vs not, PostgreSQL GIN/GiST/BRIN overview |
| `views_materialised_views` | CREATE VIEW, views as abstraction and security layers, updatable vs non-updatable views, materialised views (PostgreSQL), REFRESH MATERIALIZED VIEW, CONCURRENTLY, view stacking pitfalls |

## Dataset

Healthcare schema: `patients`, `providers` (with `manager_id` self-reference), `departments`, `encounters`, `diagnoses`, `encounter_diagnoses` (bridge table), `lab_results` — 7 tables built in-memory with SQLite. The schema is itself a teaching example of a well-normalised 3NF design.

**Key design features demonstrated:**
- Surrogate integer PKs throughout (except `diagnoses` which uses ICD-10 natural PK)
- Composite PK on the `encounter_diagnoses` bridge table
- Self-referencing FK: `providers.manager_id -> providers.provider_id`
- Nullable FK: `providers.dept_id` (a provider may be unassigned)
- CHECK constraints: `sex IN ('M','F','O')`, `admitted IN (0,1)`
- UNIQUE constraint: `providers.licence_no`

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
