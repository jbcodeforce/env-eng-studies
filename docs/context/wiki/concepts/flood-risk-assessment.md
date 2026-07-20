---
title: "Flood Risk Assessment"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [dem-processing-terrain-analysis, renewable-energy-siting]
tags: [flood-risk, sea-level-rise, storm-surge, climate-adaptation, vulnerability]
---

# Flood Risk Assessment

Flood risk combines static hazards, dynamic climate projections, and population/vulnerability data to estimate exposure and inform adaptation planning.

## Risk Index Framework

```python
import pandas as pd

# Flood risk assessment using DEM + climate data
flood_risk = pd.DataFrame()

# Static hazard
flood_risk["base_flood_probability"] = base_flood_data
flood_risk["topographic_exposure"] = terrain_analysis(df)

# Dynamic climate component
flood_risk["sea_level_rise_2050"] = climate_projection(df, year=2050)
flood_risk["storm_surge_100yr"] = storm_surge_model(df)

# Combined risk index
flood_risk["flood_risk_score"] = (
    flood_risk["base_flood_probability"] * 0.3 +
    flood_risk["topographic_exposure"] * 0.2 +
    flood_risk["sea_level_rise_2050"] * 0.3 +
    flood_risk["storm_surge_100yr"] * 0.2
)
```

## Components of Flood Risk

| Component | Timescale | Data Source | Weight |
|-----------|-----------|-------------|--------|
| **Base flood probability** | 100-yr recurrence | FEMA flood maps, HEC-RAS | 30% |
| **Topographic exposure** | Annual | DEM-derived flood hazard zones | 20% |
| **Sea level rise** | 2050-2100 | IPCC scenarios, tide gauges | 30% |
| **Storm surge** | 100-yr event | NWS models, SLOSH | 20% |

## Sea Level Rise Projection

```python
# Example: Sea level rise contribution to flood risk
flood_risk["sea_level_rise_2050"] = climate_projection(df, year=2050)

# Under CMIP6 scenarios:
# SSP1-2.6: ~0.3m by 2050 (best case)
# SSP5-8.5: ~0.8m by 2050 (high emission)
```

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [DEM Processing & Terrain Analysis](dem-processing-terrain-analysis.md)
- [Renewable Energy Siting](renewable-energy-siting.md)