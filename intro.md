# Introduction

## Background

The uptake of solar panels as a source of renewable energy has increased substantially over the last twenty years, and Tasmania, despite its southern latitude and shorter winter sunlight hours, has followed that trend ([](#fig-tas-solar)).

```{figure} Images/tasmania_solar_consumption.png
:label: fig-tas-solar
:width: 80%
:alt: Bar chart of the annual value of household solar electricity in Tasmania, rising from near zero in 2004-05 to 15.8 million dollars in 2024-25

Annual value of household solar electricity in Tasmania, 2004-05 to 2024-25 ($ million). Source: [ABS, 2025](https://www.abs.gov.au/articles/household-solar-electricity-generation-australian-national-accounts#insights-into-household-solar-electricity-generation-in-the-australian-economy).
```

How much sunlight a roof gets depends on its slope, the direction it faces and what shades it. In Hobart the sun stays low in the sky through winter, so a north-facing roof can accumulate far more than a south-facing one on the same house.

A solar model gives one number for every small patch of roof: the solar radiation that lands on it over a year, measured in kilowatt hours per square metre (kWh/m²). For scale, open flat ground in Hobart gets about 1,400 kWh/m² a year (Global Solar Atlas, 2024). A well-angled, unshaded roof gets more than that, and a shaded or south-facing one gets a lot less. This number is the sunlight arriving at the roof, not the electricity a panel would make.

Established tools such as the Solar Energy on Building Envelopes (SEBE) model in the UMEP plugin for QGIS can estimate this for every pixel of a roof, including shading from nearby buildings and trees (Lindberg et al., 2015; Lindberg et al., 2018). However, these tools can be slow to run over large areas at high resolution and rely on specialist software.

## Motivation

If you know which roofs get the most sun, and which parts of each roof, it's much easier for households to decide if panels are worth it. It also helps councils and network planners get ready for more rooftop solar (Roberts et al., 2018). But for that to work, the model has to be accurate and still be practical to run.

We found this out the hard way. Our original study area was too big for SEBE to process at 0.5 m resolution. That got us wondering if a simpler model we built ourselves in Python could get close enough to SEBE to be useful. Building it from scratch also meant we had to understand every step of how solar radiation is calculated, instead of just trusting a tool we couldn't see inside.



## Project overview

This project compares two ways of modelling how much solar radiation rooftops receive over a year.  We built our own simplified model in Python and compared it against the SEBE model in UMEP to see where the two agree and where they differ. We then used our model to pick out which parts of each roof are suited to solar panels.


## Aim and objectives

The aim of this project was to build a simple rooftop solar model in Python and test how well it matches SEBE, an established solar model, in two parts of Greater Hobart .

1. Model annual solar radiation on rooftops with our own Python model.
2. Run SEBE on the same areas with the same inputs.
3. Compare the two models, and see whether they agree in both areas.


## Study area

We started with one large study area running from Hobart's western suburbs across the River to the Eastern Shore (red outline in [](#fig-extent)). The plan was to cover both sides of the river in one go, however, at 0.5 m resolution this was too large to run through SEBE, and too large to run through our Python model in the detail we wanted.

So we chose two 600 m × 600 m subsets inside it, Sandy Bay on the western shore (yellow) and Howrah on the eastern shore (green). Both subsets were run through both models in exactly the same way. 

Having one on each side of the river means a difference between the two shores might show up in the results, but that is not the focus of this project. The main comparison is between the two models.

```{figure} Images/StudyArea.png
:label: fig-extent
:width: 90%
:alt: Map of Hobart showing the original study area in red, the Sandy Bay subset in yellow and the Howrah subset in green

Original study area (red), with the Sandy Bay subset (yellow) and the Howrah subset (green). Each subset is 600 m × 600 m.
```
