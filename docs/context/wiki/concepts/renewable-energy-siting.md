---
title: "Renewable Energy Siting"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [dem-processing-terrain-analysis, raster-analysis-environmental-engineering]
tags: [solar, wind, siting, renewable-energy, solar-irradiance, terrain]
---

# Renewable Energy Siting

Site selection for solar and wind energy combines terrain analysis, resource assessment, environmental constraints, and multi-criteria scoring to identify optimal locations.

## Multi-Criteria Scoring Framework

```python
# Solar/Wind resource mapping
# 1. Terrain analysis (slope, aspect, shading)
# 2. Wind resource assessment (ERA5 wind speed data)
# 3. Solar irradiance modeling
# 4. Land use / zoning overlay
# 5. Environmental constraints (wetlands, cultural resources)
# 6. Distance-to-grid estimation
# 7. LCOE estimation

siting_score = (
    (solar_irradiance / max_irradiance) * 0.3 +
    (wind_speed_score) * 0.25 +
    (terrain_score) * 0.15 +
    (accessibility_score) * 0.1 +
    (dist_to_grid_score) * 0.1 +
    (environmental_score) * 0.1
)

viable_sites = sites[sites["siting_score"] > 0.6].sort_values(
    "siting_score", ascending=False
)
```

## Siting Score Components

| Component | Weight | Data Source |
|-----------|--------|-------------|
| **Solar irradiance** | 30% | NREL NSRDB, ERA5-derived |
| **Wind speed/resource** | 25% | ERA5 10m wind, SPM |
| **Terrain** | 15% | DEM slope, aspect, shading |
| **Accessibility** | 10% | Road proximity, grid distance |
| **Distance to grid** | 10% | Transmission line proximity |
| **Environmental** | 10% | Wetlands, cultural resources |

## Solar Resource Assessment

```python
import pandas as pd

# Solar potential from DEM + weather data
solar_data = pd.DataFrame({
    "latitude": lat,
    "longitude": lon,
    "elevation": elevation,
    "annual_irradiation": annual_irradiation,  # kWh/m²/year
    "capacity_factor": capacity_factor,       # %
    "potential_gw_h": solar_energy * system_efficiency,
})

# Filter viable sites (e.g., > 1500 kWh/m²/year)
viable_sites = solar_data[
    solar_data["annual_irradiation"] > 1500
].sort_values("potential_gw_h")
```

## Terrain Constraints

```python
# DEM-based terrain analysis for solar
with rio_open("dem.tif") as src:
    dem = src.read(1)

# Slope filter (max 15° for fixed tilt)
slope = compute_slope(dem)
flat_areas = slope[slope < 15]  # degrees

# Aspect analysis (south-facing preferred in Northern Hemisphere)
aspect = compute_aspect(dem)
south_facing = (aspect > 315) | (aspect < 45)  # N315°E to N45°E

# Combine: flat AND south-facing
optimal = flat_areas & south_facing
```

## Environmental Constraints

```python
import geopandas as gdp

# Load environmental constraint layers
constraints = {
    "wetlands": gdp.read_file("wetlands.gpkg"),
    "floodway": gdp.read_file("floodway.gpkg"),
    "cultural_resources": gdp.read_file("cultural.gpkg"),
    "protected_areas": gdp.read_file("protected.gpkg"),
}

# Propose site
proposed_site = gdp.read_file("proposed_site.shp")

# Check constraints
issues = {}
for name, layer in constraints.items():
    impact = gdp.overlay(proposed_site, layer, how="intersection")
    if len(impact) > 0:
        issues[name] = len(impact)

# Environmental score (0 = no issues, 1 = many issues)
env_score = 1 - (len(issues) / len(constraints))
```

## Data Sources

| Dataset | Provider | Use |
|---------|----------|-----|
| **NREL NSRDB** | NREL | Solar resource data |
| **ERA5 wind** | ECMWF/Copernicus | 10m wind speed, direction |
| **S2 DEM** | Copernicus | Terrain analysis |
| **NREL Renewable Energy Siting Tool** | NREL | Pre-processed siting data |
| **Land use** | USFS/USGS | Zoning and land suitability |

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [DEM Processing & Terrain Analysis](dem-processing-terrain-analysis.md)