---
title: "Python Geospatial Stack"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [coordinate-reference-systems, vector-vs-raster-data-models]
tags: [library, geopandas, rasterio, shapely, pyproj, fiona, scipy, scikit-learn, plotly, folium, deck.gl]
---

# Python Geospatial Stack

A comprehensive comparison of 18+ Python libraries for geospatial analysis, evaluated by their primary role, key strengths, limitations, and environmental use cases.

## Library Comparison Matrix

| Library | Primary Role | Key Strengths | Limitations | Environmental Use Case |
|---------|-------------|---------------|-------------|----------------------|
| **geopandas** | Vector data manipulation | Pandas-style operations; spatial joins, overlays | Memory-intensive for large datasets; limited raster support | Monitoring well analysis, watershed networks, compliance mapping |
| **rasterio** | Raster I/O & analysis | Handles virtually all GIS raster formats; windowed access; large file support | Steeper learning curve; Python bindings can be slow | DEM analysis, satellite image classification, soil mapping |
| **shapely** | Geometry operations | Precise geometric computations; buffer, intersect, union | No I/O; pure geometry library | Custom spatial analysis, centroid calculations, setback zones |
| **pyproj** | CRS transformations | Industry-standard CRS handling; reprojection engine | Low-level API; not for data manipulation | Coordinate transformations, reprojection pipelines |
| **fiona** | Vector I/O | Most format support; streaming reads/writes | No spatial operations; older API | Reading ESRI File Geodatabases, zipped shapefiles |
| **pandas** | Tabular data | DataFrame operations, merging | Not spatial; CRS handling is manual | Environmental monitoring data (water/air quality tables) |
| **numpy** | Numerical arrays | Fast array operations; memory efficient | No geospatial awareness | Raster algebra, large DEM processing, interpolation arrays |
| **scipy** | Scientific computing | Interpolation, optimization, signal processing | No spatial defaults | Kriging interpolation, terrain analysis, model fitting |
| **scikit-learn** | Machine learning | Classical ML algorithms | No spatial defaults | Predictive modeling with spatial features |
| **matplotlib** | 2D static plotting | Fine-grained control; publication quality | Not interactive; limited spatial features | Maps with exact control, simple overlays |
| **plotly** | Interactive 3D/2D plots | Zoomable, hover info, exportable | Larger file sizes; slower rendering | Explorer-grade data viz dashboards |
| **folium** | Leaflet.js wrappers | Quick interactive maps; lightweight | Limited styling; newer alternatives available | Rapid prototyping, client-facing maps |
| **contextily** | Tile overlays in matplotlib | Seamless basemap integration with matplotlib | Requires internet for tiles | Quick maps without GIS software |
| **deck.gl** | WebGL-powered big data viz | Handles millions of features; GPU-accelerated | Complex setup; JavaScript dependency | Large-scale contamination plume visualization |
| **geovista** | Terrestrial globe visualization | Native to geographic data; clean API | Newer; smaller ecosystem | 3D terrain visualization, elevation mapping |
| **ipyleaflet** | Jupyter-friendly Leaflet | Works in Jupyter notebooks | Limited advanced styling | Interactive exploration in notebooks |
| **obspy** | Earthquake/Seismic | Standard for seismic data | Niche to geophysics | Ground motion, seismic hazard mapping |

## Installation

```bash
# Core stack
pip install geopandas rasterio shapely pyproj pandas numpy scipy

# Visualization
pip install matplotlib plotly folium contextily

# Cloud / large data
pip install rioxarray zarr xarray

# System dependencies (Ubuntu/Debian):
apt-get install -y libgdal-dev libproj-dev

# GDAL_DATA on Windows:
import os
os.environ["GDAL_DATA"] = r"C:\ProgramData\GDAL\gdal_data"
```

## Stack Progression

| Level | Libraries | Focus |
|-------|-----------|-------|
| **Foundation** | numpy, pandas, pyproj, shapely | Data structures, CRS, geometry ops |
| **Core geospatial** | geopandas, rasterio, fiona | Vector/raster I/O and manipulation |
| **Visualization** | matplotlib, plotly, folium, contextily | Static and interactive maps |
| **Advanced** | scipy, scikit-learn, deck.gl | Analysis, ML, big data viz |
| **Cloud/Scale** | xarray, zarr, rioxarray | Large datasets, cloud-optimized workflows |

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Coordinate Reference Systems](coordinate-reference-systems.md)
- [Vector vs. Raster Data Models](vector-vs-raster-data-models.md)