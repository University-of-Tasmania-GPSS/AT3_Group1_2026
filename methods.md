# Methods

## Workflow

```{mermaid}
flowchart TD

  A[LiDAR point cloud]
  B[Building footprints]
  C[Hourly weather file]

  D[0.5 m surface model]
  F[Our Python model]
  G[SEBE in QGIS]

  H[Annual radiation per roof]
  J[Our model vs SEBE]

  A --> D
  D --> F
  D --> G
  C --> F
  C --> G
  B --> H
  F --> H
  G --> H
  H --> J

  classDef input fill:#cfe2f3,stroke:#3d85c6,color:#000
  classDef process fill:#eeeeee,stroke:#666666,color:#000
  classDef python fill:#fff2cc,stroke:#bf9000,color:#000
  classDef sebe fill:#ead1dc,stroke:#a64d79,color:#000
  classDef output fill:#d9ead3,stroke:#38761d,color:#000

  class A,B,C input
  class D process
  class F python
  class G sebe
  class H,J output
```



```{mermaid}
flowchart TD

  A[LiDAR point cloud]
  B[Building footprints]
  C[Hourly weather file]

  D[0.5 m surface model]
  E[Slope and aspect]

  F[Our Python model]
  G[SEBE in QGIS]

  H1[Python yearly radiation]
  H2[SEBE yearly radiation]

  I[Slope-corrected roof area]
  J[Yearly sunlight per roof]
  K[Our model vs SEBE]

  L[Suitability rules]
  M[Suitable roof per building]

  A --> D
  D --> E
  D --> F
  E --> F
  C --> F
  D --> G
  C --> G
  F --> H1
  G --> H2
  H1 --> I
  H2 --> I
  E --> I
  B --> J
  I --> J
  J --> K
  H1 --> L
  E --> L
  B --> L
  L --> M

  classDef input fill:#cfe2f3,stroke:#3d85c6,color:#000
  classDef process fill:#eeeeee,stroke:#666666,color:#000
  classDef python fill:#fff2cc,stroke:#bf9000,color:#000
  classDef sebe fill:#ead1dc,stroke:#a64d79,color:#000
  classDef output fill:#d9ead3,stroke:#38761d,color:#000

  class A,B,C input
  class D,E,I,L process
  class F,H1 python
  class G,H2 sebe
  class J,K,M output
```


## Data

| Data | Source | Details | Used for |
|---|---|---|---|
| LiDAR point cloud | Geoscience Australia, ELVIS | Converted to a 0.5 m digital surface model in ArcGIS Pro | Roof heights, slope and direction, and shading |
| Building footprints | LIST 2D Building Polygons | Hobart and Clarence council layers joined | Picking out the roofs and averaging radiation for each one |
| Weather | EPW typical year file, Hobart Ellerslie Road | 8,760 hourly values of GHI, DNI and DHI | The sunlight going into both models |

Everything was reprojected to GDA2020 / MGA zone 55 (EPSG:7855).


## Data preparation

All of the preparation was done for the full study area first, and the two subsets were extracted at the end. 


### Surface model

A 1m digital elevation model was first downloaded, but it couldn't identify the shape of individual roofs. A LiDAR point cloud from ELVIS was then loaded and built a 0.5 m digital surface model in ArcGIS Pro. A surface model gives the height of whatever is on top, so it includes roofs and trees and not just the ground.


### Building footprints

The study area crosses two council areas, so the Hobart and Clarence building layers were joined into one. The joined layer was reprojected, and clipped to the study area.


### Weather data

