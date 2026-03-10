# Boston Snow Map -- Winter 2025-2026

An interactive street-level snowfall visualization for Greater Boston, built end-to-end from raw weather station data to a live web map.

**Live map:** https://mekhalraj.github.io/boston-snow-map

---

## What It Shows

Every street in Boston, Cambridge, Somerville, Brookline, Newton, Medford, and Watertown -- 41,000+ segments -- colored by estimated snowfall intensity. Switch between 7 individual storms or view the full season. Hover any street for exact measurements. Toggle plow routes.

The 2025-26 season hit 62.5 inches at Logan Airport, ranking #10 in 46 years of records -- after back-to-back near-snowless winters that combined for just 22 inches.

---

## How It Works

### Data Pipeline (Python / Google Colab)

The notebook `boston_snow_pipeline.ipynb` builds all data files from scratch:

**1. CoCoRaHS Station Data**
- Pulls daily snowfall readings from 34 volunteer observer stations across the Boston metro
- Filters to quality stations (excludes zero-reading stations with likely data gaps)
- Aggregates by storm event and full season

**2. NOAA GHCN Historical Data**
- Fetches 46 winters of seasonal snowfall from Logan Airport (USW00014739) via the NOAA CDO API
- Includes retry logic for 503 rate-limit errors
- Takes the higher of NOAA vs CoCoRaHS for the current season

**3. Street Network**
- Downloads drive network geometry for 7 Greater Boston cities via OSMnx
- Extracts midpoint coordinates for each of 41,000+ street segments
- Classifies road type (major / secondary / residential)

**4. Spatial Interpolation**
- Applies Inverse Distance Weighting (IDW), power=2, k=8 nearest neighbors
- Estimates snowfall at each street midpoint from the 8 nearest CoCoRaHS stations
- Runs separately for each storm event and for the full season

**5. Plow Routes**
- Attempts to load official Boston plow route data from Boston Open Data portal
- Falls back to road classification (major/secondary = Priority 1-2) for cities without open data

### Frontend (Single HTML File)

No frameworks, no build step. Everything in `index.html`:

- **MapLibre GL** -- street map rendering with per-feature color interpolation
- **D3.js** -- 46-year historical bar chart
- **Canvas API** -- animated snowfall background
- **CARTO Dark Matter** -- basemap
- Deployed on **GitHub Pages**

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Full interactive dashboard |
| `boston_streets_snow.geojson` | 41k street segments with snowfall per storm (25MB) |
| `boston_storms.json` | 7 storm events with dates, averages, peaks |
| `boston_historical.json` | 46 winters of Logan Airport snowfall data |
| `boston_neighborhoods.geojson` | Neighborhood boundaries with avg snowfall |
| `boston_snow_pipeline.ipynb` | Full data pipeline (Google Colab) |

---

## Tech Stack

| Layer | Tools |
|-------|-------|
| Data collection | Python, requests, pandas |
| Spatial analysis | GeoPandas, OSMnx, scipy (cKDTree) |
| Visualization | MapLibre GL, D3.js |
| Pipeline | Google Colab |
| Hosting | GitHub Pages |

---

## Running the Pipeline

1. Open `boston_snow_pipeline.ipynb` in Google Colab
2. Upload `DailyPrecipReports_MA_*.csv` from CoCoRaHS (Massachusetts, daily precip, Oct 2025 onward)
3. Add your NOAA API key (free at https://www.ncdc.noaa.gov/cdo-web/token)
4. Run all cells top to bottom (~15 min total)
5. Download all files from the `outputs/` folder
6. Replace the corresponding files in this repo

---

## Data Sources

- **CoCoRaHS** -- Community Collaborative Rain, Hail and Snow Network (cocorahs.org)
- **NOAA GHCN Daily** -- Global Historical Climatology Network (ncdc.noaa.gov)
- **OpenStreetMap** -- Street geometry via OSMnx
- **Boston Open Data** -- Neighborhood boundaries and plow routes (data.boston.gov)

---

Built by [Mekhal Raj](https://www.linkedin.com/in/mekhalrajr/)
