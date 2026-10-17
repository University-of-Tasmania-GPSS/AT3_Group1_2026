# Results

## Python model

Yearly sunlight on roofs ranged from under 400 kWh/m² on the most shaded cells to about 1,600 kWh/m² on the best ones in both subsets ([](#fig-yearly-sunlight)). The highest values were on roof faces angled towards the north, and the lowest were on south-facing faces and cells shaded by trees or neighbouring buildings. The middle value was higher in Sandy Bay (1,181 kWh/m²) than in Howrah (1,038 kWh/m²). 
For scale, unshaded flat ground gets 1,424 kWh/m² in our weather file.

```{figure} Images/yearly_sunlight_roofs.png
:label: fig-yearly-sunlight
:width: 100%
:alt: Maps of yearly sunlight from our model for the Sandy Bay and Howrah subsets

Yearly sunlight from our Python model for the Sandy Bay (left) and Howrah (right) subsets, in kWh/m².
```

| | Lowest | Middle | Highest |
|---|---|---|---|
| Sandy Bay | 384 | 1,181 | 1,597 |
| Howrah | 385 | 1,038 | 1,596 |

Values are in kWh/m² per year.

## SEBE



### SEBE vs Python model: West and East (per-roof means, kWh/m²/year)

```{figure} Images/SEBE_vs_Python_graphs.png
:label: fig-model-graphs
:width: 100%
:alt: Graphs comparing the Python model and SEBE using the mean, count and sum for each building

Comparison of our Python model and SEBE using zonal statistics, showing the mean, count and sum for each building.
```

| Metric | West | East |
|:--|--:|--:|
| Number of roofs | 581 | 279 |
| SEBE mean | 1,334.4 | 1,253.3 |
| Python mean | 1,459.0 | 1,376.7 |
| SEBE median | 1,351.0 | 1,307.5 |
| Python median | 1,470.2 | 1,414.3 |
| Bias (Python − SEBE) | +124.7 | +123.4 |
| MAE | 124.7 | 123.4 |
| RMSE | 130.9 | 129.3 |
| NMBE (% of SEBE mean) | 9.3% | 9.8% |
| NRMSE (% of SEBE mean) | 9.8% | 10.3% |
| Median % difference | 8.7% | 8.7% |
| Pearson r | 0.956 | 0.981 |
| R² | 0.914 | 0.963 |
| Regression slope | 0.872 | 0.835 |
| Regression intercept | 295.8 | 330.5 |
| Spearman ρ | 0.949 | 0.943 |
| Roofs where Python > SEBE | 581 of 581 | 279 of 279 |
| Wilcoxon p-value | 7.5e-97 | 1.7e-47 |

*Positive bias = Python model higher than SEBE. Values converted from kWh per 0.5 m cell per year (÷ 0.25 m²).*


## Model comparison

To compare the models roof by roof, the yearly sunlight on each building was added up to give one total per roof ([](#fig-zonal-python) and [](#fig-zonal-sebe)). Because these are totals, they depend on the size of the roof as well as how sunny it is. In both models the few large buildings fall in the highest class, and most houses sit in the second and third classes.

The two models gave a very similar pattern. The same buildings come out high and low in each. The SEBE totals were slightly lower than ours. The largest roof in Sandy Bay reached about 1.78 million kWh a year in SEBE and 1.89 million in our model. In Howrah it was about 1.10 million in SEBE and 1.19 million in ours.

```{figure} Images/Sum zonal comparisons2.png
:label: fig-zonal-python
:width: 100%
:alt: Maps of total yearly sunlight per roof from the Python model for the Sandy Bay and Howrah subsets

Total yearly sunlight on each roof from our Python model, for the Sandy Bay (left) and Howrah (right) subsets. Values are the zonal sum for each building in kWh per year, shown in five natural breaks classes.
```

```{figure} Images/Sum zonal comparisonsSEBE.png
:label: fig-zonal-sebe
:width: 100%
:alt: Maps of total yearly sunlight per roof from SEBE for the Sandy Bay and Howrah subsets

Total yearly sunlight on each roof from SEBE, for the Sandy Bay (left) and Howrah (right) subsets. Values are the zonal sum for each building in kWh per year, shown in five natural breaks classes.
```

% JANE: difference maps and how close the two models were


## Suitable roof area

About two thirds of the roof area in both subsets was suitable for solar panels ([](#fig-suitable)). 

The suitable parts are mostly the north-facing side of each roof, with south-facing sides, roof edges and shaded areas left out. Almost every building had at least 10 m² of suitable roof: 479 of 484 in Sandy Bay and 249 of 257 in Howrah. The share of suitable roof was close in the two subsets, at 70% and 67%.

```{figure} Images/suitable_roof_area.png
:label: fig-suitable
:width: 100%
:alt: Maps of suitable roof area for the Sandy Bay and Howrah subsets

Roof area suitable for solar panels (orange) and not suitable (grey) in the Sandy Bay (left) and Howrah (right) subsets.
```


### Comparing the two models

Use the tabs to switch between the two models and the difference between them. The maps are the same size and extent, so they line up when switching.

::::{tab-set}
:::{tab-item} Python model
![Sandy Bay, Python model](Images/west_python.png)
:::
:::{tab-item} SEBE
![Sandy Bay, SEBE](Images/west_sebe.png)
:::
:::{tab-item} Difference
![Sandy Bay, Python model minus SEBE](Images/west_difference.png)
:::
::::

::::{tab-set}
:::{tab-item} Python model
![Howrah, Python model](Images/east_python.png)
:::
:::{tab-item} SEBE
![Howrah, SEBE](Images/east_sebe.png)
:::
:::{tab-item} Difference
![Howrah, Python model minus SEBE](Images/east_difference.png)
:::
::::




# Results

Both models were run on the same 0.5 m surface models and the same weather file for Hobart Ellerslie Road. Use the tabs to step through the results for each test area. The maps are all the same size and extent, so they line up when switching tabs.

## Sandy Bay

::::{tab-set}
:::{tab-item} Yearly sunlight
![Sandy Bay, yearly sunlight](Images/west_yearly.png)
:::
:::{tab-item} Roofs: Python model
![Sandy Bay, Python model on roofs](Images/west_python.png)
:::
:::{tab-item} Roofs: SEBE
![Sandy Bay, SEBE on roofs](Images/west_sebe.png)
:::
:::{tab-item} Difference
![Sandy Bay, Python model minus SEBE](Images/west_difference.png)
:::
:::{tab-item} Suitable roof
![Sandy Bay, suitable roof](Images/west_suitable.png)
:::
::::

## Howrah

::::{tab-set}
:::{tab-item} Yearly sunlight
![Howrah, yearly sunlight](Images/east_yearly.png)
:::
:::{tab-item} Roofs: Python model
![Howrah, Python model on roofs](Images/east_python.png)
:::
:::{tab-item} Roofs: SEBE
![Howrah, SEBE on roofs](Images/east_sebe.png)
:::
:::{tab-item} Difference
![Howrah, Python model minus SEBE](Images/east_difference.png)
:::
:::{tab-item} Suitable roof
![Howrah, suitable roof](Images/east_suitable.png)
:::
::::

## What the maps show

### Yearly sunlight
[2 to 3 sentences: range, flat ground near 1,424, north faces highest, shade and south faces lowest]

### Sunlight on roofs
[Roof cover 21% and 13%, most roofs 1,200 to 1,500, the bushland edge in Howrah]

### Comparison with SEBE

| | Sandy Bay | Howrah |
|---|---|---|
| Python model on roofs (kWh/m²) | 397 – 1,597 | 388 – 1,594 |
| SEBE on roofs (kWh/m²) | 213 – 1,614 | 213 – 1,586 |
| Average difference, Python minus SEBE (kWh/m²) | [ ] | [ ] |

[Where the difference map is red and where it's blue]

### Suitable roof area

| | Sandy Bay | Howrah |
|---|---|---|
| Roof area (m²) | 76,412 | 45,217 |
| Suitable area (m²) | 53,279 | 30,504 |
| Share suitable | 70% | 67% |

[The three rules in one line, linking to the methods page, and what the grey parts are]

## Key results
- [3 or 4 bullet points]