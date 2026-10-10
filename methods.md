# Methods

## Workflow

```{mermaid}
flowchart LR
  A[LiDAR point cloud] --> B[0.5 m surface model]
  B --> C[Our Python model]
  B --> D[SEBE in QGIS]
  E[Weather file] --> C
  E --> D
  F[Building footprints] --> G[Annual radiation per roof]
  C --> G
  D --> G
  G --> H[Compare models and shores]
```

## Data

| Data | Source | Details |
|---|---|---|
| LiDAR point cloud | ELVIS | Converted to a 0.5 m digital surface model in ArcGIS Pro |
| Building footprints | LIST 2D Building Polygons | Hobart and Clarence council layers joined, 18,045 buildings in the study area |
| Weather | EPW typical year file, Hobart Ellerslie Road | 8,760 hourly values of GHI, DNI and DHI |
| Check data | Global Solar Atlas | Long-term average GHI raster |

Everything was reprojected to GDA2020 / MGA zone 55 (EPSG:7855).

## Data preparation

The building layers for the two councils were joined, reprojected and clipped to the study area. The surface model was checked for its cell size and coordinate system. As a check on the weather file, we added up GHI for the year and compared it with the Global Solar Atlas value at the same spot. The weather file gave 1,424 kWh/m² and the atlas gave 1,353 kWh/m², which is about 5% apart, so we were happy to use it.

:::{dropdown} Show the code: joining and clipping the buildings
```python
# Join the Hobart and Clarence buildings into one layer
buildings = pd.concat([buildings_h, buildings_c], ignore_index=True)

# Reproject to GDA2020 / MGA zone 55 (EPSG:7855)
buildings = buildings.to_crs(project_crs)

# Clip to the study area
buildings_clipped = gpd.clip(buildings, study_area)
```
:::

## The two models

::::{tab-set}

:::{tab-item} Our Python model
[2 to 3 sentences on the idea behind it.]

```{mermaid}
flowchart TD
  A[Surface model] --> B[Slope]
  A --> C[Aspect]
  D[Hourly GHI, DNI, DHI] --> E[Sun position each hour]
  B --> F[Radiation on each tilted cell]
  C --> F
  E --> F
  F --> G[Add up the year]
  G --> H[Average per roof]
```

[One short paragraph per step.]
:::

:::{tab-item} SEBE
SEBE (Solar Energy on Building Envelopes) is a model in the UMEP plugin for QGIS. It uses the surface model to cast shadows for every hour of the year, so a roof shaded by a neighbour, a tree or a hill gets less radiation.

```{mermaid}
flowchart TD
  A[Surface model] --> B[Wall height and aspect]
  C[Weather file] --> D[UMEP met format]
  A --> E[SEBE]
  B --> E
  D --> E
  E --> F[Annual radiation raster]
  F --> G[Average per roof]
```

[Your settings: albedo, UTC offset, whether you used a vegetation layer.]
:::

::::

:::{dropdown} Show the code: slope and aspect
```python
# Calculates slope raster in degrees
gdal.DEMProcessing(str(slope_path), str(dsm_path), "slope", slopeFormat="degree")

# Calculates aspect raster in degrees clockwise from north
gdal.DEMProcessing(str(aspect_path), str(dsm_path), "aspect")
```
:::

## How the models differ

| | Our model | SEBE |
|---|---|---|
| Software | Python | QGIS (UMEP) |
| Roof slope and direction | Yes | Yes |
| Shading from buildings and terrain | [Yes/No] | Yes |
| Reflected radiation | [Yes/No] | Yes |
| Run time | [ ] | [ ] |

## How we compared them

[What you measured: difference per roof, a scatter plot, average by shore.]

The full code is in our [GitHub repository](https://github.com/University-of-Tasmania-GPSS/AT3_Group1_2026).