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
  A[Surface model]
  C[Weather file]

  B[Slope and aspect<br>GDAL]
  D[Sun position and hourly sunlight<br>pvlib, typical day each month]
  E[Shadows<br>WhiteboxTools]

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

This model calculates how much sunlight lands on every 0.5 m cell over a year. Adding up three parts: direct sunlight, diffuse sunlight from the sky, and sunlight reflected off the ground.

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

**2. Sun position.** For every hour in the weather file, pvlib works out where the sun is in the sky: its elevation and its azimuth (Anderson et al., 2023). This only needs the latitude, longitude and time zone of the study area.

**3. Typical day.** Running the next steps for all 8,760 hours of the year would take too long. Instead, using the sun's position on the 15th of each month as a typical day for that month.

**4. Shadows.** For each daylight hour of the typical day, WhiteboxTools works out how high the skyline is in the direction of the sun. If the skyline is higher than the sun, the cell is in shadow.

**4. Shadows.** For each daylight hour of the typical day, WhiteboxTools works out the horizon angle of every cell in the direction of the sun. This is how high the skyline looks from that cell, looking towards the sun, if the horizon angle is higher than the sun's elevation, something is blocking the sun and the cell is in shadow ([](#fig-shadow-example)).

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
For each hour, WhiteboxTools works out how high the skyline is from every pixel, looking towards the sun. If the skyline is higher than the sun, that pixel is in shadow.
:::

```{figure} Images/shadows_june.png
:label: fig-shadow-example
:width: 100%
:alt: Shadow masks for Sandy Bay at noon on 15 June.

Shadow masks at noon on the middle of the day on 15th June (middle of winter) Black cells are in shadow. The low winter sun casts much longer shadows.
```

**5. Direct sunlight.** How much direct sunlight a cell gets depends on the angle between the sun and the surface. This is the angle of incidence ($\theta$), worked out from the cell's slope ($\beta$) and aspect ($\gamma$) and the sun's zenith angle ($\theta_z$) and azimuth ($\gamma_s$):

```{math}
:label: eq-incidence

\cos\theta = \cos\beta\cos\theta_z + \sin\beta\sin\theta_z\cos(\gamma_s - \gamma)
```

The direct sunlight on a cell for each hour is then:

```{math}
:label: eq-direct

I_{dir} = DNI \times \cos\theta \times S
```

