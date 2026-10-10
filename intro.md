# Introduction

The aim of this study was to look at roof top solar potential for renewable energy generation, with both simple modelling techniques, using Python commands and the modern geospatial stack (Make sure you can explain what this is Jane!) and to compare this to a proprietary solar modelling application "Insert our model here".
We focussed on an area in Hobart, either side of the Derwent River, the Eastern and Western shores.

:::{figure} C:\Users\jesta\KGG375_AT3\AT3_Group1_2026\Images\TESTScreenshot 2026-10-09 135024.png
:label: fig:my-photo
:alt: A test screenshot, to see how to insert an image

A test screenshot, to see how to insert an image.
:::

```{figure} Images/LailaTesr.jpeg
:label: fig-study-area
:width: 80%
:alt: Map of the study area in Hobart

Study area showing the western and eastern shore test areas.
```

I am a book about ... something! Wikipedia has [information about books](wiki:book): hover over the link for more information.

% An admonition containing a note
:::{note}
Books are usually written on paper ... But Jupyter Book can create _websites_!
:::

If you sold 100 books at \$10 per book, you'd have \$1000 dollars according to [](#eq:book). If instead you publish your Jupyter Book to the web for free, you'd have \$0 dollars!

% An arbitrary math equation
:::{math}
:name: eq:book

x \times y = z
:::

Sometimes when reading it is helpful to foster a _tranquil_ environment. The image in [](#fig:mountains) would be a perfect spot!

% A figure of a photograph of some mountains, followed by a caption
:::{figure} https://github.com/rowanc1/pics/blob/main/mountains.png?raw=true
:label: fig:mountains

A photograph of some beautiful mountains to look at whilst reading.
:::



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

We focused on two test areas in Hobart, one on each side of the River Derwent. The western area covers [suburb/s] ([](#fig-west)) and the eastern area covers [suburb/s] ([](#fig-east)). We chose these because [reason, e.g. similar housing density but different terrain and aspect].

```{figure} Images/west_area.png
:label: fig-west
:width: 80%
:alt: Map of the western shore test area

Western shore test area, [suburb/s].
```

```{figure} Images/east_area.png
:label: fig-east
:width: 80%
:alt: Map of the eastern shore test area

Eastern shore test area, [suburb/s].
```

## Our approach

We converted LiDAR point clouds into surface models, worked out the slope and aspect of each roof, and modelled the solar radiation reaching them across a year. The same areas were then run through SEBE so the two sets of results could be compared. The full workflow is on the [Methods](methods.md) page.