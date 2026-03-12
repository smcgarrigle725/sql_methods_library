# 19 Geospatial SQL

A practical reference for working with spatial data in SQL databases and Python. All notebooks run live using GeoPandas + Shapely + PyProj — no PostGIS server required. Every PostGIS SQL function and operator is shown throughout, ready to run against a live PostgreSQL + PostGIS instance.

## Dataset (ecological theme)

Atlantic Canada ecological monitoring dataset:

- **Monitoring sites** — 8 sites (S01–S08) across NB and NS: Petitcodiac River, Miramichi Lake, Fundy Shore, Tantramar Marsh, Kouchibouguac, Restigouche River, Saint John River, Annapolis Valley
- **Protected areas** — 3 national parks (Fundy, Kouchibouguac, Kejimkujik) as simplified polygons
- **Watersheds** — 3 watershed polygons: Saint John (55,200 km²), Miramichi (13,800 km²), Petitcodiac (3,100 km²)
- **Species observations** — 10 point observations: Atlantic Salmon, Blanding's Turtle, Piping Plover, Little Brown Bat

**CRS strategy:** data stored in EPSG:4326 (WGS84); projected to EPSG:32620 (UTM Zone 20N) for all distance and area calculations.

## Notebooks

| Notebook | Topics covered |
|---|---|
| `geospatial_concepts` | Geometry type hierarchy (Point → MultiPolygon → GeometryCollection); Shapely live demos (Point, LineString, Polygon, MultiPolygon); CRS / EPSG table (4326, 3857, 32620, 32619, 4269, 3979); `pyproj.Transformer` for CRS conversion; Moncton NB WGS84 → UTM Zone 20N demo; critical rule: project before distance/area; WKT vs WKB vs GeoJSON vs Shapefile vs GeoPackage format comparison |
| `postgis_basics` | PostGIS setup (`CREATE EXTENSION postgis`); geometry column DDL (`geometry(Point, 4326)`); GIST index creation; ST_Distance (degrees vs metres); ST_Area and ST_Centroid; 10 spatial predicate functions (ST_Intersects, ST_Within, ST_Contains, ST_Covers, ST_Crosses, ST_Overlaps, ST_Touches, ST_Disjoint, ST_DWithin, ST_Equals) with descriptions; live demo comparing site-to-site distances in UTM metres |
| `spatial_queries` | Spatial JOIN with `gpd.sjoin()` (predicate='within', 'intersects', 'left'); sites → watershed join; species observations → watershed aggregation; nearest neighbour per observation (brute-force `distances.idxmin()`); PostGIS KNN with `CROSS JOIN LATERAL ... ORDER BY geom <-> o.geom LIMIT 1`; `<->` vs `<#>` operators; 10km buffer + sjoin for observation catchment; buffer area calculation; PostGIS `ST_Buffer` options |
| `spatial_indexes` | GIST R-tree mechanics (bbox filter → exact recheck); GIST vs BRIN vs SP-GIST; index DDL (basic, partial, 3D, geography cast, BRIN); EXPLAIN ANALYZE plan node guide (Index Scan vs Seq Scan, `&&` bbox condition, Bitmap Heap Scan); sargability table (ST_Distance → ST_DWithin, ST_Buffer → ST_DWithin, ST_Transform penalty, NOT ST_Intersects); `ST_Simplify` vs `ST_SimplifyPreserveTopology`; CLUSTER, materialized views, partitioning for spatial performance |
| `geopandas_basics` | GeoDataFrame structure (type, shape, CRS, columns); `to_crs()` reprojection; geometry accessors (`.geom_type`, `.total_bounds`, `.x`, `.y`); element-wise `.distance()`; `.buffer()` and `.union_all()`; `gpd.overlay()` (intersection, union, difference, symmetric_difference, identity); watershed overlap percentages; I/O methods: `read_file`, `to_file`, `read_postgis`, `to_postgis`, `to_wkt`, `from_wkt`; GeoJSON round-trip demo |
| `ecological_analysis` | Protected area × watershed overlap matrix (area km² and % of PA); species range via convex hull (`union_all().convex_hull`); range area and centroid per species; inside-vs-outside protected area counts per species; watershed site summary (n_sites, avg/max elevation); observation density per 1000 km²; PostGIS upstream accumulation recursive CTE pattern |

## Key patterns

```python
import geopandas as gpd
from shapely.geometry import Point
import pyproj

# Load data
sites = gpd.GeoDataFrame(data, crs='EPSG:4326')

# Project to UTM for accurate distances/areas
sites_utm = sites.to_crs('EPSG:32620')
dist_m = sites_utm.geometry.iloc[0].distance(sites_utm.geometry.iloc[1])

# Spatial join: sites within watersheds
result = gpd.sjoin(sites, watersheds, how='left', predicate='within')

# Buffer analysis: 10km catchment
sites_utm['geometry'] = sites_utm.geometry.buffer(10_000)

# Overlap analysis
overlap = pa_polygon.intersection(watershed_polygon)
pct = 100 * overlap.area / pa_polygon.area
```

```sql
-- PostGIS: accurate distance in metres
SELECT ST_Distance(
    ST_Transform(a.geom, 32620),
    ST_Transform(b.geom, 32620)
) / 1000 AS dist_km
FROM monitoring_sites a, monitoring_sites b
WHERE a.site_id = 'S01' AND b.site_id = 'S06';

-- Nearest neighbour using spatial index:
SELECT s.site_id, s.site_name
FROM   monitoring_sites s
ORDER  BY s.geom <-> ST_SetSRID(ST_MakePoint(-66.06, 45.95), 4326)
LIMIT  1;

-- Buffer and intersect:
SELECT s.site_id, COUNT(o.obs_id) AS nearby_obs
FROM   monitoring_sites s
JOIN   species_observations o
    ON ST_DWithin(
        ST_Transform(s.geom, 32620),
        ST_Transform(o.geom, 32620),
        10000      -- 10km
    )
GROUP  BY s.site_id;
```

## Dependencies

```
python >= 3.8
jupyter
geopandas >= 0.14   (pip install geopandas)
shapely >= 2.0      (pip install shapely)
pyproj >= 3.0       (pip install pyproj)
# PostGIS: PostgreSQL 14+ with PostGIS 3.3+
# pip install psycopg2-binary sqlalchemy for read_postgis / to_postgis
```

---
*sql_methods_library - Samantha McGarrigle*
