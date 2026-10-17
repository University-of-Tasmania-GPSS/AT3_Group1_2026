# **KGG375 Geospatial Data Analytics, AT3 Group Project (Group1)**

## Group members
Laila McLennan and Jane Stanton

## Rooftop Solar Modelling in Hobart
Can a simple Python model match an established solar model?

## Published site
**Published Jupyter Book:** [Rooftop Solar Modelling in Hobart](https://university-of-tasmania-gpss.github.io/AT3_Group1_2026/)

## Project Overview
This porject estimates how much solar radiation rooftops between Hobart and the Eastern Shore, recieves over a year and which roofs are best suited to solar panels. 
We built our own simplified model in Python and compared it against the SEBE UMEP plugin in QGIS to explore the differences between the two.

## Objectives
The aim of this project was to build a simple rooftop solar model in Python and test how well it matches SEBE, an established solar model, in two parts of Greater Hobart .

1. Model annual solar radiation on rooftops with our own Python model.
2. Run SEBE on the same areas with the same inputs.
3. Compare the two models, and see whether they agree in both areas.
4. Set criteria to identify roof areas suitable for solar panels.

## Repository structure
```
AT3_Group1_2026/
├── README.md                    # This file
├── LICENSE
├── environment.yml              # Conda environment with all the libraries used
├── myst.yml                     # Jupyter Book settings and page order
├── .gitignore
├── .github/
│   └── workflows/               # Builds and publishes the book when changes are pushed
├── Images/                      # Figures and maps used on the website
├── 01Data_Preparation.ipynb     # Reads in and checks the data
├── 02Python_Model.ipynb         # Our solar model for both subsets
├── 03Comparison.ipynb           # Compares our model with SEBE and applies the suitability rules
├── index.md                     # Cover page
├── intro.md                     # Introduction
├── methods.md                   # Methods
├── results.md                   # Results
├── discussion.md                # Discussion
└── conclusion.md                # Conclusion
```

## How to reproduce

### 1. Set up the environment
All the Python libraries we used are listed in `environment.yml`. With conda installed, open a terminal in the repo folder and run:

```bash
conda env create -f environment.yml
conda activate KGG375_AT3
```

This creates an environment called `KGG375_AT3` with Python and every library the notebooks need.

| Library | Used for |
|---|---|
| pandas | Joining the building footprint layers |
| geopandas | Reading, reprojecting and clipping vector data |
| rasterio | Reading and writing rasters, making the roof mask |
| numpy | Raster calculations |
| matplotlib | Charts and maps |
| pvlib | Reading the weather file and calculating sun position |
| GDAL | Slope, aspect and roughness |
| whitebox | Shadow casting with the horizon angle tool |

### 2. Acquire data
The data files need to be downloaded and put in an `Inputs` folder. The notebooks uses this structure:

```
Inputs/
├── BoundingBox/
│   └── BoundingBox.shp
├── Buildings/
│   ├── buildings_hobart.shp
│   └── buildings_clarence.shp
├── DSM/
│   └── DSM_05mv2.tif
├── Ellerslie_Road_TMY/
│   └── AUS_TAS_Hobart.Ellerslie.Road.949700_TMYx.epw
└── LTAy_AvgDailyTotals/
    └── GHI.tif
```

| Data | Source | Notes |
|---|---|---|
| LiDAR point cloud | [ELVIS](https://elevation.fsdf.org.au/), Geoscience Australia | Download the point cloud tiles covering the study area |
| Building footprints | [LISTdata](https://listdata.thelist.tas.gov.au/opendata/), 2D Building Polygons | Hobart and Clarence council layers |
| Weather file | [Climate.OneBuilding.Org](https://climate.onebuilding.org/) | TMYx file for Hobart Ellerslie Road |
| Reference GHI | [Global Solar Atlas](https://globalsolaratlas.info/download/australia) | Long-term average GHI raster, only used as a check |


**Making the surface model.** The LiDAR was turned into a 0.5 m digital surface model in ArcGIS Pro:

1. Convert LAS, to unzip the `.laz` tiles
2. Create LAS Dataset from the tiles
3. Filter to first returns and remove noise points
4. LAS Dataset To Raster: binning, maximum cell value, linear void fill, 0.5 m cell size
5. Clip to the bounding box and export as a GeoTIFF
   

### 3. Run notebooks
Run the notebooks in this order. Each one saves outputs that the next one reads in.

| Notebook | What it does |
|---|---|
| `01Data_Preparation.ipynb` | Reads in and checks the data, joins and clips the buildings, checks the weather file, profects everything to project CRS|
| `02Python_Model.ipynb` | Slope, aspect and roughness, sun position, shadows and the yearly sunlight for both subsets |
| `03Comparison.ipynb` | Reads in SEBE, clips both models to the roofs and compares them and applies the suitability rules to our model's output |

**SEBE** was run separately in QGIS with the UMEP plugin. The SEBE outputs go in the `Outputs` folder before running the comparison notebook.

## AI usage and References
Many of the documentation pages and worked examples used to build the code are linked directly in the notebooks, next to the step they were used for.

- Klein, S. A. (1977). Calculation of monthly average insolation on tilted surfaces. *Solar Energy, 19*(4), 325–329. https://doi.org/10.1016/0038-092X(77)90001-9
- Liu, B. Y. H., & Jordan, R. C. (1960). The interrelationship and characteristic distribution of direct, diffuse and total solar radiation. *Solar Energy, 4*(3), 1–19. https://doi.org/10.1016/0038-092X(60)90062-1
- Roberts, M. B., Copper, J., & Bruce, A. (2018). *Analysis of rooftop solar potential on Australian residential buildings* [Conference paper]. Asia Pacific Solar Research Conference, Sydney, Australia. https://energyconsumersaustralia.com.au/sites/default/files/2025-05/apsrc-paper-rooftop-solar-potential-2018.pdf
- UMEP. (n.d.). *Solar energy – Introduction to SEBE* [Tutorial]. https://umep-docs.readthedocs.io/projects/tutorial/en/latest/Tutorials/SEBE.html
  
- Anderson, K. S., Hansen, C. W., Holmgren, W. F., Jensen, A. R., Mikofski, M. A., & Driesse, A. (2023). pvlib python: 2023 project update. *Journal of Open Source Software, 8*(92), 5994. https://doi.org/10.21105/joss.05994
- Jensen, A. R., Anderson, K. S., Holmgren, W. F., Mikofski, M. A., Hansen, C. W., Boeman, L. J., & Loonen, R. (2023). pvlib iotools – Open-source Python functions for seamless access to solar irradiance data. *Solar Energy, 266*, 112092. https://doi.org/10.1016/j.solener.2023.112092
- Lindsay, J. B. (2016). Whitebox GAT: A case study in geomorphometric analysis. *Computers & Geosciences, 95*, 75–84. https://doi.org/10.1016/j.cageo.2016.07.003
-  Ilba, M. (2025). SolarQGIS: A QGIS application for calculating solar radiation on 3D vector GIS data. SoftwareX, 31, 102230. https://doi.org/10.1016/j.softx.2025.102230 Kodysh, J. B., Omitaomu, O.
-  A., Bhaduri, B. L., & Neish, B. S. (2013). Methodology for estimating solar potential on multiple building rooftops for photovoltaic systems. Sustainable Cities and Society, 8, 31–41. https://doi.org/10.1016/j.scs.2013.01.002


### AI Usage
Claude.ai was used during this project to:
- Plan the workflow and work out which specific datasets we needed
- Troubleshoot problems, including:
  - identifying bugs and errors in our code
  - fixing errors with SEBE model in QGIS
- Help adapt example code from other sources to fit our data and model
- Tidy code to follow PEP 8
- Draft and edit the markdown explanations in the notebooks