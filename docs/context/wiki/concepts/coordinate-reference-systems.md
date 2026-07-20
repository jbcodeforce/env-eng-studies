---
title: "Coordinate Reference Systems"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [vector-vs-raster-data-models]
tags: [crs, epsg, reprojection, pyproj, coordinate-transforms]
---

# Coordinate Reference Systems

Coordinate Reference Systems (CRS) are the foundation of all geospatial analysis in environmental engineering. Understanding how Earth is projected onto flat maps is non-negotiable for accurate distance, area, and spatial join operations.

## Geographic vs. Projected CRS

| Type | Examples | Use Case |
|------|----------|----------|
| **Geographic CRS** | WGS84 (EPSG:4326), Web Mercator (EPSG:3857) | GPS data, web maps, global coverage |
| **Projected CRS** | UTM (EPSG:32618), State Plane, NAD83 | Distance/area calculations, local/regional analysis |

**Key rule**: Always set CRS explicitly. Never rely on implicit assumptions.

## EPSG Codes

Standard identifiers for reproducible CRS definitions:
- `EPSG:4326` — WGS84 (global geographic)
- `EPSG:3857` — Web Mercator (web mapping)
- `EPSG:32618` — NAD83 / UTM Zone 18N (Washington D.C. area)
- `EPSG:4269` — NAD83 (general)

## Reprojection with pyproj

```python
import pyproj

# Define source and target CRS
gcrs = pyproj.CRS.from_epsg(4326)  # WGS84
utm_crs = pyproj.CRS.from_epsg(32618)  # NAD83 / UTM Zone 18N

# Transform coordinates
transform = pyproj.Transformer.from_crs(gcrs, utm_crs, always_xy=True)
lon, lat = -77.0369, 38.9072  # Washington D.C.
x, y = transform.transform(lon, lat)
print(f"UTM: x={x:.1f} m, y={y:.1f} m")
```

## Reprojection in GeoPandas

```python
import geopandas as gpd

gdf = gpd.read_file("watershed.shp")
gdf_utm = gdf.to_crs(epsg=32618)
gdf_utm["area_m2"] = gdf_utm.geometry.area
```

The `to_crs()` method creates a new GeoDataFrame with transformed geometries without modifying the original.

## Key Libraries

- **pyproj** — CRS transformations, projections, datum definitions (industry standard)
- **proj** — underlying engine for pyproj
- **geopandas.GeoSeries.transform()** — automatic reprojection during spatial operations

## Environmental Engineering Context

CRS errors are a common source of mistakes:
- Buffering in degrees instead of meters produces meaningless distances
- Area calculations in WGS84 return steradians, not square meters
- Spatial joins fail when layers have mismatched CRS
- Web Mercator distorts area at high latitudes

**Always**: Check CRS with `gdf.crs` before analysis. Reproject to a suitable projected CRS before computing distances, areas, or buffers.

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Vector vs. Raster Data Models](vector-vs-raster-data-models.md)