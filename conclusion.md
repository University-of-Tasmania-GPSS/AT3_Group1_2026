# Conclusion



DONT KNOW WHERE to put yet. 






## Suitability criteria

A cell getting plenty of sun is not always somewhere a panel can go. It might be a tree, a wall or the ground. We set three rules, and a cell only counts as suitable if it passes all of them.

| Rule | Limit | Why | Source |
|---|---|---|---|
| On a building | Inside a building footprint | Only roofs count, not ground or trees | LIST building footprints |
| Not a wall | Slope of 65° or less | Steeper cells are walls or roof edges | UMEP SEBE tutorial |
| Enough sun | At least 80% of the yearly sunlight on unshaded flat surface | Leaves out shaded and poorly angled roof | Roberts, Copper and Bruce (2018) |

There is no separate rule for aspect or shading. South-facing and shaded cells get less sun, so they already fail the sunlight rule. Roughness was also left out, because we could not find a limit to cite for it and steep roof edges are already removed by the slope rule.

The suitable cells were then added up for each building. A building was counted as viable if it had at least 10 m² of suitable roof, which is about the space a small system needs. We also worked out the share of each roof that was suitable, so large buildings would not look better just for being large.


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