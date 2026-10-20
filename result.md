# Results

Both models were run on the same 0.5 m surface models and the same weather file for Hobart Ellerslie Road, so any difference between them comes from the models and not the data. Use the tabs to step through the results for each subset.

The results follow the project objectives: yearly sunlight from our model (Objective 1), SEBE (Objective 2), the comparison between them (Objective 3) and suitable roof area (Objective 4).

## Sandy Bay

::::{tab-set}
:::{tab-item} Yearly Sunlight
![Sandy Bay, yearly sunlight from the Python model](Images/west_yearly.png)

Yearly sunlight across the Sandy Bay subset ranged from about 384 to 1,597 kWh/m². Roads and other flat open areas received close to the 1,424 kWh/m² measured on flat ground in the weather file. There is no large area of bush, but trees are spread through backyards and along streets, so there are small shaded patches across the whole subset. North-facing roof faces are the brightest surfaces.
:::
:::{tab-item} Roofs: Python model
![Sandy Bay, Python model on roofs](Images/west_python.png)

Most roofs receive between 1,200 and 1,500 kWh/m² a year, the houses are small and close together, so a lot of roof area is affected by roof edges, south-facing faces and shading from neighbouring buildings and trees. 

:::
:::{tab-item} Roofs: SEBE
![Sandy Bay, SEBE on roofs](Images/west_sebe.png)

SEBE shows the same pattern as our model, with bright north-facing faces and darker south-facing faces and roof edges. The values are slightly lower across the subset.

:::
:::{tab-item} Difference
![Sandy Bay, Python model minus SEBE](Images/west_difference.png)

The Python model was higher than SEBE on almost every roof cell, by an average of 100 kWh/m² (8.7%). Most roofs were between 100 and 150 kWh/m² higher. The smallest differences were on large, open roofs with little shading, and the largest were on small buildings tightly packed between others.

:::
:::{tab-item} Suitable roof
![Sandy Bay, suitable roof](Images/west_suitable.png)

53,279 m² of the 76,412 m² of roof in Sandy Bay was suitable (70%). Non suitable roofs are spread across the subset in small patches, mostly on south-facing faces, roof edges and parts of roofs shaded by trees or neighbouring houses. Large flat roofs were almost entirely suitable.

:::
:::::

Sandy Bay has dense housing, and roofs cover 21% of the subset. Shading comes from neighbouring houses and backyard trees and is spread across nearly every block, so the difference between the two models is fairly even across the subset.


## Howrah

::::{tab-set}
:::{tab-item} Yearly Sunlight
![Howrah, yearly sunlight from the Python model](Images/east_yearly.png)

Yearly sunlight across the Howrah subset ranged from about 385 to 1,596 kWh/m², almost the same range as Sandy Bay. Roads and open areas again received the most and the bushland in the east of the subset is much darker, because the surface model treats the tree canopy as a rough surface that shades itself.

:::
:::{tab-item} Roofs: Python model
![Howrah, Python model on roofs](Images/east_python.png)

Most roofs receive between 1,200 and 1,500 kWh/m² a year, similar to Sandy Bay, roof direction makes the biggest difference within each building. The lowest values are grouped on the buildings along the edge of the bushland in the north-east, where trees shade the roofs for most of the day.

:::
:::{tab-item} Roofs: SEBE
![Howrah, SEBE on roofs](Images/east_sebe.png)

SEBE again shows the same overall pattern as our model, with the roofs near the bushland receiving the least amount of potential, with the SEBE roofs are slightly darker across the whole subset.

:::
:::{tab-item} Difference
![Howrah, Python model minus SEBE](Images/east_difference.png)

The python model was higher than SEBE on almost every roof cell, by an average of 105 kWh/m² (9.5%). Most roofs were between 50 and 150 kWh/m² higher, but the roofs along the bushland edge were up to 200 kWh/m² or more higher. Those few heavily shaded roofs are why the average gap is slightly bigger here than in Sandy Bay.