where $DNI$ is direct normal irradiance from the weather file and $S$ is the shadow mask (0 in shadow, 1 in sun). If $\cos\theta$ is negative the cell faces away from the sun, so it is set to zero. For each hour of the typical day, the DNI for that hour on every day of the month was added up and used in [](#eq-direct). That way the whole month's sunlight is counted, even though the shadows only come from one day.

**6. Diffuse sunlight.** Diffuse sunlight comes from the whole sky rather than straight from the sun. We treated the sky as equally bright in every direction (an isotropic sky). A flat cell sees the whole sky, and a tilted cell sees less of it:

```{math}
:label: eq-diffuse

I_{diff} = DHI \times \frac{1 + \cos\beta}{2}
```

where $DHI$ is diffuse horizontal irradiance (Klein, 1977). Shadows were not applied to diffuse sunlight, which is one of the main differences from SEBE.

**7. Reflected sunlight.** Tilted cells also pick up some sunlight reflected off the ground:

```{math}
:label: eq-reflected

I_{ref} = GHI \times \rho \times \frac{1 - \cos\beta}{2}
```

where $GHI$ is global horizontal irradiance and $\rho$ is the ground albedo. We used 0.15, the same value as SEBE. For flat roofs this term is close to zero.

**8. Yearly total.** The yearly direct sunlight from the loop was added to the yearly diffuse and reflected sunlight, then divided by 1,000 to convert from Wh/m² to kWh/m².

:::{dropdown} Code: yearly total
```python
# Add the three parts together and convert from Wh to kWh
west_yearly = (west_direct + west_diffuse + west_reflected) / 1000
east_yearly = (east_direct + east_diffuse + east_reflected) / 1000

# Check the range of the results
print("West:", round(west_yearly.min()), "to", round(west_yearly.max()))
print("East:", round(east_yearly.min()), "to", round(east_yearly.max()))
```
This adds the direct, diffuse and reflected parts together for every pixel and converts the total from Wh/m² to kWh/m². The print lines check that the results are in a sensible range.
:::

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

A cell receiving plenty of sun is not always somewhere a panel can go. It might be a tree, a wall or the ground. We set three rules, and a cell only counts as suitable if it passes all of them. The rules were applied to the yearly sunlight output from our Python model.

| Rule | Limit | Why |
|---|---|---|
| On a building | Inside a building footprint | Only roofs count, not ground or trees |
| Not a wall | Slope of 65° or less | Steeper cells are walls or roof edges |
| Enough sun | At least 1,139 kWh/m² a year | 80% of the sunlight on an unshaded flat surface. Leaves out shaded and poorly angled roof (Roberts et al, 2018) |

The sunlight limit is 80% of the 1,424 kWh/m² a year that falls on flat open ground in the weather file. This follows Roberts et al. (2018), who used 80% of the sunlight on an unshaded horizontal surface to pick out usable roof for PV.

There is no separate rule for aspect or shading. South-facing and shaded cells get less sun, so they already fail the sunlight rule. Roughness was also left out, because we could not find a limit to cite for it and steep roof edges are already removed by the slope rule. One limitation is that trees hanging over a roof sit inside the building footprint, so they pass the first rule. Most of these should still be removed by the sunlight rule, as the cells under and around them are shaded.

```{mermaid}
flowchart TD
  A[Yearly sunlight<br>Python model] --> D[Suitable cells]
  B[Slope] --> D
  C[Building footprints] --> D
  D --> E[Suitable area per building<br>zonal statistics]
  E --> F[Viable buildings<br>at least 10 m²]
  E --> G[Share of each roof<br>that is suitable]

  classDef input fill:#cfe2f3,stroke:#3d85c6,color:#000
  classDef python fill:#fff2cc,stroke:#bf9000,color:#000
  classDef output fill:#d9ead3,stroke:#38761d,color:#000

  class A,B,C input
  class D,E python
  class F,G output
```

Each rule was turned into a yes/no raster, and a cell was marked suitable only where all three were yes. The slope-corrected area of the suitable cells ([](#eq-area)) was then added up for each building using zonal statistics. A building was counted as viable if it had at least 10 m² of suitable roof. Roberts et al. (2018) used the same minimum, although they applied it to each roof plane rather than each building. We also worked out the share of each roof that was suitable, so large buildings would not look better just for being large.





# N

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

A 1 m digital elevation model was downloaded first, but it couldn't pick up the shape of individual roofs. A LiDAR point cloud was then downloaded from ELVIS and used to build a 0.5 m digital surface model in ArcGIS Pro. A surface model gives the height of whatever is on top, so it includes roofs and trees and not just the ground.


### Building footprints

The study area crosses two council areas, so the Hobart and Clarence building layers were joined into one. The joined layer was reprojected and clipped to the study area. Some of the footprints were small placeholder squares rather than real buildings, so anything under 5 m² was removed.


### Weather data

The weather file is a projected typical year of hourly data from the Hobart Ellerslie Road station. Adding up GHI for the year gave 1,424 kWh/m², and most of that falls in summer ([](#fig-monthly-ghi)). As a check, we compared this with the Global Solar Atlas value for the same location, which was 1,353 kWh/m².

```{figure} Images/monthly_ghi.png
:label: fig-monthly-ghi
:width: 80%
:alt: Bar chart of monthly GHI at Hobart Ellerslie Road

Monthly global horizontal irradiance at Hobart Ellerslie Road.
```


### Subsets

SEBE could not process the full study area at 0.5 m, so we created two 600 m × 600 m subsets out of the prepared data, one in Sandy Bay and one in Howrah. Both models were run on the same two subsets so the results could be compared directly. The Sandy Bay subset has 484 buildings and the Howrah subset has 257.


## The two models

:::::{tab-set}

::::{tab-item} Our Python model

```{mermaid}
flowchart TD
  A[Surface model]
  C[Weather file]

  B[Slope and aspect<br>GDAL]
  D[Sun position and hourly sunlight<br>pvlib, typical day each month]
  E[Shadows<br>WhiteboxTools]

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

This model works out how much sunlight lands on every 0.5 m cell over a year. It adds up three parts: direct sunlight, diffuse sunlight from the sky, and sunlight reflected off the ground.

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

**2. Sun position.** For every hour in the weather file, pvlib works out where the sun is in the sky: its elevation and its azimuth (Anderson et al., 2023). The position was taken at the middle of each hour, since the weather values cover the whole hour. This only needs the latitude, longitude and time zone of the study area.

**3. Typical day.** Running the shadow step for all 8,760 hours of the year would take too long. Instead, we used the sun's position on the 15th of each month as a typical day for that month, which is close to the average days recommended by Klein (1977).

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
For each hour, WhiteboxTools works out how high the skyline is from every pixel, looking towards the sun. If the skyline is higher than the sun, that pixel is in shadow.
:::

```{figure} Images/shadows_june.png
:label: fig-shadow-example
:width: 100%
:alt: Shadow masks at noon on 21 June

Shadow masks at noon on 21 June, the winter solstice, when the sun is at its lowest. Black cells are in shadow. The low winter sun casts long shadows.
```

**5. Direct sunlight.** How much direct sunlight a cell gets depends on the angle between the sun and the surface. This is the angle of incidence ($\theta$), worked out from the cell's slope ($\beta$) and aspect ($\gamma$) and the sun's zenith angle ($\theta_z$) and azimuth ($\gamma_s$) (Duffie & Beckman, 2013):

```{math}
:label: eq-incidence

\cos\theta = \cos\beta\cos\theta_z + \sin\beta\sin\theta_z\cos(\gamma_s - \gamma)
```

The direct sunlight on a cell for each hour is then:

```{math}
:label: eq-direct

I_{dir} = DNI \times \cos\theta \times S
```

where $DNI$ is direct normal irradiance from the weather file and $S$ is the shadow mask (0 in shadow, 1 in sun). If $\cos\theta$ is negative the cell faces away from the sun, so it is set to zero. For each hour of the typical day, the DNI for that hour on every day of the month was added up and used in [](#eq-direct). That way the whole month's sunlight is counted, even though the shadows only come from one day.

**6. Diffuse sunlight.** Diffuse sunlight comes from the whole sky rather than straight from the sun. We treated the sky as equally bright in every direction (an isotropic sky). A flat cell sees the whole sky, and a tilted cell sees less of it:

```{math}
:label: eq-diffuse

I_{diff} = DHI \times \frac{1 + \cos\beta}{2}
```

where $DHI$ is diffuse horizontal irradiance (Klein, 1977). Since diffuse sunlight doesn't depend on where the sun is, the yearly DHI total was used rather than going hour by hour. Shadows were not applied to diffuse sunlight, which is one of the main differences from SEBE.

**7. Reflected sunlight.** Tilted cells also pick up some sunlight reflected off the ground:

```{math}
:label: eq-reflected

I_{ref} = GHI \times \rho \times \frac{1 - \cos\beta}{2}
```

where $GHI$ is global horizontal irradiance and $\rho$ is the ground albedo (Klein, 1977). Like diffuse, this was worked out from the yearly GHI total. We used an albedo of 0.15, the same as the SEBE default. For flat roofs this part is close to zero.

**8. Yearly total.** The yearly direct sunlight from the loop was added to the yearly diffuse and reflected sunlight, then divided by 1,000 to convert from Wh/m² to kWh/m².

:::{dropdown} Code: yearly total
```python
# Add the three parts together and convert from Wh to kWh
west_yearly = (west_direct + west_diffuse + west_reflected) / 1000
east_yearly = (east_direct + east_diffuse + east_reflected) / 1000

# Check the range of the results
print("West:", round(west_yearly.min()), "to", round(west_yearly.max()))
print("East:", round(east_yearly.min()), "to", round(east_yearly.max()))
```
This adds the direct, diffuse and reflected parts together for every pixel and converts the total from Wh/m² to kWh/m². The print lines check that the results are in a sensible range.
:::

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

The weather file was converted to UMEP's text format with the UMEP pre-processing tool. The same tool set was used to make wall height and wall aspect rasters from each subset's surface model, leaving out walls lower than 3 m. SEBE was then run on these layers to estimate solar radiation for a typical year. Our first run had the UTC offset left at 0, which gave values that were far too low, so both subsets were run again with the offset set to +10 for Hobart.

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
| Reflected sunlight | From the ground only | From surrounding surfaces that block the sky |


## How we compared them

Both models produce a final raster of yearly sunlight in kWh/m² for the same 0.5 m cells. The building footprints were used to keep only the cells on roofs, and the SEBE output was subtracted from our model's output to show where the two models differ. Zonal statistics in QGIS were then used to work out the yearly sunlight on each building for both models, so the two could be compared roof by roof.

When working out areas, each cell was counted as 0.25 m² (0.5 m × 0.5 m), which is its area seen from above. A sloped roof actually has more surface area than this, and the UMEP SEBE tutorial corrects for it by dividing each cell's area by the cosine of its slope (UMEP, n.d.). We didn't apply this correction, so roof areas and totals for sloped roofs will be slightly lower than they really are. This affects both models in the same way, so it doesn't change how they compare.


## Suitability criteria

A cell receiving plenty of sun is not always somewhere a panel can go. It might be a tree, a wall or the ground. We set three rules, and a cell only counts as suitable if it passes all of them. The rules were applied to the yearly sunlight output from our Python model.

| Rule | Limit | Why |
|---|---|---|
| On a building | Inside a building footprint | Only roofs count, not ground or trees |
| Not a wall | Slope of 65° or less | Steeper cells are walls or roof edges (UMEP, n.d.) |
| Enough sun | At least 1,139 kWh/m² a year | 80% of the sunlight on an unshaded flat surface. Leaves out shaded and poorly angled roof (Roberts et al., 2018) |

The sunlight limit is 80% of the 1,424 kWh/m² a year that falls on flat open ground in the weather file. This follows Roberts et al. (2018), who used 80% of the sunlight on an unshaded horizontal surface to pick out usable roof for PV.

There is no separate rule for aspect or shading. South-facing and shaded cells get less sun, so they already fail the sunlight rule. Roughness was also left out, because we could not find a limit to cite for it and steep roof edges are already removed by the slope rule. One limitation is that trees hanging over a roof sit inside the building footprint, and their tops are often in full sun, so some tree cells may be counted as suitable roof.

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

Each rule was turned into a yes/no raster, and a cell was marked suitable only where all three were yes. The number of suitable cells was then multiplied by 0.25 m² to give the total suitable roof area in each subset.