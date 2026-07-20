---
title: "Watershed Analysis"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [vector-vs-raster-data-models, dem-processing-terrain-analysis]
tags: [watershed, hydrology, geopandas, dem, flow-direction, huc]
---

# Watershed Analysis

Watershed analysis is a cornerstone of environmental engineering, combining DEM processing, hydrological modeling, and spatial analysis to understand water flow patterns, delineate catchment areas, and assess pollution sources.

## Workflow Overview

```
1. DEM Acquisition (NED, 30m)
2. DEM Pre-processing (fill sinks, smooth)
3. Flow direction & accumulation
4. Watershed delineation (pour point → watershed)
5. Sub-watershed division (Census tracts, land use)
6. Land use classification (NDVI, Sentinel-2)
7. Soil data integration (SSURGO, SoilGrids)
8. Monitoring well network characterization
9. Pollution load estimation
10. BMP (Best Management Practice) analysis
```

## Key Components

### 1. DEM Processing

Start with a high-quality Digital Elevation Model. Common sources:
- **National Elevation Dataset (NED)** — USGS, 1m to 30m
- **NASADEM** — Global 30m
- **Copernicus DEM** — Global 30m

```python
from scipy.ndimage import binary_fill_holes

# Fill sinks (essential for hydrological modeling)
filled = binary_fill_holes(dem > nodata).astype(int)

# Calculate slope
dz = dem[1:, :] - dem[:-1, :]
dx = np.linspace(0, src.width, num=src.width)
slope = np.degrees(np.arctan(np.abs(dz / dx)))

# Extract DEM within study area
from rasterio.features import geometry_mask
mask = geometry_mask(study_area.geometry.values, transform=src.transform, invert=True)
dem_study = dem.astype(float)
dem_study[mask] = np.nan
```

### 2. Flow Direction & Accumulation

The D8 algorithm determines flow direction based on steepest descent:

```python
# Flow direction (D8 algorithm)
from heapq import heappush, heappop

flow_dir = compute_d8(dem)  # Simplified; use DEMetrix or WhiteboxTools
flow_acc = flow_accumulation(flow_dir, dem)  # D8 accumulation
```

For production, use specialized tools:
- **DEMetrix** — Python-based watershed delineation
- **WhiteboxTools** — Python/C++ hydrological tools
- **r.terrain** in GRASS GIS

### 3. Watershed Delineation

```python
from scipy.ndimage import label, minimum_filter
from heapq import heappush, heappop

# Fill sinks
filled = binary_fill_holes(dem > nodata).astype(int)

# Simplified flow direction
# (Production: use rasterio, DEMetrix, or WhiteboxTools)

# Delineate from pour point
watershed = delineate_watershed(flow_direction, flow_accumulation, pour_point)
```

### 4. Spatial Analysis of Watershed Features

```python
import geopandas as gpd

# Load watershed network
watersheds = gpd.read_file("watersheds.gpkg")
monitoring_wells = gpd.read_file("monitoring_wells.gpkg")

# Find wells within each watershed
analysis = monitoring_wells.sjoin(watersheds, how="left", predicate="within")
analysis["watershed_name"] = analysis["geometry_right"].name

# Calculate distance to watershed centroid
analysis["distance_to_centroid_km"] = analysis.apply(
    lambda row: row["geometry_right"].centroid.distance(row["geometry"]) / 1000,
    axis=1
)

# Identify wells in multiple watersheds
from collections import Counter
multi_well = analysis[analysis["watershed_name"].duplicated()].groupby("watershed_name")
```

### 5. Elevation Statistics

```python
demStats = {
    "elevation_range": lambda x: np.nanmax(x) - np.nanmin(x),
    "mean_elevation": lambda x: np.nanmean(x),
    "max_slope": lambda x: np.nanmax(terrain_slope(x)),
    "flood_risk_score": lambda x: np.where(x < flood_threshold, 1, 0).mean(),
}
```

## EPA Data Sources

| Source | URL | Content |
|--------|-----|---------|
| **EPA ArcGIS Online** | `gis.epa.gov` | Watersheds, hydrology, land use |
| **HUC (Hydrologic Unit Codes)** | `watershed.usgs.gov` | Stream gauges, watershed boundaries |
| **WRRP** | EPA | Groundwater resources, water use |
| **StreamStats** | USGS | Flood frequency, yield analysis |

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Vector vs. Raster Data Models](vector-vs-raster-data-models.md)
- [DEM Processing & Terrain Analysis](dem-processing-terrain-analysis.md)