:::
:::{tab-item} Suitable roof
![Howrah, suitable roof](Images/east_suitable.png)

30,504 m² of the 45,217 m² of roof in Howrah was suitable (67%). The roof that failed was mostly on south-facing faces and on whole buildings heavily shaded next to the trees.

:::
::::

Howrah has fewer buildings, with roofs covering 13% of the subset, and bushland along its eastern side. That bushland is the main reason sunlight is lower overall in Howrah, and the roofs along its edge are the most shaded in the subset. These are also the roofs where our model is furthest above SEBE.


## Yearly sunlight from the Python model

Across each full subset, yearly sunlight ranged from under 400 kWh/m² on cells that never get direct sun to about 1,600 kWh/m² on the best-placed surfaces. The median was higher in Sandy Bay than in Howrah, mostly because of the bushland on the eastern side of the Howrah subset.

| | Lowest | Middle | Highest |
|---|---|---|---|
| Sandy Bay | 384 | 1,181 | 1,597 |
| Howrah | 385 | 1,038 | 1,596 |

*Values are in kWh/m² per year, for every cell in the subset.*


Looking only at roofs, both models reach almost the same highest values, but SEBE goes much lower on the most shaded roof cells.

| | Lowest | Highest |
|---|---|---|
| Sandy Bay: Python model | 397 | 1,597 |
| Sandy Bay: SEBE | 213 | 1,614 |
| Howrah: Python model | 388 | 1,594 |
| Howrah: SEBE | 213 | 1,586 |

*Values are in kWh/m² per year, for roof cells only.*



## Comparison with SEBE

### Cell by cell

Our model was higher than SEBE on almost every roof cell, by an average of 100 kWh/m² (8.7%) in Sandy Bay and 105 kWh/m² (9.5%) in Howrah. For 95% of roof cells, our model was between 0 and ~230 kWh/m² higher in Sandy Bay, and between about −6 and 260 kWh/m² higher in Howrah. On the difference maps, white shows where the models nearly agree and dark red shows where our model is well above SEBE. There is almost no blue on either map, meaning there are very few places where SEBE is higher.

### Roof by roof

