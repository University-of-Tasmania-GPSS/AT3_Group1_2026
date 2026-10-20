# Discussion

**This study compared the results that can be achieved when using a proprietary, QGIS based tool, with a simpler Python based model, built from scratch.**

Our original study area was too large for SEBE to process at 0.5 m resolution. This raised the question of whether a simpler model, written by us in Python, could produce results close enough to SEBE to still be useful. Knowing which roofs, and which parts of a roof, get the most sun helps households decide if panels are worth the cost, and helps councils and network planners prepare for more rooftop solar panels (Roberts et al., 2018). A lighter model that is accurate enough would make that easier to do over bigger areas. Building it from scratch also meant we had to understand each step of the calculation, instead of relying on a tool we couldn't see inside.

There were advantages to running both models. Mistakes in processing were picked up as we worked on our analysis. SEBE’s preprocessing Meteorological Data tool converted our meteorological EPW file, from UTC to local time. Then, before running SEBE on our buildings, we needed to convert back to UTC! Having our Python model and comparing outputs alerted us to the double handling required for our time conversion.

Quicker and faster changes in Python code were possible: The ability to change elements in the analysis, for example changing the true area calculation formula for the roof top area estimation, which was a laborious task in QGIS, would have been a quick fix in Python code.

The more we investigated solar modelling on rooftops in urban areas, the more complicated it became. Urban areas are subject to more reflective radiation variations, dues to the variety of surfaces, many of them highly reflective (Hofierka, 2012), making this a challenging area to quantify.

*Should we talk about sky differences here?*

Getting SEBE working in QGIS was a problem, as it currently has a run problem caused by the way NumPy casts one-dimensional arrays. We may have been better off exploring more recent solar modelling tools like r.sun (GRASS GIS), however this has been designed for terrain (Hofiera, 2002), whereas SEBE is specifically designed with an urban environment in mind. So, we persevered with SEBE and implemented a software patch, enabling it to run, as long as we didn’t update the plugin.

**So why did our model come up with a more optimistic estimate of solar radiation potential?**

Edge effects, complications caused where a pixel straddles a bit of roof and the ground, for example, or where variations occur due to chimneys or other extrusions on a roof’s surface may be responsible for the difference in the two models’ outputs. These are improved with a finer resolution DSM. Weighing the benefits of fine resolution with the higher computational demands to run the software caused us to reduce our study area size, so decisions made in this regard are a balancing act.

Applying a negative buffer (effectively shrinking) a roof edge and thereby avoiding the double surface transition, might have been another avenue to explore, when comparing our Python and SEBE models. Roof edge pixels are the area’s of most uncertainty, where areas are complex and the topology diverse (Polo, 2023)

Running the same analysis on more sample urban areas patches, including areas further up under the lee of Mt Wellington would have expanded our ability to compare the two models.

**This study was a useful first step, exploring the power of Python based code to run a custom-made analysis, that can be build on and further refined and enhanced, depending on the area of investigation and the desired outcomes.**
