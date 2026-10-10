# Introduction

## Background

People in Hobart often say the Eastern Shore is the sunnier side of the river. We wanted to test that with data. Hobart sits at about 43°S, so the sun stays low in the sky through winter, and kunanyi/Mt Wellington rises behind the western suburbs. Both of these change how much sunlight a roof gets, which matters for anyone thinking about putting solar panels on their house. [Add one or two sentences with references on rooftop solar uptake in Tasmania or Australia.]


## Project overview

This project estimates how much solar radiation rooftops on the western and eastern shores of Hobart receive over a year, and which roofs are best suited to solar panels. We built our own simplified model in Python and compared it against the SEBE model in UMEP to see where the two agree and where they differ.


## Aim and objectives

The aim of this project was to assess rooftop solar potential on both sides of the River Derwent using a simple Python model, and to compare the results against an established solar modelling tool.

1. Build a high resolution surface model of each test area from LiDAR data.
2. Model annual solar radiation on rooftops using open-source Python tools.
3. Compare solar potential between the western and eastern shores.
4. Compare our model outputs against SEBE.


## Study area

Our original study area covered a strip of Hobart from the western suburbs across the River Derwent to the Eastern Shore ([](#fig-extent)). At 0.5 m resolution this was too large to process in the time we had, so we picked two smaller test areas inside it, one on each side of the river.

The western test area covers [suburb/s] ([](#fig-west)) and the eastern test area covers [suburb/s] ([](#fig-east)). We chose these because [reason].

```{figure} Images/study_extent.png
:label: fig-extent
:width: 90%
:alt: Map of the full study extent across Hobart with the two test areas marked

Original study extent across Hobart, with the western and eastern test areas outlined.
```

```{figure} Images/west_area.png
:label: fig-west
:width: 80%
:alt: Map of the western shore test area

Western test area, [suburb/s].
```

```{figure} Images/east_area.png
:label: fig-east
:width: 80%
:alt: Map of the eastern shore test area

Eastern test area, [suburb/s].
```


## Our approach

We converted LiDAR point clouds into surface models, worked out the slope and aspect of each roof, and modelled the solar radiation reaching them across a year. The same areas were then run through SEBE so the two sets of results could be compared. The full workflow is on the [Methods](methods.md) page.