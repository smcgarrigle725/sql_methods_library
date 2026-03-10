# sql_methods_library

**Owner:** Samantha McGarrigle  
**Purpose:** Comprehensive SQL and NoSQL reference library for portfolio and interview preparation. Targets data science and data engineering roles in healthcare, finance, and insurance.

---

## Folder Structure

| # | Folder | Notebooks | Dialect / Tool | Theme |
|---|---|---|---|---|
| 01 | `01_sql_fundamentals` | 6 | SQLite | Healthcare |
| 02 | `02_joins_relationships` | 6 | SQLite | Healthcare |
| 03 | `03_aggregation_grouping` | 5 | SQLite | Finance |
| 04 | `04_window_functions` | 6 | SQLite → Postgres notes | Finance |
| 05 | `05_subqueries_ctes` | 5 | SQLite → Postgres notes | Healthcare |
| 06 | `06_data_cleaning_sql` | 6 | SQLite | Mixed |
| 07 | `07_database_design` | 5 | PostgreSQL | Healthcare |
| 08 | `08_analytical_patterns` | 6 | PostgreSQL | Finance |
| 09 | `09_query_optimization` | 5 | PostgreSQL | Ecological |
| 10 | `10_dbt_fundamentals` | 6 | dbt + PostgreSQL | Mixed |
| 11 | `11_nosql_mongodb` | 6 | PyMongo | Mixed |
| 12 | `12_nosql_dynamodb` | 5 | boto3 + DynamoDB Local | Ecological |
| 13 | `13_transactions_concurrency` | 5 | SQLite → Postgres notes | Finance |
| 14 | `14_python_sql_integration` | 6 | SQLAlchemy + pandas | Mixed |
| 15 | `15_regex_advanced_sql` | 5 | PostgreSQL | Healthcare |
| 16 | `16_graph_databases_neo4j` | 6 | py2neo / Neo4j | Mixed |
| 17 | `17_redis_basics` | 5 | redis-py | Mixed |
| 18 | `18_json_jsonb_sql` | 5 | SQLite json_extract + PostgreSQL JSONB | Healthcare |
| 19 | `19_geospatial_sql` | 6 | PostGIS + geopandas | Ecological |
| 20 | `20_data_warehousing_patterns` | 6 | PostgreSQL | Finance / Insurance |
| 21 | `21_sql_testing_documentation` | 5 | PostgreSQL + dbt | Mixed |
| 22 | `22_full_text_search` | 5 | PostgreSQL tsvector/tsquery | Healthcare |
| 23 | `23_database_security` | 5 | PostgreSQL | Healthcare / Finance |
| **Total** | | **126 notebooks** | | |

---

## Notebook Standards

- **Format:** Jupyter notebooks (`.ipynb`), Python kernel, SQL via `sqlite3` or connection objects
- **Structure:** Overview table/summary → setup cell → topic sections with code → Common Pitfalls section → footer
- **Footer:** `*sql_methods_library - Samantha McGarrigle*`
- **Cell count:** As many as needed for the topic — no artificial cap
- **Code cells:** Runnable, self-contained within each notebook; all data simulated inline
- **Pitfalls:** Minimum 5 pitfalls per notebook, concrete and specific
- **PostgreSQL notes:** Included inline wherever SQLite and PostgreSQL differ meaningfully

## Folder Standards

- Every folder has a `README.md` covering: purpose, notebook list with topics, dataset description, dependencies, run instructions

---

## Notebook Detail by Folder

### 01 sql_fundamentals
1. `select_basics` — SELECT anatomy, aliases, CASE, DISTINCT, WHERE, LIMIT, NULL/COALESCE
2. `filtering_where` — All WHERE operators, AND/OR precedence, NULL propagation, HAVING
3. `data_types_casting` — Type systems, TYPEOF, CAST, integer division, NULLIF
4. `string_functions` — TRIM/UPPER/LOWER, SUBSTR/INSTR/REPLACE, GROUP_CONCAT/STRING_AGG
5. `date_time_functions` — STRFTIME, JULIANDAY, LOS, age, period bucketing, timezones
6. `order_by_limit` — ORDER BY, LIMIT, OFFSET, keyset pagination, NULL sort, custom sort

