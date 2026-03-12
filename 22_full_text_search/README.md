# 22 Full-Text Search

A practical reference for PostgreSQL full-text search using `tsvector`, `tsquery`, and GIN indexes. All notebooks run live using SQLite FTS5 (porter stemmer) with complete PostgreSQL `tsvector`/`tsquery` syntax shown throughout. No PostgreSQL installation required.

## Dataset (healthcare theme)

Clinical notes and research articles for 5 patients across NB, ON, BC, NS, and QC:

- **patients** — 5 records
- **clinical_notes** — 8 notes (progress, discharge, consult, operative); rich medical narrative covering hypertension, diabetes, asthma, CKD, and medication management
- **medications** — 7 records (Lisinopril, Metformin, Empagliflozin, Insulin glargine, Salbutamol)
- **research_articles** — 5 articles on diabetes/renal, hypertension/ACE inhibitors, asthma, glycaemic control, CKD/blood pressure
- **notes_fts** — SQLite FTS5 virtual table with porter stemmer (equivalent of PostgreSQL GIN on tsvector)
- **articles_fts** — FTS5 virtual table with title, abstract, keywords columns

## Notebooks

| Notebook | Topics covered |
|---|---|
| `fts_basics` | tsvector mechanics (tokenise → normalise → stem → remove stops → record positions); PostgreSQL `to_tsvector()` output examples; `to_tsquery()` operators (`&`, `\|`, `!`, `<->`, `<N>`); `plainto_tsquery`, `phraseto_tsquery`, `websearch_to_tsquery`; `@@` match operator; live FTS5 MATCH demos across notes and articles; `GENERATED ALWAYS AS` tsvector column DDL |
| `ranking_and_relevance` | `ts_rank()` frequency scoring; 7 normalization options (0, 1, 2, 4, 8, 16, 32); `ts_rank_cd` cover density; SQLite FTS5 BM25 live demo ranking 'diabetes' notes; `ts_headline()` syntax and 8 configuration options (MaxWords, MinWords, MaxFragments, StartSel, HighlightAll); FTS5 `snippet()` live demo with `[` `]` markers; `setweight()` A/B/C/D field weighting; combined weighted tsvector stored column pattern |
| `advanced_search` | FTS5 phrase search with double quotes; 5 phrase/proximity live demos; PostgreSQL `<->` and `<N>` positional operators; `phraseto_tsquery` vs `to_tsquery` for phrases; FTS5 prefix `*` wildcard (hyper\*, diabet\*, HbA\*); PostgreSQL `:*` prefix operator; `websearch_to_tsquery` syntax table (minus, quotes, OR, spaces); built-in config comparison (`english` vs `simple` vs `french`); to_tsvector stemming vs no-stemming examples; custom medical synonym dictionary workflow; bilingual English+French config pattern |
| `fts_indexes` | GIN vs GiST 6-factor comparison (build, query, size, update, false positives, best for); GIN DDL (basic, fastupdate, expression, 3-way field combined); GiST DDL; EXPLAIN ANALYZE good vs bad plan examples; 3 storage approaches compared (inline expression, stored generated column, trigger-maintained) with pros/cons; batch tsvector population with `pg_sleep` pacing; `gin_clean_pending_list()`; VACUUM for dead GIN postings |
| `fts_patterns` | Autocomplete prefix search with `*` live demo; PostgreSQL `search_terms` table + `text_pattern_ops` B-tree for fast suggestions; multilingual schema (per-row language column + dynamic `to_tsvector(language::regconfig, ...)`); English+French dual-column pattern; combined `english`+`simple` index for acronyms; patient chart search with JOIN to patients + FTS5 `snippet()`; literature search with BM25 ranking; PostgreSQL `websearch_to_tsquery` + `ts_rank_cd` + `ts_headline` full pattern; faceted search with FTS + GROUP BY |

## Key patterns

```sql
-- PostgreSQL: stored tsvector + GIN index (recommended)
ALTER TABLE clinical_notes
    ADD COLUMN tsv tsvector
    GENERATED ALWAYS AS (to_tsvector('english', content)) STORED;
CREATE INDEX idx_notes_tsv ON clinical_notes USING GIN (tsv);

-- Basic search:
SELECT note_id FROM clinical_notes
WHERE tsv @@ websearch_to_tsquery('english', 'diabetes renal impairment');

-- Ranked search with highlighted excerpts:
SELECT note_id,
       ts_rank_cd(tsv, query, 32) AS relevance,
       ts_headline('english', content, query,
           'MaxWords=25, MaxFragments=2, StartSel=<b>, StopSel=</b>') AS excerpt
FROM   clinical_notes,
       websearch_to_tsquery('english', 'hypertension blood pressure') query
WHERE  tsv @@ query
ORDER  BY relevance DESC LIMIT 10;

-- Multi-field weighted search:
SELECT article_id,
       ts_rank(
           setweight(to_tsvector('english', title),    'A') ||
           setweight(to_tsvector('english', abstract), 'B'),
           to_tsquery('english', 'diabetes')
       ) AS rank
FROM   research_articles
ORDER  BY rank DESC;
```

## SQLite FTS5 ↔ PostgreSQL mapping

| SQLite FTS5 | PostgreSQL equivalent |
|---|---|
| `CREATE VIRTUAL TABLE t USING fts5(col)` | `CREATE INDEX USING GIN (to_tsvector('english', col))` |
| `WHERE t MATCH 'keyword'` | `WHERE tsv @@ to_tsquery('english', 'keyword')` |
| `WHERE t MATCH '"blood pressure"'` | `WHERE tsv @@ phraseto_tsquery('english', 'blood pressure')` |
| `WHERE t MATCH 'diab*'` | `WHERE tsv @@ to_tsquery('english', 'diab:*')` |
| `ORDER BY rank` (BM25) | `ORDER BY ts_rank_cd(tsv, query) DESC` |
| `snippet(t, col, '[',']','...',15)` | `ts_headline('english', col, query, 'MaxWords=15, StartSel=[, StopSel=]')` |

## Dependencies

```
python >= 3.8
jupyter
sqlite3   (stdlib with FTS5 — all notebooks fully runnable without PostgreSQL)
# PostgreSQL patterns: PostgreSQL 12+ (for GENERATED ALWAYS AS)
# PostgreSQL 9.6+ for <-> and <N> proximity operators
```

---
*sql_methods_library - Samantha McGarrigle*
