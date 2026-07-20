---
title: "Environmental Compliance Mapping (CEQA/NEPA)"
created: 2026-07-19
updated: 2026-07-19
sources: [environmental-python-roadmap.md]
related: [spatial-joins-statistics, coordinate-reference-systems]
tags: [ceqa, nepa, compliance, overlay, environmental-screening, regulatory]
---

# Environmental Compliance Mapping (CEQA/NEPA)

CEQA (California Environmental Quality Act) and NEPA (National Environmental Policy Act) screening uses geospatial overlay of multiple environmental factors against proposed project footprints to identify potential impacts requiring further study.

## Multi-Factor Overlay Workflow

```python
import geopandas as gpd

# CEQA factors as GeoDataFrames
factors = {
    "floodplain": gpd.read_file("floodway_100yr.gpkg"),
    "wetlands": gpd.read_file("wetlands_national.gpkg"),
    "habitat": gpd.read_file("critical_habitat.gpkg"),
    "soil_erosion": gpd.read_file("erosion_risk.gpkg"),
    "brownfield": gpd.read_file("brownfield_sites.gpkg"),
}

# Proposed project footprint
project = gpd.read_file("proposed_project.shp")

# Screen each factor
issues = {}
for name, layer in factors.items():
    impact = gpd.overlay(project, layer, how="intersection")
    issues[name] = {
        "count": len(impact),
        "geometry": impact.geometry,
        "area_acres": impact.geometry.area / 4046.86,  # to acres
    }

# Generate screening report
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

## CEQA Impact Categories

| Category | Typical Data Layer | Regulatory Threshold |
|----------|--------------------|---------------------|
| **Wetlands** | 404(b)(1) wetlands (FWS) | Any impact triggers Section 404 permit |
| **Floodplain** | FEMA 100-yr floodway/floodway overlay | Inundation changes = major impact |
| **Habitat** | Critical habitat (FWS), protected species | Take prohibition or Incidental Take Permit |
| **Brownfields** | Brownfield sites registry | Phase I assessment required |
| **Historic** | SHPO registers, NRHP | Archaeological survey required |
| **Cultural resources** | Tribal cultural resources | Section 106 (NHPA) compliance |
| **Air quality** | NAAQS attainment areas | Additional air quality studies |
| **Traffic** | Level of Service (LOS) thresholds | Additional traffic impact analysis |

## Statistical Compliance Analysis

For monitoring compliance rather than project screening:

```python
# Identify wells in multiple watersheds (compliance)
analysis = monitoring_wells.sjoin(watersheds, how="left", predicate="within")

multi_well = analysis[analysis["watershed_name"].duplicated()].groupby("watershed_name")

# Buffer analysis for setback violations
facility = gpd.read_file("industrial_sites.gpkg")
setback = facility.buffer(500, cap_style=2)
violations = residential.sjoin(setback, how="left", predicate="intersects")
```

## Workflow Integration

Compliance mapping feeds into:
- **Environmental Impact Statements (EIS)** — Full regulatory review
- **Environmental Assessments (EA)** — Screening document
- **Negative Declarations (ND)** — No significant impact
- **Mitigation measures** — Required actions to reduce impacts
- **Regulatory reporting** — CERCLA, RCRA compliance

## Sources
- [Summary](../summaries/geospatial-analysis-environmental-engineering.md)

## Related
- [Spatial Joins & Statistics](spatial-joins-statistics.md)
- [Coordinate Reference Systems](coordinate-reference-systems.md)