### 02 joins_relationships
1. `inner_join` — INNER JOIN mechanics, ON clause, join order, aliasing tables
2. `left_right_outer_joins` — LEFT/RIGHT JOIN, NULL in unmatched rows, outer join filtering
3. `full_outer_cross_joins` — FULL OUTER JOIN (SQLite workaround), CROSS JOIN, cartesian products
4. `self_joins` — Self-referencing tables, hierarchy traversal, employee/manager patterns
5. `multi_table_joins` — Chaining 3+ tables, join order, readability
6. `set_operations` — UNION / UNION ALL, INTERSECT, EXCEPT, column alignment rules

### 03 aggregation_grouping
1. `group_by_having` — GROUP BY, HAVING, filtering before/after aggregation
2. `aggregate_functions` — COUNT, SUM, AVG, MIN, MAX, COUNT DISTINCT, NULL in aggregates
3. `conditional_aggregation` — CASE WHEN inside aggregates, pivot patterns, flag counts
4. `rollup_grouping_sets` — ROLLUP, CUBE, GROUPING SETS (PostgreSQL), subtotals
5. `null_in_aggregation` — NULL exclusion rules, COUNT(*) vs COUNT(col), COALESCE in aggs

### 04 window_functions
1. `window_basics` — OVER(), PARTITION BY, ORDER BY in window, frame concept
2. `ranking_functions` — ROW_NUMBER, RANK, DENSE_RANK, NTILE, PERCENT_RANK
3. `lag_lead` — LAG, LEAD, default values, period-over-period change
4. `running_totals_moving_averages` — SUM/AVG OVER, frame clauses (ROWS, RANGE, GROUPS)
5. `first_last_nth_value` — FIRST_VALUE, LAST_VALUE, NTH_VALUE, frame boundary pitfalls
6. `gaps_and_islands` — Identifying consecutive sequences, session detection, gap detection

### 05 subqueries_ctes
1. `scalar_subqueries` — Single-value subqueries in SELECT, WHERE, HAVING
2. `correlated_subqueries` — Row-by-row execution, EXISTS/NOT EXISTS pattern
3. `derived_tables` — Subqueries in FROM, inline views, readability
4. `basic_ctes` — WITH clause, multiple CTEs, CTE vs subquery trade-offs
5. `recursive_ctes` — Hierarchy traversal, graph walking, sequence generation

### 06 data_cleaning_sql
1. `null_handling` — COALESCE, NULLIF, IFNULL, NULL in joins and aggregates
2. `deduplication` — ROW_NUMBER dedup, DISTINCT, GROUP BY dedup, keeping one row
3. `type_conversion_coercion` — CAST patterns, safe conversion, format standardisation
4. `string_cleaning` — TRIM variants, REPLACE chains, regex cleaning (PostgreSQL)
5. `date_standardisation` — Parsing inconsistent formats, normalising to ISO, SQLite vs Postgres
6. `case_when_transformations` — Recoding values, binning, flag columns, multi-condition logic

### 07 database_design
1. `entity_relationship_modelling` — ERD concepts, entities, attributes, relationships, cardinality
2. `normalisation` — 1NF, 2NF, 3NF, BCNF — with healthcare examples
3. `keys_constraints` — PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, NOT NULL, CASCADE
4. `indexes` — B-tree, hash, partial, composite indexes; when to add, when not to
5. `views_materialised_views` — CREATE VIEW, materialised views (PostgreSQL), use cases, limitations

### 08 analytical_patterns
1. `cohort_analysis` — Defining cohorts, cohort retention tables, acquisition cohorts
2. `funnel_conversion` — Funnel steps, conversion rates, drop-off analysis
3. `retention_curves` — Day-N retention, rolling retention, churn identification
4. `sessionisation` — Gap-based session detection using LAG and window functions
5. `pivot_unpivot` — CASE-based pivot, CROSSTAB (PostgreSQL), UNPIVOT patterns
6. `time_series_aggregation` — Date spine, gap filling, moving averages, YoY/MoM comparisons

