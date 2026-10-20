# Introduction

## Background

Rooftop solar has grown rapidly in Australia over the last twenty years. By 2018 there were close to two million households with solar panels (Roberts et al., 2018). Tasmania has followed the same trend, even though it sits far south and gets shorter days in winter. The value of electricity made by household solar in the state has gone from almost nothing in 2004-05 to about $15.8 million in 2024-25 ([](#fig-tas-solar)).

```{figure} Images/tasmania_solar_consumption.png
:label: fig-tas-solar
:width: 80%
:alt: Bar chart of the annual value of household solar electricity in Tasmania, rising from near zero in 2004-05 to 15.8 million dollars in 2024-25

Annual value of household solar electricity in Tasmania, 2004-05 to 2024-25 ($ million) (ABS, 2025).
```

Not every roof is worth putting panels on though. The amount of sunlight a roof recieves depends on its slope, the direction it faces and whether nearby buildings or trees shade it (Klein, 1977). Hobart's latitude makes this more noticeable, with the sun staying lower in the sky through winter, so a north-facing roof can collect much more sunlight than the south-facing side of the same house.

Solar models measure this as the radiation landing on each small area of roof over a year, in kilowatt hours per square metre (kWh/m²). For comparison, open flat ground in Hobart receives about 1,400 kWh/m² a year (Global Solar Atlas, 2024). A well-angled roof with no shade gets more than this, and a shaded or south-facing roof gets a lot less. It's worth noting this is the sunlight reaching the roof, not the electricity a panel would actually produce.

Modelling solar potential roof by roof using LiDAR and GIS has been done for over a decade (Kodysh et al., 2013). One of the more widely used tools is SEBE (Solar Energy on Building Envelopes), which runs in QGIS through the UMEP plugin. It uses a digital surface model (DSM) and the position of the sun to work out which pixels are sunlit or shaded across the year (Lindberg et al., 2018). The catch is that tools like this can be limited by resolution and are computationally heavy (Ilba, 2025), so they become slower over large areas.


## Project overview

This project compares two ways of modelling how much solar radiation rooftops receive over a year.  We built our own simplified model in Python and compared it against the SEBE model in UMEP to see where the two agree and where they differ. Then using that model to pick out which parts of each roof are suited to solar panels.


## Aim and objectives

The aim of this project was to build a simple rooftop solar model in Python and test how well it matches SEBE, an established solar model, in two parts of Greater Hobart .

1. Model annual solar radiation on rooftops with our own Python model.
2. Run SEBE on the same areas with the same inputs.
3. Compare the two models, and see whether they agree in both areas.
4. Use our model to identify locations on each roof are best suited to solar panels.


## Study area

Initially, one large study area running from Hobart's western suburbs across the River to the Eastern Shore (red outline in [](#fig-extent)) was selected. The plan was to cover both sides of the river in one go, however, at 0.5 m resolution this was too large to run through SEBE, and too large to run through our Python model in the detail we needed.

To rectify this two  600 m × 600 m subsets inside it were chosen, Sandy Bay on the western shore (yellow) and Howrah on the eastern shore (green). Both subsets were run through both models in exactly the same way. 

```{figure} Images/StudyArea.png
:label: fig-extent
:width: 90%
:alt: Map of Hobart showing the original study area in red, the Sandy Bay subset in yellow and the Howrah subset in green

Original study area (red), with the Sandy Bay subset (yellow) and the Howrah subset (green). Each subset is 600 m × 600 m.
```

Having one on each side of the river means a difference between the two shores might show up in the results, but that is not the focus of this project. The main comparison is between the two models.