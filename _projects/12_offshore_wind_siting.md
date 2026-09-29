---
layout: page
title: Optimizing Offshore Wind Siting in California
description: Led a five-person team that built a Python optimization model to site offshore wind turbines off California, balancing wind output against ecological impacts, shipping congestion, and grid constraints.
img: assets/img/projects/card_osw.png
og_image: /assets/img/projects/card_osw.png
importance: 2
category: grid & financial modeling
---

_Project lead, with Sophie Castillo, Gabe Hiestand, Paola Lorusso, and Whitley Rummel · CE 295 class project, UC Berkeley, 2024_

<div class="card mt-3 mb-4">
  <div class="card-body">
    <p class="text-muted mb-2" style="font-size: 0.8rem; letter-spacing: 0.08em; text-transform: uppercase">At a glance</p>
    <table class="table table-sm mb-0">
      <tr><th scope="row" style="width: 7rem; border-top: none">Role</th><td style="border-top: none">Project lead: proposed and scoped the project; wrote the motivation, methodology, and policy sections</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Methods</th><td style="border-top: none">Constrained optimization with penalty functions, weight optimization, geospatial analysis</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Tools</th><td style="border-top: none">Python (pandas, GeoPandas, SciPy), ArcGIS</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Skills</th><td style="border-top: none">Renewable resource planning, optimization, geospatial data, policy translation</td></tr>
      <tr><th scope="row" style="width: 7rem; border-top: none">Available on request</th><td style="border-top: none">Full notebook and final report</td></tr>
    </table>
  </div>
</div>

## The question

California plans 25 GW of offshore wind by 2045. The state's siting work relied on overlaying map layers in GIS and screened sites mainly on technical criteria, leaving ecological and economic conflicts largely unresolved. We asked where turbines should go once those conflicts are counted.

## What I did

1. **Led the project.** Proposed the idea, assembled the team, and scoped the problem against the California Energy Commission's siting approach.
2. **Designed the approach.** Framed siting as an optimization over 2.75 km grid cells: maximize wind output, with penalties for shipping lanes, marine habitat, whale migration, and distance from existing grid infrastructure.
3. **Supported the modeling.** Sourced and processed geospatial data and helped teammates build and run the model.
4. **Translated results for policymakers.** Wrote the background, methodology, and policy implications, including how regulators can adjust the weights to reflect their priorities.

<div class="row justify-content-sm-center">
  <div class="col-sm-12 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/osw_siting.png" title="Offshore wind siting scores compared with CEC suitable sea space" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Siting scores with equal weights (left) and optimized weights (center), compared with the CEC's AB 525 suitable sea space (right; source: CEC, 2023). Red is more suitable.
</div>

## What it shows

- **A sharper map.** Results match most of the Commission's priority areas in Northern California, but screen out Monterey because of whale activity that the map-overlay approach missed.
- **Real trade-offs.** Few sites combine strong wind with low conflict, so meeting the 2045 goal will require accepting some.
- **From model to policy.** A technical tool framed so regulators can use it.

## Code highlights

Excerpts from the team's Python notebook, lightly edited for readability. The full notebook is available on request.

**Wind power in each grid cell,** from annual average wind speed and a 17 MW-class floating turbine:

```python
def power_output(air_density, swept_area, wind_speed, power_coefficient):
    return 0.5 * air_density * swept_area * wind_speed**3 * power_coefficient

data["power_output_MW"] = power_output(
    air_density=1.225,               # kg/m^3
    swept_area=3.14 * 120**2,        # m^2, ~240 m rotor
    wind_speed=data["Annual_A_1"],   # m/s, Data Basin offshore wind aliquots
    power_coefficient=16 / 27,       # Betz limit, used as a relative metric
) / 1e6
```

**Scoring each cell:** normalized output minus weighted, normalized penalties:

```python
penalties = ["Normalized_Penalty",          # shipping lanes
             "normalized_power_plant_Pen",  # distance from existing coastal plants
             "Marine_Penalty",              # whale and sea turtle critical habitat
             "P_TW"]                        # toothed whale migration density

def score(data, w):
    return data["normalized_power_output_MW"] - data[penalties].values @ w

data["score_equal"] = score(data, np.full(4, 0.25))
```

**Choosing the weights** with SLSQP, so the result shows which constraints matter most:

```python
from scipy.optimize import minimize

res = minimize(lambda w: -score(data, w).mean(),
               x0=np.array([0.5, 1, 1, 1]) / 3.5,
               bounds=[(0, 1)] * 4, method="SLSQP")
optimal_w = res.x / res.x.sum()
data["score_optimized"] = score(data, optimal_w)
```

_The report and code are available to employers on request. [Contact me](mailto:yliang@hks.harvard.edu)._
