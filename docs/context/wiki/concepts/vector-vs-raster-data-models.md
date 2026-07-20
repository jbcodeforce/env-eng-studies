---
title: "Vector vs. Raster Data Models"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [coordinate-reference-systems]
tags: [vector, raster, geopandas, rasterio, dem, geojson, geotiff]
---

# Vector vs. Raster Data Models

Geospatial data comes in two fundamentally different models: vector and raster. Environmental engineers must understand both and know when to use each.

## Vector Data

Vector data represents geographic features as **points, lines, and polygons** with attributes stored in tables.

| Attribute | Details |
|-----------|---------|
| **Data type** | Points, lines, polygons |
| **Best for** | Boundaries, networks, discrete features |
| **Storage** | Shapefile, GeoJSON, GeoPackage, ESRI File Geodatabase |
| **Analysis** | Network analysis, proximity, buffering, spatial joins |
| **Environmental uses** | Watershed boundaries, utility lines, monitoring wells, setback zones, facility locations |

**Key libraries**: `geopandas`, `shapely`, `fiona`

### Strengths
- Exact locations and boundaries
- Efficient storage for discrete features
- Rich attribute tables
- Precise topological relationships

### Limitations
- Memory-intensive for large datasets
- Limited raster support
- Cannot represent continuous surfaces natively

## Raster Data

Raster data represents geographic space as a **grid of cells (pixels)**, each with a value.

| Attribute | Details |
|-----------|---------|
| **Data type** | Grid cells (pixels) |
| **Best for** | Surfaces, continuous phenomena |
| **Storage** | GeoTIFF, COPC (Cloud-Optimized), Zarr |
| **Analysis** | Overlay, interpolation, statistics, terrain analysis |
| **Environmental uses** | DEM, satellite imagery, soil properties, wind speed, precipitation |

**Key libraries**: `rasterio`, `rioxarray`, `xarray`, `scipy`

### Strengths
- Natural representation of continuous surfaces
- Efficient for large areas with simple analysis
- Mathematical operations (slope, aspect, NDVI)

### Limitations
- Resolution-dependent accuracy
- Memory-intensive for large grids
- Steeper learning curve

## When to Use Each

| Scenario | Data Type |
|----------|-----------|
| Watershed boundaries | Vector |
| Utility line network | Vector |
| Monitoring well locations | Vector |
| Setback zones around facilities | Vector |
| Digital Elevation Model (DEM) | Raster |
| Satellite imagery | Raster |
| Soil property maps | Raster |
| Wind speed fields | Raster |

## Vector Operations

```python
import geopandas as gpd
from shapely.geometry import Point, buffer

# Point in polygon (monitoring well inside watershed)
well = gpd.points_from_xy([100, 200], [300, 400])[0]
polygon = gpd.read_file("watershed.gpkg")
inside = polygon.contains(well).iloc[0]

# Buffer analysis (500m setback zone)
setback = facility.buffer(500, cap_style=2)

# Spatial join
analysis = monitoring.sjoin(watersheds, how="left", predicate="within")
```

## Raster Operations

```python
from rasterio import open as rio_open
import numpy as np
from scipy import ndimage
import math

# Read DEM
with rio_open("dem.tif") as src:
    dem = src.read(1)
    transform = src.transform

# Calculate slope
dz = dem[1:, :] - dem[:-1, :]
dx = np.linspace(0, src.width, num=src.width)
slope = np.degrees(np.arctan(np.abs(dz / dx)))

# Calculate aspect
cos_aspect = dx / (dx**2 + dz**2)**0.5
aspect = np.arctan2(dz, dx)

# Extract within study area
from rasterio.features import geometry_mask
mask = geometry_mask(study_area.geometry.values, transform=src.transform, invert=True)
dem_study = dem.astype(float)
dem_study[mask] = np.nan
```

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Coordinate Reference Systems](coordinate-reference-systems.md)