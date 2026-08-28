# MT Ski Tour Guru

An interactive map for ski touring routes in southwest Montana that filters routes based on avalanche danger rating (Danger Indicator) and terrain danger rating (Terrain Indicator) using the QRM (Quantitative Reduction Method) evaluation approach.

## Project Overview

This project aims to develop a Python-based interactive web map for ski touring route planning, inspired by [skitourenguru.com](https://www.skitourenguru.ch). The tool applies quantitative risk assessment methodology to backcountry skiing in southwest Montana, combining avalanche forecast danger levels with terrain-based risk indicators derived from high-resolution DEMs.

## Motivation

Backcountry skiers in rural settings like southwest Montana lack accessible tools to quantitatively assess avalanche risk relative to terrain. This project applies the QRM methodology—developed by Schmudlach et al. and validated by Winkler et al. in Switzerland—to a smaller dataset and region. The goal is to practice Python development, geospatial data analysis, and program architecture while testing whether QRM effectiveness transfers to different snow climates and terrain.

## Methodology

The project is based on the **Quantitative Reduction Method (QRM)**, which combines two indicators:

### Terrain Indicator (TI)
- Continuous value between 0 and 1
- Derived from the Relevant Slope Area (RSA) around each point
- Considers slope angles and RSA size to indicate avalanche terrain appropriateness
- Classifications:
  - TI < 0.25: No avalanche terrain
  - 0.25–0.5: Atypical avalanche terrain
  - 0.5–0.75: Typical avalanche terrain
  - > 0.75: Very typical avalanche terrain

### Danger Indicator (DI)
- Spatially interpolated avalanche forecast danger level
- Accounts for elevation and aspect
- Integrates current avalanche bulletin information

### Risk Assessment
QRM combines DI and TI via a smoothing kernel applied to historical accident/travel data to produce relative risk scores.

## Current Status

**Phase 1: Foundation & Algorithm Development**
- Project planning complete
- Research papers acquired (Schmudlach et al., Winkler et al., SLABS)
- *In Progress:* Understanding RSA and TI calculation methodology
- Next: Implement TI calculation in Python

**Data Available:**
- 1 route: Hyalite Peak approach (Hyalite Canyon, Bozeman, MT)
- 1m resolution DEM for Hyalite area

## Project Roadmap

### Phase 1: TI Calculation Engine (Current)
1. Reverse-engineer RSA algorithm from Schmudlach et al. papers
2. Build Python module to calculate TI from DEMs
3. Test on Hyalite Peak route with 1m DEM
4. Validate results against published examples

**Deliverable:** `ti_calculator.py` — reusable TI calculation module

### Phase 2: Route Processing & Basic Map
1. Load and resample GPS tracks into points (10–25m intervals)
2. Build interactive map using Folium/Leaflet
3. Overlay TI values on map, color-coded by terrain classification
4. Test with Hyalite Peak route

**Deliverable:** Interactive HTML map showing route with TI classification

### Phase 3: Danger Indicator Integration
1. Integrate avalanche forecast data (NOAA/local sources or synthetic test data)
2. Implement spatial interpolation for DI across terrain
3. Calculate DI for each route point

**Deliverable:** DI calculation module; map showing DI by elevation/aspect

### Phase 4: QRM Risk Model
1. Collect historical accident/travel data for SW Montana (if available)
2. Implement QRM smoothing kernel and risk binning
3. Calculate relative risk scores for routes
4. Color-code map by risk category (slight/elevated/high)

**Deliverable:** Full risk assessment tool

### Phase 5: Refinement & Expansion
1. Add more routes (additional Hyalite routes, Bridger Range, Absarokas, etc.)
2. Optimize performance for larger datasets
3. Deploy as web application
4. Gather user feedback and refine risk thresholds

## Tech Stack

- **Python 3.9+**
- **Geospatial Libraries:**
  - `rasterio` — DEM I/O and raster operations
  - `richdem` — Terrain analysis (slope, aspect, curvature)
  - `geopandas` / `shapely` — Vector operations for RSA polygons
  - `numpy`, `scipy` — Array math and morphology
- **Data Processing:**
  - `pandas` — Tabular data
  - `geopandas` — Geodataframes
- **Visualization:**
  - `folium` — Interactive maps (Phase 2)
  - `matplotlib` — Exploratory analysis
- **Web Frontend (Phase 2+):**
  - Leaflet.js or Folium-based HTML

## Installation & Setup

### Requirements
- Python 3.9 or later
- GDAL/GEOS system libraries (for geopandas)
- A 1m+ resolution DEM in GeoTIFF format

### Install Dependencies

```bash
pip install rasterio richdem geopandas shapely numpy scipy pandas folium matplotlib
```

### Running TI Calculation (Phase 1)

```python
from ti_calculator import calculate_ti_from_dem
import rasterio

# Load DEM
with rasterio.open('hyalite_dem_1m.tif') as src:
    dem = src.read(1)
    transform = src.transform

# Calculate TI
ti_map = calculate_ti_from_dem(dem, transform)
```

## Data Requirements

### For TI Calculation
- **DEM:** 1m resolution preferred; minimum 5m
- **Route:** GPS track (GPX or GeoJSON) resampled to ~10–25m point intervals

### For DI Calculation (Phase 3)
- **Avalanche Forecast:** NOAA/SLF-style danger bulletins with elevation/aspect zones
- **Or:** Synthetic test data for algorithm development

### For QRM Validation (Phase 4)
- Historical accident data (if available)
- Historical backcountry activity data (GPS tracks, track counts, etc.)

## References

### Core Methodology Papers

1. **Schmudlach, G., & Kühler, U. (2016).** Deriving a terrain indicator for avalanche risk assessment from high resolution digital elevation models. In *Proceedings of the International Snow Science Workshop* (pp. 18–25).

2. **Schmudlach, G., Hirschberg, J., & Kühler, U. (2018a).** Avalanche Risk Assessment: Towards Defining the Terrain Indicator. In *Proceedings of the International Snow Science Workshop*.

3. **Schmudlach, G., Hirschberg, J., & Kühler, U. (2018b).** A new algorithm for avalanche terrain classification and its application to the quantitative reduction method. In *The Cryosphere*.

4. **Winkler, K., Techel, F., Середа, А., Darms, G., & Schweizer, J. (2021).** On the correlation between the forecast avalanche danger and avalanche risk taken by backcountry skiers in Switzerland. *Cold Regions Science and Technology*, 188, 103299.

5. **Degraeuwe, B., et al. (2024).** SLABS: Statistical Modelling of Avalanche Danger and Backcountry Skiers (Technical Report). Swiss Avalanche Warning Service (SLF).

### Additional Resources

- [skitourenguru.ch](https://www.skitourenguru.ch) — Reference implementation
- [Swiss Avalanche Warning Service](https://www.slf.ch) — Avalanche data and methods
- [Folium Documentation](https://python-visualization.github.io/folium/) — Interactive maps
- [Rasterio Documentation](https://rasterio.readthedocs.io) — DEM I/O

## Project Goals (Learning Outcomes)

- ✅ Understand avalanche risk quantification and terrain analysis
- ✅ Practice geospatial data processing with Python
- ✅ Develop modular, reusable code for terrain analysis
- ✅ Build interactive data visualization tools
- ✅ Test applicability of Swiss QRM to US backcountry context
- ✅ Create reproducible, documented analysis pipeline

## Contributing & Feedback

This is a personal learning project. Feedback and suggestions welcome.

## License

MIT License — See LICENSE file for details.

## Author

Jonas David — [GitHub](https://github.com/jonas11david)

---

**Last Updated:** August 2026  
**Phase:** 1 — TI Calculation Engine (In Progress)
