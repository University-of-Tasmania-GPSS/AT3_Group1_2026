# Methods

## Workflow

```{mermaid}
flowchart TD

  subgraph S1[1. Data]
    A[LiDAR point cloud]
    B[Building footprints]
    C[Hourly weather file]
  end

  subgraph S2[2. Preparation]
    D[0.5 m surface model]
    E[Two 600 m test areas]
    K[Roofs in each test area]
  end

  subgraph S3[3. Modelling]
    F[Our Python model]
    G[SEBE in QGIS]
  end

  subgraph S4[4. Results]
    H[Annual radiation per roof]
    J[Our model vs SEBE]
    I[Sandy Bay vs Howrah]
  end

  A --> D
  D --> E
  B --> K
  E --> F
  E --> G
  C --> F
  C --> G
  F --> H
  G --> H
  K --> H
  H --> J
  H --> I
```

## Data

| Data | Source | Details | Used for |
|---|---|---|---|
| LiDAR point cloud | Geoscience Australia, ELVIS | Converted to a 0.5 m digital surface model in ArcGIS Pro | Roof heights, slope and direction, and shading |
| Building footprints | LIST 2D Building Polygons | Hobart and Clarence council layers joined | Picking out the roofs and averaging radiation for each one |
| Weather | EPW typical year file, Hobart Ellerslie Road | 8,760 hourly values of GHI, DNI and DHI | The sunlight going into both models |
| Reference data | Global Solar Atlas | Long-term average GHI raster | Checking the weather file gives a sensible yearly total |

Everything was reprojected to GDA2020 / MGA zone 55 (EPSG:7855), so the layers line up and distances are in metres.

:::{dropdown} What do GHI, DNI and DHI mean?
- **GHI (global horizontal irradiance):** all the sunlight landing on flat ground.
- **DNI (direct normal irradiance):** sunlight coming straight from the sun.
- **DHI (diffuse horizontal irradiance):** sunlight scattered by the sky and clouds.

A roof is tilted, so the direct and diffuse parts have to be worked out separately and then added back together.
:::

## Data preparation

All of the preparation was done for the full study area first, and the two test areas were cut out at the end.

### Surface model

We first tried a coarser digital elevation model, but it couldn't pick up the shape of individual roofs. We downloaded a LiDAR point cloud from ELVIS and built a 0.5 m digital surface model in ArcGIS Pro. A surface model gives the height of whatever is on top, so it includes roofs and trees and not just the ground.

**Add in details on ELVIS**

| Setting | Value |
|---|---|
| Points used | First returns, noise removed |
| Interpolation | Binning, maximum value in each cell |
| Gap filling | Linear |
| Cell size | 0.5 m |

Trees were left in the surface model. Both models use the same one, so trees are treated the same way in each.

### Building footprints

The study area crosses two council areas, so the Hobart and Clarence building layers were joined into one. The joined layer was reprojected, clipped to the study area and cut down to the columns we needed.


### Weather data

The weather file is a typical year of hourly data from the Hobart Ellerslie Road station. Adding up GHI for the year gave 1,424 kWh/m². Most of that falls in summer ([](#fig-monthly-ghi)), which is why roof direction matters so much in Hobart. In winter the sun sits low in the northern sky, so a north-facing roof picks up a lot more than a south-facing one.

```{figure} Images/monthly_ghi.png
:label: fig-monthly-ghi
:width: 80%
:alt: Bar chart of monthly GHI at Hobart Ellerslie Road

Monthly global horizontal irradiance at Hobart Ellerslie Road.
```


### Global Solar Atlas

To check the weather file was accurate, we compared its yearly total with the Global Solar Atlas value at the same location. The weather file gave 1,424 kWh/m² and the atlas gave 1,353 kWh/m². 


### Test areas

SEBE could not process the full study area at 0.5 m, so we created two 600 m × 600 m samples out of the prepared data, one in Sandy Bay and one in Howrah. Both models were run on the same two samples so the results could be compared cell by cell.

% Add the number of buildings in each sample


## The two models

::::{tab-set}

:::{tab-item} Our Python model
Our model works out how much sunlight lands on each 0.5 m cell of roof, using its slope and the direction it faces.

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

**Slope and aspect.** Slope is how steep each cell is, and aspect is the compass direction it faces. Both were calculated from the surface model.

**Need to descrbe each step of the model**

:::

:::{tab-item} SEBE
SEBE (Solar Energy on Building Envelopes) is a model in the UMEP plugin for QGIS. It uses the surface model to cast shadows for each hour of the year, so a roof that is shaded by a neighbouring building, a tree or a slope gets less radiation. It also splits the sky into patches so diffuse light is counted from the parts of the sky each cell can see.

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

**JANE: please add SEBE input details here (how the wall height and aspect rasters and the met file were made, plus albedo, UTC offset and any other settings)**
:::

::::

:::{dropdown} Slope and aspect
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
| Shading from buildings and trees | [Yes/No] | Yes |
| Reflected radiation | [Yes/No] | Yes |
| What else?|||

## How we compared them

Both models produce a raster of annual radiation in kWh/m² for the same cells. We averaged each raster inside every building footprint to get one value per roof from each model, then compared them.
