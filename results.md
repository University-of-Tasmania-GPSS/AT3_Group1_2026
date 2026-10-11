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

JANE: SEBE map and summary numbers go here


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
