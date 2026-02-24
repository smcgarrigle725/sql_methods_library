# databases

Documented reference implementations for SQL and NoSQL database design, querying, and data engineering patterns. Grounded in real project experience designing and deploying databases for multi-organization research data on AWS.

---

## Structure

```
databases/
│
├── sql/
│   ├── README.md
│   ├── 01_schema_design/
│   │   ├── README.md
│   │   └── schema_patterns.sql         # normalized design, naming conventions
│   │
│   ├── 02_core_queries/
│   │   ├── README.md
│   │   ├── select_filter_sort.sql
│   │   ├── joins.sql                   # inner, left, right, full, self
│   │   └── aggregations.sql            # GROUP BY, HAVING, COUNT, SUM, AVG
│   │
│   ├── 03_window_functions/
│   │   ├── README.md
│   │   └── window_functions.sql        # ROW_NUMBER, RANK, LAG, LEAD, running totals
│   │
│   ├── 04_advanced_queries/
│   │   ├── README.md
│   │   ├── subqueries_ctes.sql
│   │   └── query_optimization.sql      # indexes, EXPLAIN, common pitfalls
│   │
│   └── 05_etl_patterns/
│       ├── README.md
│       └── etl_patterns.sql            # staging tables, upserts, incremental loads
│
└── nosql/
    ├── README.md
    ├── 01_dynamodb_design/
    │   ├── README.md
    │   └── dynamodb_patterns.py        # table design, partition keys, sort keys
    │
    ├── 02_dynamodb_operations/
    │   ├── README.md
    │   └── crud_operations.py          # put, get, query, scan, delete via boto3
    │
    └── 03_real_world_example/
        ├── README.md
        └── microplastics_schema.md     # case study: multi-org watershed data integration
                                        # 10 partner organizations, Saint John River watershed
                                        # deployed on AWS DynamoDB + Aurora
```

---

## SQL Coverage

| Topic | File | Description |
|---|---|---|
| Schema design | `schema_patterns.sql` | Normalization, keys, constraints, naming |
| Core queries | `select_filter_sort.sql` | The fundamentals |
| Joins | `joins.sql` | All join types with examples |
| Aggregations | `aggregations.sql` | GROUP BY patterns |
| Window functions | `window_functions.sql` | Ranking, lag/lead, running totals |
| CTEs | `subqueries_ctes.sql` | Readable complex queries |
| ETL patterns | `etl_patterns.sql` | Staging, upserts, incremental loads |

## NoSQL Coverage

| Topic | File | Description |
|---|---|---|
| DynamoDB design | `dynamodb_patterns.py` | Table design principles, access patterns |
| CRUD operations | `crud_operations.py` | boto3 implementation |
| Real-world case | `microplastics_schema.md` | Multi-org data integration on AWS |

---

## Real-World Grounding

The NoSQL section is anchored in a real deployment: a multi-table database designed to integrate data from 10 partner organizations for the Saint John River watershed microplastics project (Environment and Climate Change Canada, 2025). The database was built on AWS DynamoDB and Aurora to support a public-facing website with dynamic dashboards and GIS maps.

---

*Part of a broader portfolio. See also:
[ecological_data_science](https://github.com/samantha-mcgarrigle/ecological_data_science) ·
[r_methods_library](https://github.com/samantha-mcgarrigle/r_methods_library) ·
[python_methods_library](https://github.com/samantha-mcgarrigle/python_methods_library)*
