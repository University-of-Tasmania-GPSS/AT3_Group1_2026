# Results

## Python model

[One or two sentences on what the map shows.]

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

```{figure} Images/SEBE_vs_Python_graphs.png
:label: fig-Zonal statistics- comparison between our Python and SEBE model following mean, count and sum calculations.
:width: 100%
:alt: 

```

### SEBE vs Python model: West and East (per-roof means, kWh/m²/year)

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

```{figure} Images/Sum zonal comparisons2.png
:label: fig-Zonal statistics- 'sum' applied to Python model
:width: 100%
:alt: 

```
``{figure} Images/Sum zonal comparisonsSEBE.png
:label: fig-Zonal statistics- 'sum' applied to SEBE model
:width: 100%
:alt: 

```

JANE: difference maps and how close the two models were


## Suitable roof area

About two thirds of the roof area in both subsets was suitable for solar panels ([](#fig-suitable)). Almost every building had at least 10 m² of suitable roof.

```{figure} Images/suitable_roof_area.png
:label: fig-suitable
:width: 100%
:alt: Maps of suitable roof area for the Sandy Bay and Howrah subsets

Roof area suitable for solar panels (orange) and not suitable (grey) in the Sandy Bay (left) and Howrah (right) subsets.
```

| | Roof area | Suitable area | Share | Viable buildings |
|---|---|---|---|---|
| Sandy Bay | 76,412 m² | 53,279 m² | 70% | 479 of 484 |
| Howrah | 45,217 m² | 30,504 m² | 67% | 249 of 257 |