To compare whole roofs, the average yearly sunlight on each building was found for both models using zonal statistics in QGIS ([](#fig-model-graphs)).


```{figure} Images/SEBE_vs_Python_scatter.png
:label: fig-model-graphs
:width: 100%
:alt: Graphs comparing the Python model and SEBE for each building

Comparison of our Python model and SEBE for each building, using zonal statistics.
```

| | Sandy Bay | Howrah |
|---|---|---|
| SEBE mean (kWh/m²) | 1,334 | 1,253 |
| Python model mean (kWh/m²) | 1,459 | 1,377 |
| Average difference, Python minus SEBE (kWh/m²) | +125 | +123 |
| Average difference as % of SEBE | 9.3% | 9.8% |
| RMSE (kWh/m²) | 131 | 129 |
| Correlation (R²) | 0.91 | 0.96 |
| Roofs where Python model is higher | all | all |

Our model was higher than SEBE on every roof, by about 125 kWh/m² (around 9 to 10%) in both subsets. Even so, the two models were very closely related. The R² values of 0.91 in Sandy Bay and 0.96 in Howrah mean most of the variation between roofs in SEBE is matched by our model. The Spearman values of 0.95 and 0.94 show the two models ranked the roofs in almost the same order, so the roofs that come out best in one model also come out best in the other.

The regression slopes of 0.87 and 0.84 show that the gap is not the same for every roof. It is largest on roofs that get less sun and smallest on the sunniest roofs. For a roof that gets 1,000 kWh/m² in SEBE, our model gives about 170 kWh/m² more, but for a roof that gets 1,500 kWh/m² the gap drops to about 85 to 105 kWh/m². This fits with our model not shading diffuse sunlight, which matters most on roofs that are already shaded.

:::{dropdown} Full comparison statistics
| Metric | Sandy Bay | Howrah |
|:--|--:|--:|
| SEBE median | 1,351.0 | 1,307.5 |
| Python median | 1,470.2 | 1,414.3 |
| MAE | 124.7 | 123.4 |
| NRMSE (% of SEBE mean) | 9.8% | 10.3% |
| Median % difference | 8.7% | 8.7% |
| Pearson r | 0.956 | 0.981 |
| Regression slope | 0.872 | 0.835 |
| Regression intercept | 295.8 | 330.5 |
| Spearman ρ | 0.949 | 0.943 |
| Wilcoxon p-value | 7.5e-97 | 1.7e-47 |

*Positive values mean our model is higher than SEBE.*
:::

The maps below show the average yearly sunlight on each building.

::::{tab-set}
:::{tab-item} Python model
![Mean yearly sunlight per roof from the Python model](Images/MeanzonalcomparisonsPython.png)
:::
:::{tab-item} SEBE
![Mean yearly sunlight per roof from SEBE](Images/MeanzonalcomparisonsSEBE.png)
:::
::::

### Total sunlight per roof

Adding up the sunlight on each building gives one total per roof. These totals depend on the size of the roof as well as how sunny it is, so the few large buildings fall in the highest class and most houses sit in the middle classes. The two models gave a very similar pattern, with SEBE slightly lower. [The largest roof in Sandy Bay reached about 1.78 million kWh a year in SEBE and 1.89 million in our model. In Howrah it was about 1.10 million in SEBE and 1.19 million in ours.]

::::{tab-set}
:::{tab-item} Python model
![Total yearly sunlight per roof from the Python model](Images/SumzonalcomparisonsPythonWhiteB.png)
:::
:::{tab-item} SEBE
![Total yearly sunlight per roof from SEBE](Images/SumzonalcomparisonsSEBEwhite.png)
:::
::::

Values are the total yearly sunlight on each building in kWh per year, shown in five natural breaks classes.


## Suitable roof area

Approximately two thirds of the roof area was suitable in both subsets (see [](#tbl-compare) below). The parts that failed were mostly south-facing roof faces, roof edges and shaded areas. In Howrah, whole buildings along the bushland edge failed, while in Sandy Bay the unsuitable roof was spread in small patches across most buildings.


## Comparing Sandy Bay and Howrah

:::{table} Summary of results for the two subsets. Mean values are for roof cells, cell by cell.
:label: tbl-compare

| | Sandy Bay | Howrah |
|---|---|---|
| Buildings | 484 | 257 |
| Roof cover | 21% | 13% |
| Total roof area (m²) | 76,412 | 45,217 |
| Main source of shading | Neighbouring houses and backyard trees | Bushland on the north-east edge |
| Mean roof sunlight, Python (kWh/m²) | 1,252 | 1,219 |
| Mean roof sunlight, SEBE (kWh/m²) | 1,151 | 1,114 |
| Mean difference (kWh/m²) | 100 (8.7%) | 105 (9.5%) |
| Suitable roof area (m²) | 53,279 (70%) | 30,504 (67%) |
:::

Sandy Bay has almost twice as many buildings as Howrah and about 70% more roof area. Even so, the results for the two subsets were very similar. Mean roof sunlight was only about 30 kWh/m² higher in Sandy Bay, the gap between the models was close to 100 kWh/m² in both, and the number of suitable roofs differed by only 3%.

## Key results

- **Objective 1:** Yearly sunlight on roofs from our model ranged from about 400 to 1,600 kWh/m², with north-facing, unshaded roof faces getting the most.
- **Objective 2:** SEBE illustrated the same pattern, but calculated much lower results on the most shaded roofs (down to 213 kWh/m²).
- **Objective 3:** Our model was higher than SEBE on almost every roof, by about 100 kWh/m² cell by cell and 125 kWh/m² roof by roof (9 to 10%). The gap was largest on shaded roofs and smallest on the sunniest ones, however, the two models were very similar.
- **Objective 4:** About two thirds of the roof area was suitable for solar panels, 70% in Sandy Bay and 67% in Howrah.