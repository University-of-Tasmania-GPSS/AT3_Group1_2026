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
