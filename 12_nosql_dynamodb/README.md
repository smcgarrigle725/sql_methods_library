# 12 NoSQL: DynamoDB

A practical reference for Amazon DynamoDB — fully managed key-value and document database. All notebooks run live using `boto3` against **DynamoDB Local** (via `moto` for in-process simulation), with no AWS account required. Every pattern applies directly to production DynamoDB.

## Dataset (ecological theme)

An ecological monitoring system for Atlantic Canada, modelling the access-pattern-first design philosophy DynamoDB requires:

- **Monitoring sites** — 6 sites (S01–S06) across NB and NS with province, type, elevation, coordinates
- **Species observations** — 10 observations across sites: Atlantic Salmon, Blanding's Turtle, Piping Plover, Little Brown Bat
- **Single-table design** — all entities in one table using overloaded PK/SK patterns (`SITE#S01`, `OBS#2023-04-15#001`)
- **GSI** — `ProvinceSiteIndex` (pk=province, sk=site_id); `SpeciesIndex` (pk=species, sk=obs_date)

## Notebooks

| Notebook | Topics covered |
|---|---|
| `dynamodb_concepts` | DynamoDB vs MongoDB vs PostgreSQL (3-way comparison table); partition key + sort key design; composite key access patterns (list all observations for a site, get single observation, get by date range); capacity modes (On-Demand vs Provisioned); DynamoDB data types (S, N, B, SS, NS, L, M, NULL, BOOL); `Decimal` requirement for numeric attributes |
| `querying_and_scanning` | Query golden rule (always Query, never Scan in production); `KeyConditionExpression` with `Key('pk').eq() & Key('sk').begins_with()`; `FilterExpression` vs key conditions (cost difference); `ScanIndexForward=False` for reverse sort; pagination with `LastEvaluatedKey` loop; `Scan` with parallel segments; `ProjectionExpression` to limit returned attributes; `Select='COUNT'` |
| `crud_operations` | `PutItem` (full replace); `ConditionExpression` for conditional writes (`attribute_not_exists(pk)`); `GetItem` with `ConsistentRead=True`; `UpdateItem` with `UpdateExpression` (`SET`, `REMOVE`, `ADD`, `DELETE`); `DeleteItem` with condition; `BatchWriteItem` (up to 25); `TransactWriteItems` for multi-item atomicity; `TransactGetItems` |
| `indexes_gsi_lsi` | GSI vs LSI comparison (creation time, consistency, capacity, key constraints); `ProjectionType` (KEYS_ONLY, INCLUDE, ALL); creating GSI after table creation; querying a GSI with `IndexName`; LSI (same PK, alternate SK) use cases; sparse indexes (only items with the indexed attribute appear); GSI overloading pattern; index write amplification cost |
| `dynamodb_patterns` | Access-pattern-first design process; single-table design with entity type overloading; adjacency list pattern for relationships; overloaded GSI for multiple query patterns; write sharding for hot partition keys; time-series partition key strategies; DynamoDB Streams for event-driven pipelines; TTL for automatic item expiry; DynamoDB vs RDS decision framework |

## Key patterns

```python
import boto3
from boto3.dynamodb.conditions import Key, Attr
from decimal import Decimal

# Query all observations for site S01:
response = table.query(
    KeyConditionExpression=Key('pk').eq('SITE#S01') & Key('sk').begins_with('OBS#')
)

# Conditional PutItem (only if not exists):
table.put_item(
    Item={'pk': 'SITE#S01', 'sk': 'META', 'name': 'Petitcodiac River'},
    ConditionExpression='attribute_not_exists(pk)'
)

# UpdateItem with atomic counter:
table.update_item(
    Key={'pk': 'SITE#S01', 'sk': 'STATS'},
    UpdateExpression='ADD observation_count :inc',
    ExpressionAttributeValues={':inc': Decimal('1')}
)

# Query GSI for all Atlantic Salmon observations:
response = table.query(
    IndexName='SpeciesIndex',
    KeyConditionExpression=Key('species').eq('Atlantic Salmon')
)
```

## Dependencies

```
python >= 3.8
jupyter
boto3          (pip install boto3)
moto[dynamodb] (pip install "moto[dynamodb]")  -- in-process DynamoDB simulation
# Live DynamoDB: AWS credentials via environment or ~/.aws/credentials
```

---
*sql_methods_library - Samantha McGarrigle*
