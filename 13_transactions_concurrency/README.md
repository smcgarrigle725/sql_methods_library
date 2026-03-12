# 13 Transactions and Concurrency

A practical reference for database transaction management and concurrency control in financial systems. All notebooks use in-memory SQLite for live runnable demonstrations, with PostgreSQL syntax differences and production patterns noted throughout.

## Dataset (finance theme)

- **accounts** — account_id, owner_name, balance (CHECK >= 0), account_type, version (for optimistic locking), updated_at
- **ledger** — entry_id, account_id, amount, entry_type (debit/credit), description, status
- **trades** — trade_id, account_id, symbol, quantity, price, status, created_at

## Notebooks

| Notebook | Topics covered |
|---|---|
| `acid_properties` | Atomicity (transfer demo with rollback on constraint failure); Consistency (CHECK constraint enforcement); Isolation (transaction timeline diagram, READ COMMITTED vs REPEATABLE READ); Durability (WAL journal mode, fsync guarantees); Python context manager pattern (`with conn:`) |
| `isolation_levels` | Four concurrency anomalies (dirty read, non-repeatable read, phantom read, serialization anomaly); isolation level matrix with PostgreSQL implementation details (MVCC, SSI); SQLite WAL snapshot behaviour; READ COMMITTED vs REPEATABLE READ use cases; SERIALIZABLE retry loop with exponential backoff |
| `locks_and_deadlocks` | PostgreSQL lock type hierarchy (RowShare through ACCESS EXCLUSIVE); `SELECT FOR UPDATE`, `FOR SHARE`, `FOR UPDATE NOWAIT`, `FOR UPDATE SKIP LOCKED`; deadlock timeline diagram; four prevention strategies (consistent lock ordering, short transactions, NOWAIT/SKIP LOCKED, application retry); SQLite write-lock model |
| `optimistic_vs_pessimistic` | Lost update problem; pessimistic pattern (`FOR UPDATE`); optimistic pattern (version column + `WHERE version = ?`); Python implementation with retry logic and rowcount check; CAS variants (version int, timestamp, xmin, hash); head-to-head comparison table |
| `savepoints_and_nested` | Savepoint syntax (SAVEPOINT, ROLLBACK TO, RELEASE); batch payment processor with per-item savepoint rollback (live demo); nested transaction patterns (savepoint stack, SQLAlchemy/Django ORM behaviour); PostgreSQL PL/pgSQL EXCEPTION block; audit log autonomy problem; SQLite savepoint quirks |

## Key patterns demonstrated

- **Transfer function** with full atomicity (debit + credit + ledger entries, all-or-nothing)
- **Optimistic withdrawal** with version check, rowcount validation, and retry with backoff
- **Batch payment processor** where individual failures roll back their savepoint but the batch commits
- **Deadlock prevention** via consistent lock ordering (`ORDER BY account_id`)
- **PostgreSQL SERIALIZABLE retry loop** with `SerializationFailure` exception handling

## Dependencies

```
python >= 3.8
jupyter
sqlite3  (stdlib)
# For PostgreSQL examples: pip install psycopg2-binary
```

---
*sql_methods_library - Samantha McGarrigle*
