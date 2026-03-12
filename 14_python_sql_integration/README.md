# 14 Python–SQL Integration

A practical, fully runnable reference for connecting Python to SQL databases. All notebooks use in-memory SQLite via `sqlite3` and SQLAlchemy 2.0, with PostgreSQL equivalents and production patterns noted throughout.

## Dataset (mixed theme)

- **departments** — dept_id, dept_name, location (Toronto / Montreal / Vancouver)
- **employees** — emp_id, full_name, dept_id, salary, hire_date, is_active, version
- **projects** — project_id, project_name, dept_id, budget, start_date, status
- **assignments** — emp_id, project_id, role, hours_week (bridge table)

## Notebooks

| Notebook | Topics covered |
|---|---|
| `sqlite3_basics` | DB-API 2.0 overview; `connect()` and `:memory:`; `row_factory`; positional `?` and named `:name` parameters; `fetchone()`, `fetchall()`, `fetchmany()`, cursor iteration; `executemany()`; `lastrowid`, `rowcount`; `with conn:` context manager; `PRAGMA` settings (foreign_keys, journal_mode, synchronous) |
| `sqlalchemy_core` | `create_engine()` with pool and echo options; `engine.connect()` vs `engine.begin()`; `text()` with named params; `scalar()`, `scalars()`, `mappings()`; `MetaData.reflect()`; `select()`, `insert()`, `update()`, `delete()` expression builders; `func.*` aggregates; `.returning()` portability |
| `sqlalchemy_orm` | `DeclarativeBase`, `Mapped`, `mapped_column()`; `relationship()` with `back_populates`; `Session`; `scalars()` / `execute()` query patterns; `joinedload()` / `selectinload()` for eager loading; `session.add()`, `session.flush()`, `session.delete()`; bulk Core update via `sa_update()`; `session.expire_all()` |
| `pandas_sql` | `pd.read_sql()` with Engine; `text()` parameterised queries in `read_sql`; `index_col`, `parse_dates`; pandas transformation pipeline (cut, rank, dt accessors); `df.to_sql()` with `if_exists`, `chunksize`; SQL aggregation vs pandas groupby decision guide |
| `connection_pooling` | Pool type comparison (QueuePool, NullPool, StaticPool, SingletonThreadPool, AsyncAdaptedQueuePool); `pool_size`, `max_overflow`, `pool_timeout`, `pool_recycle`, `pool_pre_ping` parameters with rationale; `@event.listens_for(engine, 'connect')` for per-connection PRAGMAs; `pool.checkedout()` / `pool.size()` monitoring; Lambda/serverless NullPool pattern |
| `query_builders_and_safety` | SQL injection anatomy with live attack demonstration; parameterised query defence (`?`, `:name`, Core expressions); dynamic query building with `and_()`, `or_()`, `col.in_()`; allowlist pattern for dynamic table/column names; `echo=True` log redaction warning; least-privilege credential guidance |

## Dependencies

```
python >= 3.10
jupyter
sqlite3      (stdlib)
sqlalchemy   >= 2.0   pip install sqlalchemy
pandas       >= 2.0   pip install pandas
# For PostgreSQL: pip install psycopg2-binary
```

---
*sql_methods_library - Samantha McGarrigle*
