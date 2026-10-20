# Discussion

```{figure} Images/solarroof6.jpg
:label: fig-solar-roof
:width: 100%
:alt: 
```

**This study of rooftop solar potential, compared the results that can be achieved when using a proprietary, QGIS based tool, with a simpler Python based model, built from scratch.**

Our original study area was too large for SEBE to process at 0.5 m resolution. This raised the question of whether a simpler model, written by us in Python, could produce results close enough to SEBE to still be useful. Knowing which roofs, and which parts of a roof, get the most sun helps households decide if panels are worth the cost, and helps councils and network planners prepare for more rooftop solar panels (Roberts et al., 2018). A lighter model that is accurate enough would make that easier to do over bigger areas. Building it from scratch also meant we had to understand each step of the calculation, instead of relying on a tool we couldn't see inside.

There were advantages to running both models. Mistakes in processing were picked up as we worked on our analysis. SEBE’s preprocessing Meteorological Data tool converted our meteorological EPW file, from UTC to local time. Then, before running SEBE on our buildings, we needed to convert back to UTC! Having our Python model and comparing outputs alerted us to the double handling required for our time conversion.

Quicker and faster changes in Python code were possible. The ability to change elements in the analysis, for example, changing the true area calculation formula for roof top area estimation, which was a laborious task in QGIS, would have been a quick fix in Python code.

Getting SEBE working in QGIS at first was a problem, as it currently has a run problem caused by the way NumPy casts one-dimensional arrays. We may have been better off exploring more recent solar modelling tools like r.sun (GRASS GIS), however this has been designed for terrain (Hofiera, 2002), whereas SEBE is specifically designed with an urban environment in mind. So, we persevered with SEBE and implemented a software patch, enabling it to run, as long as we didn’t update the plugin.

```{figure} Images/solarroof3.jpg
:label: fig-solar-roof

:width: 80%
:alt: 
```

**Why did our model come up with a more optimistic estimate of solar radiation potential?**

The more we investigated solar modelling on rooftops in urban areas, the more complicated it became. Urban areas are subject to more reflective radiation variations, dues to the variety of surfaces, many of them highly reflective (Hofierka, 2012), making this a challenging area to quantify.
While our model accounted for reflectance from the ground, wall reflectance was excluded. This might account for some of the differences seen in the two models.

The effect of vegetaion shading on rooftops is another compenent that is hard to meassure, and subject to alot of variation due to growth, wind, sun angle and the type of solar installations used (considering string layouts and microinverters, for example).

The sky difference in the sky models used for our analysis, might also have lead to the optimistic results we got back from our Python model. In our model the sky was considered to be equally bright everywhere. SEBE considers a 145 patch sky model based upon Tragenza sky matrices (Lindberg, 2015).

Edge effects, complications caused where a pixel straddles a bit of roof and the ground, for example, or where variations occur due to chimneys or other extrusions on a roof’s surface may be responsible for the difference in the two models’ outputs. These are improved with a finer resolution DSM. Weighing the benefits of fine resolution with the higher computational demands to run the software caused us to reduce our study area size, so decisions made in this regard are a balancing act.

Applying a negative buffer (effectively shrinking) a roof edge and thereby avoiding the double surface transition, might have been another avenue to explore, when comparing our Python and SEBE models. Roof edge pixels are the areas of most uncertainty, where areas are complex and the topology diverse (Polo, 2023)

Running the same analysis on more sample urban areas patches, including areas further up under the lee of Mt Wellington would have expanded our ability to compare the two models.

Our secondary question, of relevance to all Hobartian’s, **“Is it sunnier on the Eastern or Western shore?”**
If anything our results suggested that our Western patch is sunnier, however, without further study of microclimates and weather patterns of difference between the two areas, and further comparison between aligning areas, it’s an impossible question to answer.
