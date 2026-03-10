# 11 NoSQL — MongoDB

A practical reference for MongoDB and PyMongo. Because MongoDB requires a live database connection unavailable in this offline environment, each notebook uses Python to display and explain realistic PyMongo code, document structures, aggregation pipelines, and annotated output — the same approach used in `10_dbt_fundamentals`. All examples use a healthcare-themed dataset (patients, encounters, lab results).

## Notebooks

| Notebook | Topics covered |
|---|---|
| `mongodb_basics` | SQL-to-MongoDB terminology map; `MongoClient` connection patterns (local, Atlas, auth); `insert_one` / `insert_many`; embedded documents and arrays; `find()` cursor vs `find_one()`; projection syntax (include vs exclude); `count_documents()` |
| `querying_and_filtering` | All comparison operators (`$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`, `$in`, `$nin`); logical operators (`$and`, `$or`, `$not`, `$nor`); field operators (`$exists`, `$type`); array operators (`$all`, `$size`, `$elemMatch`); dot notation for nested fields; `sort()`, `limit()`, `skip()` pagination; `update_one/many` with `$set`, `$push`, `$pull`, `$inc`, `$addToSet`; upsert; `delete_one/many` |
| `aggregation_pipeline` | All 14 pipeline stages; `$match` + `$group` with all accumulators; `$project`, `$addFields`; `$lookup` (join) with before/after `$unwind` document structure; `$unwind` on arrays; `allowDiskUse`; SQL-to-pipeline translation table |
| `indexes_in_mongodb` | Index types (single, compound, multikey, text, TTL, geospatial, hashed, wildcard, partial); `create_index`, `list_indexes`, `drop_index`; TTL expiry; text search with relevance scoring; `explain('executionStats')` COLLSCAN vs IXSCAN; efficiency checklist |
| `schema_design_patterns` | Embedding vs referencing decision framework; 4 patterns (polymorphic, bucket/roll-up, outlier, extended reference) with healthcare and time-series examples; `$jsonSchema` validation; `validationLevel` strict vs moderate |
| `mongodb_vs_sql` | Side-by-side SQL vs PyMongo for 11 common operations; MongoDB vs PostgreSQL decision matrix (14 criteria); PostgreSQL JSONB as a middle path (`->`, `->>`, `@>`, GIN indexes); when each tool is the right choice |

## Dataset (healthcare theme)

- **patients** — patient_id, demographics, embedded conditions array, nested contact subdocument
- **encounters** — encounter_id, patient_id (reference), encounter_date, encounter_type, notes
- **lab_results** — lab_id, patient_id (reference), test name, value, units, collected_at

## Dependencies

```
pip install pymongo   # or: pip install motor  (async)
# MongoDB server: brew install mongodb-community  or  cloud.mongodb.com (free Atlas tier)
```

---
*sql_methods_library - Samantha McGarrigle*
