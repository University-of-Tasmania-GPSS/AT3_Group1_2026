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

A 1m digital elevation model was first downloaded, but it couldn't identify the shape of individual roofs. A LiDAR point cloud from ELVIS was then loaded and built a 0.5 m digital surface model in ArcGIS Pro.


### Building footprints

The study area crosses two council areas, so the Hobart and Clarence building layers were joined into one. The joined layer was reprojected, and clipped to the study area.


### Weather data

The weather file is a projected typical year of hourly data from the Hobart Ellerslie Road station. Adding up GHI for the year gave 1,424 kWh/m². Most of that falls in summer ([](#fig-monthly-ghi)), which is why roof direction matters so much in Hobart. As a check, this was compared with the Global Solar Atlas value for the same location, which was 1,353 kWh/m².

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

This model calculates the sunlight on every 0.5 m cell over a year. It adds up three parts: direct sunlight, diffuse sunlight from the sky, and sunlight reflected off the ground.

```{mermaid}
flowchart TD
  A[Surface model]
  C[Weather file]

  B[Slope and aspect<br><i>GDAL<i>]
  D[Sun position and hourly sunlight<br><i>pvlib, typical day each month</i>]
  E[Shadows<br><i>WhiteboxTools<i>]

  F[Sunlight on each cell<br>direct + diffuse + reflected]
  G[Yearly total<br>kWh/m²]

  A --> B
  A --> E
  C --> D
  D --> E
  B --> F
  D --> F
  E --> F
  F --> G

  classDef input fill:#cfe2f3,stroke:#3d85c6,color:#000
  classDef python fill:#fff2cc,stroke:#bf9000,color:#000
  classDef output fill:#d9ead3,stroke:#38761d,color:#000

  class A,C input
  class B,D,E,F python
  class G output
```

**1. Surface characteristics.** Slope, aspect and roughness were calculated from the surface model with GDAL, however, roughness didnt provide enough added detail to warrant adding into the model.

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

**2. Sun position.** For every hour in the weather file, pvlib calculates where the sun is in the sky: its elevation and its azimuth (Anderson et al., 2023). The position was taken at the middle of each hour, since the weather values cover the whole hour.

**3. Typical day.** Running the shadow step for all 8,760 hours of the year would take too long. Instead, we used the sun's position on the 15th of each month as a typical day for that month.

**4. Shadows.** For each daylight hour of the typical day, WhiteboxTools works out the horizon angle of every cell in the direction of the sun. This is how high the skyline looks from that cell, looking towards the sun. If the horizon angle is higher than the sun's elevation, something is blocking the sun and the cell is in shadow ([](#fig-shadow-example)).

:::{dropdown} Code: shadow mask
```python
def cast_shadows(dsm_path, altitude_degrees, azimuth_degrees):

    # Work out how high the skyline is for every pixel, looking towards
    # the sun. max_dist is how far it looks in metres, and 600 is the
    # width of our test areas
    skyline_path = outpath / "skyline.tif"
    wbt.horizon_angle(
        str(dsm_path),
        str(skyline_path),
        azimuth=azimuth_degrees,
        max_dist=600,
    )

    # Read the skyline raster in
    skyline_file = rasterio.open(skyline_path)
    skyline = skyline_file.read(1)
    skyline_file.close()

    # A pixel is in shadow if the skyline is higher than the sun
    shadow = skyline > altitude_degrees

    return shadow
```
- **Inputs:** the surface model and one sun position (elevation and compass direction).
- **`wbt.horizon_angle`:** works out the skyline angle for every cell, looking towards the sun up to 600 m away, and saves it as a raster.
- **Read the raster:** loads the skyline angles back in as an array.
- **The comparison:** any cell where the skyline is higher than the sun is marked True, meaning it's in shadow.
- **Output:** the True/False shadow map, used in the next step to switch off direct sunlight for shaded cells.

:::

```{figure} Images/shadows_june.png
:label: fig-shadow-example
:width: 100%
:alt: Shadow masks at noon on 21 June

Shadow masks at noon on 21 June, the winter solstice, when the sun is at its lowest. Black cells are in shadow. The low winter sun casts long shadows.
```

**5. Direct sunlight.** Direct sunlight is the light coming straight from the sun. How much of it a cell gets depends on how directly the cell faces the sun. A roof facing straight at the sun gets the full amount, and the more it's angled away, the less it gets. We worked this out for each hour from the cell's slope and aspect and the sun's position, using the same equation pvlib uses (Anderson et al., 2023; pvlib, n.d.-a). This gives a "facing the sun" value between 0 and 1. Cells facing away from the sun get 0.

The direct sunlight on a cell for each hour is then (pvlib, n.d.-b):

```{math}
:label: eq-direct

\text{Direct} = \text{DNI} \times \text{facing the sun} \times \text{shadow}
```

DNI is the direct sunlight from the weather file, and the shadow mask is 0 if the cell is in shadow and 1 if it's in sun. For each hour of the typical day, we added up the DNI for that hour across every day of the month. That way the whole month's sunlight is counted, even though the shadows only come from one day.

**6. Diffuse sunlight.** Diffuse sunlight is light that has been scattered by clouds and the air, so it comes from the whole sky rather than straight from the sun. We assumed the sky is equally bright in every direction. A flat roof can see the whole sky, so it gets all of the diffuse sunlight. A tilted roof can only see part of the sky, so it gets less (pvlib, n.d.-c):

```{math}
:label: eq-diffuse

\text{Diffuse} = \text{DHI} \times \frac{1 + \cos(\text{slope})}{2}
```

DHI is the diffuse sunlight from the weather file. For a flat roof the fraction is 1, and for a wall it's 0.5. Diffuse sunlight doesn't depend on where the sun is, so we used the yearly DHI total instead of going hour by hour. We didn't apply shadows to diffuse sunlight, and this is one of the main differences from SEBE.

**7. Reflected sunlight.** Tilted roofs also pick up a small amount of sunlight that bounces off the ground (pvlib, n.d.-d):

```{math}
:label: eq-reflected

\text{Reflected} = \text{GHI} \times \text{albedo} \times \frac{1 - \cos(\text{slope})}{2}
```

GHI is the total sunlight on flat ground from the weather file, and albedo is how much of it the ground reflects. We used 0.15, which is the SEBE default. A flat roof can't see the ground, so it gets almost none of this, while steeper roofs get a little more. Like diffuse, we used the yearly GHI total.


**8. Yearly total.** The yearly direct sunlight from the loop was added to the yearly diffuse and reflected sunlight, then divided by 1,000 to convert from Wh/m² to kWh/m².

:::{dropdown} Code: yearly total
```python
# Add the three parts together and convert from Wh to kWh
west_yearly = (west_direct + west_diffuse + west_reflected) / 1000
east_yearly = (east_direct + east_diffuse + east_reflected) / 1000
```
This adds the direct, diffuse and reflected parts together for every pixel and converts the total from Wh/m² to kWh/m².
:::

::::

::::{tab-item} SEBE
SEBE (Solar Energy on Building Envelopes) is a solar model in the UMEP plugin for QGIS (Lindberg et al., 2015). It uses the surface model and a weather file to work out how much sunlight reaches each cell over a year, taking into account shading from nearby buildings and trees.

```{mermaid}
flowchart TD
  A["`**Digital Surface model**`"] --> B[UMEP Preprocessing tool<br> for Wall height and aspect]
  C[Weather file<br>EPW] --> D[UMEP Metdata processor]
  E[Wall height raster]
  F[Wall aspect raster]
  G[SEBE]
  H["`**Annual radiation raster**`"]
  B --> E
  B --> F
  E --> G
  F --> G
  D --> G
  G --> H

  classDef input fill:#cfe2f3,stroke:#3d85c6,color:#000
  classDef python fill:#fff2cc,stroke:#bf9000,color:#000
  classDef output fill:#d9ead3,stroke:#38761d,color:#000
  class A,C input
  class B,D,E,F,H python
  class H output
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

Both models produce a final raster of yearly sunlight in kWh/m² for the same 0.5 m cells. The building footprints were used to keep only the cells on roofs, and the SEBE output was subtracted from our model's output to show where the two models differ. Zonal statistics in QGIS were then used to work out the yearly sunlight on each building for both models, so the two could be compared roof by roof.


## Suitability criteria

A cell receiving plenty of sun is not always somewhere a panel can go. It might be a tree, a wall or the ground, so to identify these uitable region three rules were selected and a cell only counts as suitable if it passes all of them. The rules were applied to the yearly sunlight output from our Python model.

| Rule | Limit | Why |
|----|---|---|
| On a building | Inside a building footprint | Only roofs count, not ground or trees |
| Not a wall | Slope of 65° or less | Steeper cells are walls or roof edges (UMEP, n.d.) |
| Enough sun | At least 1,139 kWh/m² a year | 80% of the sunlight on an unshaded flat surface. Leaves out shaded and poorly angled roof (Roberts et al., 2018) |

The sunlight limit is 80% of the 1,424 kWh/m² a year that falls on flat open ground in the weather file. This follows Roberts et al. (2018), who used 80% of the sunlight on an unshaded horizontal surface to pick out usable roof for PV.

There is no separate rule for aspect or shading. South-facing and shaded cells get less sun, so they already fail the sunlight rule. Roughness was also left out, because we could not find a limit to cite for it and steep roof edges are already removed by the slope rule. One limitation is that trees hanging over a roof sit inside the building footprint, and their tops are often in full sun, so some tree cells may be counted as suitable roof. Each rule was turned into a yes/no raster, and a cell was marked suitable only where all three were yes.

```{mermaid}
flowchart TD
  A[Yearly sunlight<br>Python model] --> D[Suitable cells]
  B[Slope] --> D
  C[Building footprints] --> D
  D --> E[Suitable roof area<br>for each subset]

  classDef input fill:#cfe2f3,stroke:#3d85c6,color:#000
  classDef python fill:#fff2cc,stroke:#bf9000,color:#000
  classDef output fill:#d9ead3,stroke:#38761d,color:#000

  class A,B,C input
  class D python
  class E output
```