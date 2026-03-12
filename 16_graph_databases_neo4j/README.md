# 16 Graph Databases and Neo4j

A practical reference for graph databases using Neo4j and Cypher, with all examples running live via networkx. The full Cypher equivalents are shown alongside every query.

## Dataset (mixed theme: clinical research network)

- **Researcher** — id, name, role (PI / Biostatistician / Coordinator / Researcher)
- **Institution** — id, name, country
- **Trial** — id, title, phase
- **Disease** — id, name, category
- **Drug** — id, name, drug_class
- **Relationships** — AFFILIATED_WITH, LEADS, CONTRIBUTES_TO, STUDIES, TESTS, TREATS, COLLABORATES_WITH

## Notebooks

| Notebook | Topics covered |
|---|---|
| `graph_concepts` | Nodes / relationships / properties; graph vs relational model (7-factor comparison); when to choose graph; neo4j-driver and py2neo connection patterns; networkx graph construction; label and relationship type counts |
| `cypher_basics` | Cypher pattern syntax (ASCII-art); all core clauses (MATCH, CREATE, MERGE, WHERE, RETURN, SET, REMOVE, DELETE, DETACH DELETE); CREATE vs MERGE distinction; ON CREATE SET / ON MATCH SET; live MATCH + WHERE queries (PIs, trial teams, phase-filtered drugs) |
| `cypher_querying` | 2-hop traversal (Researcher→Trial→Drug); multi-type relationship pattern `[:LEADS\|CONTRIBUTES_TO]`; variable-length paths `*1..3`; `shortestPath()`; OPTIONAL MATCH (left join equivalent); WITH + aggregation (team size, multi-trial researchers); COLLECT and COLLECT(DISTINCT); drug-per-disease via trial |
| `graph_algorithms` | Degree centrality (in/out); shortest path between all researcher pairs (BFS); PageRank (dampingFactor=0.85, networkx live); betweenness centrality (bridge node detection); Neo4j GDS equivalents (gds.pageRank, gds.betweenness, gds.louvain); pre-computation vs live computation guidance |
| `neo4j_python` | neo4j-driver: connection, parameterised read/write, explicit transaction control, `execute_read`/`execute_write`; py2neo: Node/Relationship objects, OGM (GraphObject, Property, RelatedTo), raw Cypher; driver comparison table; Neo4j property data types; live networkx result simulation |
| `graph_vs_relational` | Same 2-hop query written in both SQL (3-join) and Cypher; live SQL execution (SQLite) vs graph traversal (networkx); 7-factor decision matrix; hybrid architectures (PostgreSQL+Neo4j, JSONB+recursive CTE, Apache AGE, DuckDB+networkx); Apache AGE example |

## Key patterns

```cypher
-- Find 2nd-degree trial connections
MATCH (r1:Researcher {name: 'Aroha Ngata'})
      -[:LEADS|CONTRIBUTES_TO]->(:Trial)
      <-[:LEADS|CONTRIBUTES_TO]-(r2:Researcher)
      -[:LEADS|CONTRIBUTES_TO]->(t:Trial)
WHERE NOT (r1)-[:LEADS|CONTRIBUTES_TO]->(t)
RETURN DISTINCT t.title, r2.name

-- Shortest path
MATCH p = shortestPath((r1:Researcher {id:'R001'})-[*]-(r2:Researcher {id:'R005'}))
RETURN p, length(p) AS hops

-- Safe upsert
MERGE (r:Researcher {id: $id})
ON CREATE SET r.name = $name, r.created_at = datetime()
ON MATCH  SET r.updated_at = datetime()
```

## Dependencies

```
python >= 3.8
jupyter
networkx >= 3.0     # live graph demos
sqlite3             # stdlib (graph_vs_relational SQL demos)

# For Neo4j:
pip install neo4j        # official driver
pip install py2neo       # community driver
# Neo4j server: https://neo4j.com/download/  or  docker pull neo4j
```

---
*sql_methods_library - Samantha McGarrigle*
