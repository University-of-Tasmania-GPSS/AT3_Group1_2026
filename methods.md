# Methods

## Workflow


## Data

| Data | Source | Details |
|---|---|---|
| LiDAR point cloud | ELVIS | Converted to a 0.5 m digital surface model in ArcGIS Pro |
| Building footprints | LIST 2D Building Polygons | Hobart and Clarence council layers joined, 18,045 buildings in the study area |
| Weather | EPW typical year file, Hobart Ellerslie Road | 8,760 hourly values of GHI, DNI and DHI |
| Check data | Global Solar Atlas | Long-term average GHI raster |

Everything was reprojected to GDA2020 / MGA zone 55 (EPSG:7855).

## Data preparation

The building layers for the two councils were joined, reprojected and clipped to the study area. 

:::{dropdown} Show the code: joining and clipping the buildings
```python

```
:::

## The two models

::::{tab-set}

:::{tab-item} Our Python model

:::

:::{tab-item} SEBE
SEBE (Solar Energy on Building Envelopes) is a model in the UMEP plugin for QGIS.
:::

::::

:::{dropdown} Show the code: slope and aspect
```python

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

Comparision