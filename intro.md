# Introduction

## Background

People in Hobart often say the Eastern Shore is the sunnier side of the river. Hobart sits at about 43°S, so the sun stays low in the sky through winter, and kunanyi/Mt Wellington rises behind the western suburbs. Both of these change how much sunlight a roof gets, which matters for anyone thinking about putting solar panels on their house.

The increase in uptake of solar panels as a source of renewable energy has increased substantially over the last twenty years, and Tasmania, despite its southern latitude and shorter winter sunlight hours, has followed that trend.

```{figure} Images/tasmania_solar_consumption.png
:label: fig-tasmania_solar_consumption
:width: 80%
:alt: Line chart showing Tasmanian solar energy consumption over time, with a steadily rising trend from the early 2000s to the present. The graph presents annual estimated household solar generation based on average feed in tariffs rather than total electricity consumed, using data from the Australian Bureau of Statistics. 
```

[Australian household solar electricity](https://www.abs.gov.au/articles/household-solar-electricity-generation-australian-national-accounts#insights-into-household-solar-electricity-generation-in-the-australian-economy)


## Project overview

This project estimates how much solar radiation rooftops on the western and eastern shores of Greater Hobart receive over a year, and which roofs are best suited to solar panels. We built our own simplified model in Python and compared it against the SEBE model in UMEP to see where the two agree and where they differ.


## Aim and objectives

The aim of this project was to build a simple rooftop solar model in Python and test how well it matches SEBE, an established solar model, in two parts of Greater Hobart .

1. Model annual solar radiation on rooftops with our own Python model.
2. Run SEBE on the same areas with the same inputs.
3. Compare the two models, and see whether they agree in both areas.


## Study area

We started with one large study area running from Hobart's western suburbs across the River to the Eastern Shore ([](#fig-extent)). The plan was to cover both sides of the river in one go.

At 0.5 m resolution the full area was too large for SEBE to process, so we picked two 600 m × 600 m samples inside it: Sandy Bay on the western shore ([](#fig-west)) and Howrah on the eastern shore ([](#fig-east)). Having one on each side gave us two different settings to test the models in. The main comparison in this project is between the two models, not the two shores.

```{figure} Images/study_extent.png
:label: fig-extent
:width: 90%
:alt: Map of the full study extent across Hobart with the two test areas marked

Original study extent across Hobart, with the Sandy Bay and Howrah test areas outlined.
```

```{figure} Images/west_area.png
:label: fig-west
:width: 80%
:alt: Map of the Sandy Bay test area on the western shore

Western test area, Sandy Bay (600 m × 600 m).
```

```{figure} Images/east_area.png
:label: fig-east
:width: 80%
:alt: Map of the Howrah test area on the eastern shore

Eastern test area, Howrah (600 m × 600 m).
```


## Our approach

We converted LiDAR point clouds into surface models, worked out the slope and aspect of each roof, and modelled the solar radiation reaching them across a year. The same areas were then run through SEBE so the two sets of results could be compared. The full workflow is on the [Methods](methods.md) page.