### 09 query_optimization
1. `explain_analyze` — Reading EXPLAIN / EXPLAIN ANALYZE output in PostgreSQL
2. `index_strategies` — Index types, covering indexes, partial indexes, index-only scans
3. `join_optimisation` — Join order, hash vs nested loop vs merge join, statistics
4. `avoiding_full_scans` — Common patterns that prevent index use, rewrites
5. `query_refactoring` — CTE vs subquery vs temp table, materialising intermediate results

### 10 dbt_fundamentals
1. `dbt_project_structure` — Directory layout, dbt_project.yml, profiles, project setup
2. `models_materialisations` — SELECT-based models, table/view/incremental/ephemeral
3. `sources_staging` — Sources YAML, staging conventions, source freshness
4. `dbt_tests` — Schema tests (not_null, unique, accepted_values, relationships), custom tests
5. `documentation` — doc blocks, descriptions, dbt docs generate/serve
6. `incremental_models` — is_incremental(), unique_key, strategies, full refresh

### 11 nosql_mongodb
1. `document_model` — Collections, documents, BSON types, schema flexibility vs SQL
2. `crud_operations` — insertOne/Many, findOne/find, updateOne/Many, deleteOne/Many
3. `querying_filtering` — Query operators ($eq, $gt, $in, $and, $or, $regex, $exists)
4. `aggregation_pipeline` — $match, $group, $project, $sort, $lookup (join), $unwind
5. `indexes_performance` — createIndex, compound indexes, text indexes, explain()
6. `schema_design_patterns` — Embedding vs referencing, one-to-many patterns, anti-patterns

### 12 nosql_dynamodb
1. `dynamodb_data_model` — Tables, items, attributes, partition key, sort key, DynamoDB vs SQL
2. `crud_with_boto3` — put_item, get_item, update_item, delete_item, conditional writes
3. `query_vs_scan` — Query (efficient), Scan (expensive), filter expressions, projections
4. `single_table_design` — Access patterns first, entity overloading, GSI patterns
5. `gsi_lsi` — Global and local secondary indexes, sparse indexes, overloading

### 13 transactions_concurrency
1. `acid_properties` — Atomicity, Consistency, Isolation, Durability — with examples
2. `begin_commit_rollback` — Transaction syntax, savepoints, nested transactions
3. `isolation_levels` — READ UNCOMMITTED → SERIALIZABLE, phenomena (dirty/phantom reads)
4. `locking_deadlocks` — Row locks, table locks, deadlock detection, avoiding deadlocks
5. `optimistic_pessimistic_concurrency` — Optimistic (version/timestamp), pessimistic (SELECT FOR UPDATE)

### 14 python_sql_integration
1. `sqlite3_module` — Connection, cursor, execute, fetchall, context managers, parameterised queries
2. `sqlalchemy_core` — Engine, Connection, text(), Table reflection, parameterised statements
3. `sqlalchemy_orm` — Declarative models, Session, CRUD, relationships, lazy vs eager loading
4. `pandas_sql` — read_sql, to_sql, chunked reads, dtype mapping, connection handling
5. `connection_pooling` — Pool types, pool_size, overflow, timeout, best practices
6. `sql_injection_prevention` — Parameterised queries, never format strings, ORM safety

### 15 regex_advanced_sql
1. `regex_basics_postgres` — SIMILAR TO, ~ operator, REGEXP_REPLACE, REGEXP_MATCHES
2. `pattern_extraction` — Extracting codes, numbers, identifiers from messy text
3. `text_normalisation` — Cleaning clinical text, removing special chars, standardising formats
4. `regex_in_sqlite` — REGEXP with Python extension, workarounds, LIKE alternatives
5. `advanced_patterns` — Lookaheads (via application layer), common healthcare regex patterns

### 16 graph_databases_neo4j
1. `graph_model_concepts` — Nodes, relationships, properties, property graph vs RDF, when to use
2. `cypher_basics` — MATCH, WHERE, RETURN, CREATE, MERGE, DELETE, basic patterns
3. `pattern_matching` — Path patterns, variable-length paths, shortestPath
4. `graph_algorithms` — PageRank, betweenness centrality, community detection (GDS library)
5. `neo4j_python` — py2neo / neo4j driver, running Cypher from Python, result parsing
6. `graph_use_cases` — Patient referral networks, drug interactions, fraud rings, supply chains

