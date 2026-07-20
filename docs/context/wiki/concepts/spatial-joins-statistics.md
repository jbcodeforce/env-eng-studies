---
title: "Spatial Joins & Statistics"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [vector-vs-raster-data-models, coordinate-reference-systems]
tags: [spatial-join, sjoin, geopandas, spatial-attributes, monitoring-data]
---

# Spatial Joins & Statistics

Spatial joins attach attributes from one layer to another based on spatial relationships (within, intersects, contains). Combined with tabular aggregation, they power environmental monitoring analysis, compliance checks, and pollutant load estimation.

## Primary Methods

| Method | Predicate | Use Case |
|--------|-----------|----------|
| **sjoin** | within | Monitoring wells inside watersheds |
| **sjoin** | intersects | Setback violations, buffer intersections |
| **sjoin** | contains | Watershed boundaries containing facilities |
| **overlay** | intersect | Multi-layer compliance screening |
| **distance** | nearest | Proximity analysis |

## Point-in-Polygon

```python
import geopandas as gpd

well = gpd.points_from_xy([100, 200], [300, 400])[0]
polygon = gpd.read_file("watershed.gpkg")
inside = polygon.contains(well).iloc[0]
```

## Spatial Join: Monitoring Data

```python
import geopandas as gpd

monitoring = gpd.read_file("monitoring_data.gpkg")
facilities = gpd.read_file("facilities.gpkg")

# Attach facility info to monitoring data
joined = monitoring.sjoin(facilities, how="left", predicate="within")

# Aggregate by facility
facility_stats = (
    joined.groupby(["facility_id", "facility_name"])
    .agg(
        sample_count=("quality_parameter", "count"),
        mean_value=("quality_parameter", "mean"),
        max_value=("quality_parameter", "max"),
        exceedance_count=(
            "quality_parameter",
            lambda x: (x > standard).sum()
        ),
    )
    .reset_index()
)
```

## Buffer & Setback Analysis

```python
from shapely.geometry import shape

# Create setback buffer (500m rounded corners)
facility = gpd.read_file("industrial_sites.gpkg")
setback = facility.buffer(500, cap_style=2)

# Identify residential parcels that violate setback
residential = gpd.read_file("residential_parcels.gpkg")
violations = residential.sjoin(setback, how="left", predicate="intersects")

violations["violation_type"] = "Setback violation (within 500m)"
```

## Intersection for CEQA Screening

```python
import geopandas as gpd

# CEQA factors
factors = {
    "floodplain": gpd.read_file("floodway_100yr.gpkg"),
    "wetlands": gpd.read_file("wetlands_national.gpkg"),
    "habitat": gpd.read_file("critical_habitat.gpkg"),
    "soil_erosion": gpd.read_file("erosion_risk.gpkg"),
}

project = gpd.read_file("proposed_project.shp")

# Screen each factor
issues = {}
for name, layer in factors.items():
    impact = gpd.overlay(project, layer, how="intersection")
    issues[name] = len(impact)

report = pd.DataFrame({
    "Factor": list(issues.keys()),
    "Potential Impact (sites affected)": list(issues.values()),
    "Recommendation": [
        "Further Study" if v > 0 else "No Significant Impact"
        for v in issues.values()
    ]
}).sort_values(
    "Potential Impact (sites affected)", ascending=False
)
```

## Environmental Applications

| Application | Spatial Operation | Purpose |
|-------------|-------------------|---------|
| **Monitoring compliance** | sjoin + groupby | Aggregate pollutant stats by facility |
| **Setback enforcement** | buffer + intersects | Identify parcels violating setback |
| **CEQA/NEPA screening** | overlay + intersect | Flag environmental constraints |
| **Multi-watershed tracing** | sjoin | Wells serving multiple watersheds |
| **Receptor mapping** | intersects | Population near pollution sources |

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Vector vs. Raster Data Models](vector-vs-raster-data-models.md)
- [Coordinate Reference Systems](coordinate-reference-systems.md)