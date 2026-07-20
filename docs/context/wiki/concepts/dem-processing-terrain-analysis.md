---
title: "DEM Processing & Terrain Analysis"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [vector-vs-raster-data-models, watershed-analysis]
tags: [dem, elevation, slope, aspect, scipy, rasterio, terrain]
---

# DEM Processing & Terrain Analysis

Digital Elevation Models (DEM) are raster data representing Earth's surface elevation. Terrain analysis derives derived metrics (slope, aspect, hillslope length) essential for hydrology, renewable energy siting, and habitat assessment.

## Key Terrain Metrics

| Metric | Description | Environmental Application |
|--------|-------------|--------------------------|
| **Elevation** | Height above reference datum | Base map for all terrain analysis |
| **Slope** | Rate of elevation change (° or %) | Erosion risk, runoff velocity, solar exposure |
| **Aspect** | Direction of maximum slope (° from N) | Solar radiation modeling, slope stability |
| **Profile curvature** | Curvature along flow direction | Deposition vs. erosion zones |
| **Plan curvature** | Curvature across flow direction | Water convergence/divergence |
| **Flow direction** | Direction of steepest descent | Watershed delineation, flow routing |
| **Flow accumulation** | Number of cells draining through each cell | Stream network identification |

## DEM Reading with rasterio

```python
from rasterio import open as rio_open
import numpy as np

with rio_open("dem.tif") as src:
    dem = src.read(1)          # meters above sea level
    transform = src.transform   # affine transform
    nodata = src.nodata         # no-data value
    bounds = src.bounds         # bounding box
    height, width = src.height, src.width
```

## Slope Calculation

```python
import numpy as np
import math

with rio_open("dem.tif") as src:
    dem = src.read(1)

# Simple finite-difference slope
dz = dem[1:, :] - dem[:-1, :]
dx = np.linspace(0, src.width, num=src.width)
slope = np.degrees(np.arctan(np.abs(dz / dx)))
```

**Note**: For production-grade DEM derivatives, use:
- `rasterio.features.rich_metrics` — GDAL-based terrain analysis
- **WhiteboxTools** (`wbTerrain`) — full suite of terrain metrics
- **DEMetrix** — Python-based GIS for DEM processing

## Aspect Calculation

```python
# Aspect from finite-difference
cos_aspect = dx / (dx**2 + dz**2)**0.5
aspect = np.arctan2(dz, dx)
```

## Sink Filling

Sinks (depressions) in DEMs prevent proper flow direction computation:

```python
from scipy.ndimage import binary_fill_holes

filled = binary_fill_holes(dem > nodata).astype(int) * dem

# More robust: Geomorphonic sinks, Brinkley method
# Available in WhiteboxTools and DEMetrix
```

## Extracting DEM Within Study Area

```python
from rasterio.features import geometry_mask

study_area = gpd.read_file("study_area.shp")
with rio_open("dem.tif") as src:
    mask = geometry_mask(
        study_area.geometry.values,
        transform=src.transform,
        invert=True
    )
    dem_study = src.read(1).astype(float)
    dem_study[mask] = np.nan
```

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Vector vs. Raster Data Models](vector-vs-raster-data-models.md)
- [Watershed Analysis](watershed-analysis.md)