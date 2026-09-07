# Habi Data Science Assessment

Geospatial data science assessment for Bogotá real estate: joins property listings to Unidad de Planeamiento Local (UPL) boundaries, maps listing density, measures local spatial autocorrelation, and estimates property prices per square meter.

![License: AGPL-3.0](https://img.shields.io/badge/license-AGPL--3.0-blue) ![Python](https://img.shields.io/badge/python-data%20science-3776AB)

## Contents

- [Scope](#scope)
- [Data sources](#data-sources)
- [Method](#method)
- [Outputs](#outputs)
- [Running the notebook](#running-the-notebook)
- [License](#license)

## Scope

The notebook answers four related questions about Bogotá's residential market:

- How many published property listings fall within each UPL?
- Which UPLs form high-high, high-low, low-high, and low-low local clusters for price per square meter?
- How accurately can listing prices be estimated from the available features?
- How many residential cadastral properties fall within each UPL?

## Data sources

The analysis uses Bogotá's open UPL and cadastral datasets together with a property-listings dataset. The repository keeps the primary inputs and the notebook documents the source URLs and preparation steps for the supplementary cadastral data.

## Method

`Prueba_Data_Science.ipynb` performs the workflow in order:

1. Loads UPL geometries and property listings, then aligns them to a common geographic coordinate reference system.
2. Uses spatial joins to assign listings and cadastral properties to a UPL.
3. Produces a Folium choropleth for listing counts by UPL.
4. Calculates local Moran's I with PySAL to identify spatial clusters in price per square meter.
5. Trains and evaluates an XGBoost regressor using cross-validation, reporting MAPE and RMSE for price estimation.

## Outputs

- [`mapa_upl.html`](mapa_upl.html) is the interactive map of property listings by UPL.
- The notebook contains the local Moran's I classification, model evaluation, and cadastral-property aggregation.
- [`upl.json`](upl.json), [`inmuebles.csv.gz`](inmuebles.csv.gz), and [`catastral.zip`](catastral.zip) are the included source artifacts.

## Running the notebook

Use a Jupyter-compatible Python environment with the libraries imported by the notebook: Pandas, NumPy, GeoPandas, Shapely, PySAL, XGBoost, scikit-learn, Folium, Branca, Matplotlib, and tqdm.

Open [`Prueba_Data_Science.ipynb`](Prueba_Data_Science.ipynb) and run the cells in order. The notebook includes the source references and data-preparation steps needed for the geospatial joins and model.

## License

[AGPL-3.0](LICENSE)
