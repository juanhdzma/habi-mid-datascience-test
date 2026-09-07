# Habi Data Science Assessment

Geospatial data science assessment for Bogotá real estate. The notebook combines property listings, Unidad de Planeamiento Local (UPL) boundaries, and cadastral data to quantify supply, identify local price patterns, and estimate property prices per square meter.

![License: AGPL-3.0](https://img.shields.io/badge/license-AGPL--3.0-blue) ![Python](https://img.shields.io/badge/python-data%20science-3776AB) ![Geospatial](https://img.shields.io/badge/geospatial-GeoPandas-139C5A)

## Contents

- [Problem and scope](#problem-and-scope)
- [Data sources and assumptions](#data-sources-and-assumptions)
- [Analysis design](#analysis-design)
- [Spatial analysis](#spatial-analysis)
- [Price model and validation](#price-model-and-validation)
- [Outputs](#outputs)
- [Running the notebook](#running-the-notebook)
- [Limitations and next steps](#limitations-and-next-steps)
- [License](#license)

## Problem and scope

The assessment answers four related questions about Bogotá's residential market:

1. How many published property listings fall within each UPL?
2. Which UPLs form high-high, high-low, low-high, and low-low local clusters for price per square meter?
3. How accurately can listing prices be estimated from the available features?
4. How many residential cadastral properties fall within each UPL?

The project is an exploratory analysis and model-validation exercise. It is not a production pricing service or a continuously updated market dataset.

## Data sources and assumptions

The analysis combines UPL geometries and cadastral datasets from Bogotá open data with a dataset of properties published on web sites. The primary repository artifacts are:

- [`upl.json`](upl.json), the UPL boundary dataset.
- [`inmuebles.csv.gz`](inmuebles.csv.gz), the property-listings dataset.
- [`catastral.zip`](catastral.zip), the cadastral input artifact.

The notebook documents the public source URLs and the preparation of the supplementary cadastral inputs. It treats the included files as a point-in-time snapshot. Results therefore describe the supplied data, not a live view of the market.

Spatial joins only have a meaningful interpretation after all geometries share a coordinate reference system. The workflow explicitly reprojects the UPL geometries before assigning listings or cadastral properties to a boundary. Records outside a valid UPL geometry are not evidence of a market absence; they may reflect incomplete coordinates, coverage, or source-data quality.

## Analysis design

[`Prueba_Data_Science.ipynb`](Prueba_Data_Science.ipynb) is the central artifact and should be executed from top to bottom. Its workflow is organized as follows:

1. Load and inspect the UPL geometry and property-listings data.
2. Align geographic coordinate reference systems.
3. Perform spatial joins to attach UPL identifiers to listings and cadastral properties.
4. Aggregate listing and property counts by UPL.
5. Build a geographic visualization of listing supply.
6. Calculate local spatial autocorrelation for price per square meter.
7. Train and evaluate a property-price model.

The analysis keeps spatial operations, visualization, and modeling in one notebook so the intermediate datasets and assumptions are inspectable in the same place as the results.

## Spatial analysis

### Listings by UPL

The first spatial join assigns each listing to a UPL. Aggregating those matches produces the number of active offers per planning unit. The resulting Folium map is useful for checking both concentration and geographic coverage before interpreting the price model.

[`mapa_upl.html`](mapa_upl.html) contains the interactive listing-density map. Its tooltip exposes the UPL identifier and listing count, allowing the result to be inspected without rerunning the notebook.

### Local Moran's I

The notebook calculates local Moran's I for price per square meter using a neighborhood structure. It categorizes UPLs into high-high, high-low, low-high, and low-low clusters:

- **High-high:** high values surrounded by high values.
- **Low-low:** low values surrounded by low values.
- **High-low** and **low-high:** local outliers relative to their neighbors.

This is a descriptive spatial statistic, not causal evidence. A cluster can be driven by coverage, composition of listings, or unobserved property characteristics as well as location.

### Cadastral properties by UPL

The final spatial join assigns residential cadastral properties to UPLs and aggregates their counts. It provides a second geographic baseline that can be compared with published supply, while remaining subject to the source datasets' update cadence and coverage.

## Price model and validation

An XGBoost regressor estimates property prices from the available listing features. The notebook evaluates it with cross-validation and reports both MAPE and RMSE:

- **MAPE** makes percentage error easier to compare across price ranges.
- **RMSE** gives higher weight to large absolute errors.

The model is evaluated as part of the assessment, not packaged for serving. Its reported performance is specific to the supplied snapshot and the selected features. Any production use would require a time-based validation strategy, monitored input quality, repeatable data-versioning, and a policy for updating the model when market conditions change.

## Outputs

| Artifact | Purpose |
| --- | --- |
| [`Prueba_Data_Science.ipynb`](Prueba_Data_Science.ipynb) | Complete analysis, spatial joins, local Moran's I, and model evaluation. |
| [`mapa_upl.html`](mapa_upl.html) | Interactive map of property-listing counts by UPL. |
| [`upl.json`](upl.json) | UPL geometries used by the spatial analysis. |
| [`inmuebles.csv.gz`](inmuebles.csv.gz) | Property-listings input. |
| [`catastral.zip`](catastral.zip) | Cadastral source artifact. |

## Running the notebook

Create a Jupyter-compatible Python environment and install the libraries imported by the notebook:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install jupyterlab pandas numpy geopandas shapely libpysal esda xgboost scikit-learn folium branca matplotlib tqdm
jupyter lab
```

Open [`Prueba_Data_Science.ipynb`](Prueba_Data_Science.ipynb) and run its cells in order. The notebook contains the source references and the preparation steps for the geospatial inputs.

## Limitations and next steps

- The repository does not pin a dependency lockfile or package the notebook as a reproducible command-line pipeline.
- The source files are snapshots, so model metrics and spatial clusters should be recalculated when the market data changes.
- The analysis can be extended with time-aware validation, feature monitoring, data-quality checks, and a scheduled refresh from the public sources.
- A production pricing system would need explicit handling for missing geographies, out-of-distribution listings, and uncertainty intervals, not only point estimates.

## License

[AGPL-3.0](LICENSE)
