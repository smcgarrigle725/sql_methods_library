# 17 Redis Basics

A practical reference for Redis as a caching layer, pub/sub bus, and data structure store, using the `redis-py` client and `fakeredis` for fully runnable notebooks with no live server required. Every command works identically against a real Redis instance.

## Dataset (mixed theme)

- **Healthcare:** patient records (hash), HbA1c scores (sorted set), session tokens (strings), lab alert events (pub/sub), rate limiting per patient API endpoint
- **Finance:** account balances (strings + MULTI/EXEC transfers), transaction logs, leaderboards

## Notebooks

| Notebook | Topics covered |
|---|---|
| `redis_data_structures` | String: `SET`/`GET`/`INCR`/`MSET`/`MGET`/`GETSET`/`NX`/`XX` with TTL; Hash: `HSET`/`HGETALL`/`HMGET`/`HINCRBY`/`HDEL`; List: RPUSH/LPOP (FIFO), LPUSH/LPOP (stack), `LRANGE`, `LTRIM` (keep-last-N); Set: `SADD`/`SMEMBERS`/`SINTER`/`SUNION`/`SDIFF`/`SINTERSTORE`; Sorted Set: `ZADD`/`ZRANGE`/`ZREVRANGE`/`ZRANGEBYSCORE`/`ZRANK`/`ZINCRBY` |
| `redis_caching_patterns` | Cache-aside (lazy load) with timing demo (50ms DB vs <1ms cache); TTL management (`SET ex=`, `EXPIRE`, `PERSIST`, `TTL`/`PTTL` return codes: -1 persistent, -2 missing); `EXPIREAT`; TTL strategy table; SET NX stampede lock; probabilistic early expiration (PER) algorithm |
| `redis_pub_sub` | Pub/Sub concepts (fire-and-forget, no persistence); `subscribe`/`psubscribe` (`alerts:*` glob); `pubsub.listen()` message types (`subscribe`, `message`, `pmessage`); threaded subscriber demo; `run_in_thread()` + per-channel handler callbacks; Pub/Sub vs Redis Streams comparison |
| `redis_transactions` | MULTI/EXEC via `r.pipeline(transaction=True)`; no-rollback semantics; WATCH optimistic locking with retry loop; `WatchError` handling; Lua `EVAL`/`EVALSHA`; rate-limiter Lua script (INCR + EXPIRE, atomic); `redis.call()` vs `redis.pcall()` |
| `redis_python` | `redis.Redis` and `redis.from_url()`; `ConnectionPool` with `max_connections`; `decode_responses=True`; `pipeline(transaction=False)` bulk-load timing; mixed pipeline commands; exception hierarchy (`ConnectionError`, `TimeoutError`, `WatchError`, `ResponseError`, `BusyLoadingError`); retry decorator with exponential backoff; graceful degradation to DB on Redis failure |

## Key patterns demonstrated

```python
# Cache-aside
def get_patient(patient_id, ttl=300):
    cached = r.get(f'patient:{patient_id}')
    if cached: return json.loads(cached), 'HIT'
    data = db.query(patient_id)
    r.set(f'patient:{patient_id}', json.dumps(data), ex=ttl)
    return data, 'MISS'

# Atomic transfer (MULTI/EXEC)
pipe = r.pipeline(transaction=True)
pipe.decrby('account:A001:balance', 500)
pipe.incrby('account:A002:balance', 500)
pipe.execute()

# Rate limiter (Lua — atomic INCR + EXPIRE)
sha = r.script_load(rate_limit_lua)
allowed = r.evalsha(sha, 1, key, max_requests, window_seconds)

# Sorted set clinical alert ranking
r.zadd('hba1c_scores', {'P003': 9.1, 'P005': 8.9, 'P001': 7.2})
high_risk = r.zrangebyscore('hba1c_scores', 8.0, '+inf', withscores=True)
```

## Dependencies

```
python >= 3.8
jupyter
redis >= 4.0          (pip install redis)
fakeredis >= 2.0      (pip install fakeredis)
# Live execution: Redis 7+ (docker run -p 6379:6379 redis:7)
```

---
*sql_methods_library - Samantha McGarrigle*
