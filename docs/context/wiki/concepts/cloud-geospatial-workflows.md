---
title: "Cloud-Native Geospatial Workflows"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [python-geospatial-stack, vector-vs-raster-data-models]
tags: [cloud, earth-engine, zarr, xarray, planetary-computer, real-time-data]
---

# Cloud-Native Geospatial Workflows

Modern geospatial analysis increasingly operates at cloud scale, leveraging Earth Engine, Zarr, xarray, and real-time data streams to process datasets too large for local machines.

## Earth Engine

```python
import ee
import geopandas as gpd

ee.Initialize()

# Process global satellite imagery at cloud scale
planting_season = ee.ImageCollection("COPERNICUS/S2_SR")
annual_crop = planting_season \
    .filterDate('2020-03-01', '2020-06-01') \
    .map(lambda img: ee.Reducer.percentile([50, 75]).compute(img))

# Export analysis to local
annual_crop.toGeoDataFrame(outpath="NDVI_2020.gpkg")
```

## Zarr & xarray for Gridded Data

```python
# Zarr for cloud-optimized, chunked gridded data
import xarray as xr
import zarr

# Read Zarr dataset (cloud-optimized, parallel reads)
era5 = xr.open_zarr("ERA5/temperature/zarr_data.grib")

# Select spatial subset efficiently
temp = era5.isel(
    latitude=slice(35, 45),
    longitude=slice(-80, -70),
    time=slice("2020-01-01", "2020-12-31")
)

# Save as Cloud-Optimized GeoTIFF
temp.to_rasterio("temperature_subset.tif", profile="io")
```

## Planetary Computer

```python
# Microsoft Planetary Computer (data catalog API)
import requests

# Query Earth data catalog
catalog = "https://planetarycomputer.microsoft.com/api/stac/v1/"
collections = requests.get(
    f"{catalog}collections?limit=20",
    params={"q": "MODIS"}
).json()

# Find MODIS collection with temperature data
temperature_modis = next(
    (c for c in collections["collections"] if "MODIS" in c["id"]),
    None
)
```

## Real-Time Data Streams

```python
# AirNow API for real-time air quality
import requests
import pandas as pd

airnow_data = requests.get(
    "https://services.api.airnowapi.org/aq/monitor/v1/observations/current",
    headers={"airnow-key": "YOUR_KEY"}
).json()["data"]["officeData"]

realtime_df = pd.DataFrame({
    "latitude": [od["site"]["Latitude"] for od in airnow_data],
    "longitude": [od["site"]["Longitude"] for od in airnow_data],
    "NO2_index": [
        od["observationParameters"][0]["index"]
        for od in airnow_data
        if len(od["observationParameters"]) > 0
    ],
})
```

## Key Cloud Libraries

| Library | Purpose | Key Features |
|---------|---------|-------------|
| **Earth Engine** | Cloud-scale satellite processing | Jupyter integration, petabyte-scale datasets |
| **Zarr** | Chunked array format | Cloud-optimized, parallel reads |
| **xarray** | Labeled multi-dimensional arrays | Geospatial awareness, coordinate slicing |
| **rioxarray** | rasterio + xarray integration | Read/write GeoTIFF in xarray workflow |

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Python Geospatial Stack](python-geospatial-stack.md)
- [Vector vs. Raster Data Models](vector-vs-raster-data-models.md)