### 17 redis_basics
1. `redis_data_structures` — Strings, Lists, Sets, Sorted Sets, Hashes, Streams
2. `crud_with_redis_py` — SET/GET/DEL, key expiry (TTL), atomic operations
3. `caching_patterns` — Cache-aside, write-through, cache invalidation, TTL strategy
4. `redis_as_queue` — LPUSH/RPOP, BLPOP, simple task queue pattern
5. `redis_use_cases` — Session storage, rate limiting, leaderboards, pub/sub overview

### 18 json_jsonb_sql
1. `json_in_sqlite` — json_extract, json_each, json_object, json_array
2. `jsonb_in_postgres` — JSONB vs JSON, operators (->, ->>, #>), containment (@>)
3. `querying_nested_json` — Extracting nested fields, array elements, conditional on JSON values
4. `json_aggregation` — json_agg, jsonb_agg, json_build_object, row_to_json
5. `json_schema_patterns` — EHR/FHIR JSON structures, flattening strategies, indexing JSONB

### 19 geospatial_sql
1. `postgis_setup` — Enabling PostGIS, geometry types, SRID/CRS concepts
2. `spatial_data_types` — POINT, LINESTRING, POLYGON, MULTIPOLYGON, WKT/WKB
3. `spatial_queries` — ST_Distance, ST_Within, ST_Intersects, ST_Contains, ST_Buffer
4. `spatial_joins` — Joining on geometry predicates, nearest-neighbour, ST_DWithin
5. `coordinate_systems` — EPSG codes, ST_Transform, degrees vs metres pitfalls
6. `geospatial_analytics` — Clustering, area calculations, spatial aggregation

### 20 data_warehousing_patterns
1. `dimensional_modelling` — Star schema, snowflake schema, fact vs dimension tables, Kimball
2. `slowly_changing_dimensions` — SCD Type 1, 2, 3 — SQL implementation patterns
3. `surrogate_keys` — Why natural keys fail, generating surrogates, sequence vs hash
4. `fact_table_patterns` — Transaction facts, periodic snapshot, accumulating snapshot
5. `etl_sql_patterns` — MERGE/UPSERT, INSERT ... SELECT, truncate-and-reload vs incremental
6. `warehouse_query_patterns` — Querying star schemas efficiently, avoiding fan-out joins

### 21 sql_testing_documentation
1. `data_quality_assertions` — Row count checks, uniqueness, referential integrity, range checks
2. `testing_with_plain_sql` — Writing test queries, assertion patterns, automated checks
3. `dbt_test_deep_dive` — Generic vs singular tests, custom macros, severity levels
4. `sql_documentation_standards` — Column descriptions, table-level docs, lineage comments
5. `data_contracts` — Defining expectations, schema evolution, breaking vs non-breaking changes

### 22 full_text_search
1. `tsvector_tsquery_basics` — to_tsvector, to_tsquery, @@ match operator, text search config
2. `ranking_and_relevance` — ts_rank, ts_rank_cd, highlighting with ts_headline
3. `full_text_indexes` — GIN index for tsvector, partial indexes, index maintenance
4. `advanced_search_patterns` — Phrase search, prefix search, stemming, stop words
5. `healthcare_text_search` — Clinical notes, drug name search, ICD code lookup, FHIR text fields

### 23 database_security
1. `users_roles_privileges` — CREATE ROLE, GRANT, REVOKE, role inheritance, least privilege
2. `row_level_security` — RLS policies, ENABLE ROW LEVEL SECURITY, policy expressions
3. `views_as_security_layers` — Column masking with views, exposing subsets, updateable views
4. `data_masking_anonymisation` — Masking PII, pseudonymisation, PIPEDA/HIPAA awareness
5. `sql_injection_and_audit` — Injection vectors, parameterised queries, pg_audit logging


---
*sql_methods_library - Samantha McGarrigle*