The weather file is a projected typical year of hourly data from the Hobart Ellerslie Road station. Adding up GHI for the year gave 1,424 kWh/m². Most of that falls in summer ([](#fig-monthly-ghi)), which is why roof direction matters so much in Hobart. As a check, we compared this with the Global Solar Atlas value for the same location, which was 1,353 kWh/m².

```{figure} Images/monthly_ghi.png
:label: fig-monthly-ghi
:width: 80%
:alt: Bar chart of monthly GHI at Hobart Ellerslie Road

Monthly global horizontal irradiance at Hobart Ellerslie Road.
```


### Subsets

SEBE could not process the full study area at 0.5 m, so we created two 600 m × 600 m subsets out of the prepared data, one in Sandy Bay and one in Howrah. Both models were run on the same two samples so the results could be compared accurately. 
In the Sandy Bay Subset there were 484 buildings and in the Howrah subset there were 257 buildings. 


## The two models

:::::{tab-set}

::::{tab-item} Our Python model

```{mermaid}
flowchart TD
  A[Surface model] --> B[Slope and aspect<br>GDAL]
  C[Weather file] --> D[Sun position<br>pvlib]
  D --> E[Typical day<br>15th of each month]
  A --> F[Skyline height<br>WhiteboxTools]
  E --> F
  F --> G[Shadow mask]
  B --> H[Direct sunlight]
  E --> H
  G --> H
  C --> H
  B --> I[Diffuse sunlight]
  C --> I
  B --> J[Reflected sunlight]
  C --> J
  H --> K[Yearly total<br>kWh/m²]
  I --> K
  J --> K
```

Our model works out how much sunlight lands on every 0.5 m cell over a year. It adds up three parts: direct sunlight, diffuse sunlight from the sky, and sunlight reflected off the ground.

**1. Surface characteristics.** Slope, aspect and roughness were calculated from the surface model with GDAL. Slope and aspect go into the model. Roughness was calculated but not used in the end.

:::{dropdown} Slope
How steep each cell is, in degrees. A flat roof is close to 0° and a wall is close to 90°.

```{figure} Images/slope.png
:label: fig-slope
:width: 100%
:alt: Slope maps of the Sandy Bay and Howrah subsets side by side

Slope in degrees for the Sandy Bay (left) and Howrah (right) subsets.
```
:::

:::{dropdown} Aspect
The compass direction each cell faces.

```{figure} Images/aspect.png
:label: fig-aspect
:width: 100%
:alt: Aspect maps of the Sandy Bay and Howrah subsets side by side

Aspect in degrees clockwise from north for the Sandy Bay (left) and Howrah (right) subsets.
```
:::

:::{dropdown} Roughness
How bumpy the surface is around each cell. A clean roof face is smooth, and chimneys, roof edges and trees show up as rough.

```{figure} Images/roughness.png
:label: fig-roughness
:width: 100%
:alt: Roughness maps of the Sandy Bay and Howrah subsets side by side

Surface roughness for the Sandy Bay (left) and Howrah (right) subsets.
```
:::

**2. Sun position.** For every hour in the weather file, pvlib works out where the sun is in the sky: how high it is and its compass direction.

**3. Typical day.** Casting shadows for every hour of the year takes too long at 0.5 m. We used the sun's position on the 15th of each month as a typical day for that month.

**4. Shadows.** For each daylight hour of the typical day, WhiteboxTools works out how high the skyline is in the direction of the sun. If the skyline is higher than the sun, the cell is in shadow.

**5. Direct sunlight.** For each hour, the direct sunlight from every day in that month is added up, scaled by how directly the cell faces the sun, and set to zero where the cell is in shadow.

**6. Diffuse sunlight.** The sky is treated as equally bright everywhere. A flat cell sees the whole sky, and a tilted cell sees less of it.

**7. Reflected sunlight.** Tilted cells also pick up some sunlight bounced off the ground. We used an albedo of 0.15, the same as SEBE.

**8. Yearly total.** The three parts are added together and converted to kWh/m².

::::

::::{tab-item} SEBE
SEBE (Solar Energy on Building Envelopes) is a solar model in the UMEP plugin for QGIS (Lindberg et al., 2015). It uses the surface model and a weather file to work out how much sunlight reaches each cell over a year, taking into account shading from nearby buildings and trees.

```{mermaid}
flowchart TD
  A[Surface model] --> B[Wall height and aspect]
  C[Weather file] --> D[UMEP met format]
  A --> E[SEBE]
  B --> E
  D --> E
  E --> F[Annual radiation raster]
```

The weather file was converted to UMEP's text format with the UMEP pre-processing tool. The same tool set was used to make wall height and wall aspect rasters from each subset's surface model, leaving out walls lower than 3 m. SEBE was then run on these layers to estimate solar radiation for a typical year.

SEBE would not run in current versions of QGIS at first, because a NumPy update broke the sun position step of the plugin. We fixed this by editing a small part of the plugin code so it worked with the newer NumPy version. Because the fix was made to the copy of the plugin installed on one computer, both subsets were run through SEBE on that same machine so the settings stayed consistent.

::::

:::::


## How the models differ

| | Our model | SEBE |
|---|---|---|
| Software | Python (GDAL, pvlib, WhiteboxTools) | QGIS (UMEP) |
| Roof slope and direction | Yes | Yes |
| Sun positions | Typical day for each month | Sky patches covering the whole year |
| Shading of direct sunlight | Yes | Yes |
| Shading of diffuse sunlight | No | Yes |
| Sky | Equally bright everywhere | 145 patches |
| Reflected sunlight | From the ground only | From the ground and walls |


## How we compared them

Both models produce a final raster of yearly sunlight in kWh/m² for the same 0.5 m cells. Turning this into a total for each roof, slope needs to be accounted for first. A sloped roof has more surface area than it looks like from above, so each cell's area was corrected for slope ([](#eq-area)):

```{math}
:label: eq-area

A_{actual} = \frac{A_{cell}}{\cos(\theta)}
```

where $A_{cell}$ is the cell area of 0.25 m² (0.5 m × 0.5 m) and $\theta$ is the slope of the cell (UMEP, n.d.). Each cell's yearly sunlight (kWh/m²) was multiplied by its actual area to give the yearly sunlight in kWh for that cell. Zonal statistics in QGIS were then used to add these up for each building, giving the total yearly sunlight on each roof in kWh per year. The same steps were applied to the SEBE output and our Python model output, so the two could be compared roof by roof.


## Suitability criteria

A cell getting plenty of sun is not always somewhere a panel can go. It might be a tree, a wall or the ground. We set three rules, and a cell only counts as suitable if it passes all of them.

| Rule | Limit | Why | Source |
|---|---|---|---|
| On a building | Inside a building footprint | Only roofs count, not ground or trees | LIST building footprints |
| Not a wall | Slope of 65° or less | Steeper cells are walls or roof edges | UMEP SEBE tutorial |
| Enough sun | At least 80% of the yearly sunlight on unshaded flat surface | Leaves out shaded and poorly angled roof | Roberts, Copper and Bruce (2018) |

There is no separate rule for aspect or shading. South-facing and shaded cells get less sun, so they already fail the sunlight rule. Roughness was also left out, because we could not find a limit to cite for it and steep roof edges are already removed by the slope rule.

The suitable cells were then added up for each building. A building was counted as viable if it had at least 10 m² of suitable roof, which is about the space a small system needs. We also worked out the share of each roof that was suitable, so large buildings would not look better just for being large.
