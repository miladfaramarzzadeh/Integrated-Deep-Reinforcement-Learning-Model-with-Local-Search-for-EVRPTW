# DRL-LS: Deep Reinforcement Learning with Local Search for the EVRPTW

A hybrid solver for the **Electric Vehicle Routing Problem with Time Windows (EVRPTW)** that combines a Deep Reinforcement Learning construction policy (Actor–Critic) with a Local Search improvement phase. The learned policy builds feasible initial solutions quickly; the local-search phase then refines them with EVRPTW-aware neighborhood operators.

The method is designed for **large-scale instances**. Beyond the classical Schneider et al. (2014) benchmarks, it is evaluated on real-world instances derived from the **Amazon Last Mile Routing Research Challenge** dataset.

---

## Repository Contents

```
├── data/
│   ├── schneider/     # Schneider et al. (2014) benchmark instances
│   └── amazon/        # Processed Amazon Last Mile EVRPTW instances
└── README.md
```

---

## Datasets

### Schneider et al. (2014) Benchmarks

The standard EVRPTW benchmark set, derived from Solomon's VRPTW instances by adding charging stations and battery parameters. It covers the C, R and RC classes. In this work, the large instances (100 customers) are used to validate solution quality against the best-known solutions reported in the literature:

- Schneider, Stenger & Goeke (2014)
- Goeke & Schneider (2015)
- Hiermann, Puchinger, Ropke & Hartl (2016)
- Keskin & Çatay (2016)

### Amazon Last Mile Dataset

#### Source

The real-world instances are built from the **Amazon Last Mile Routing Research Challenge** dataset, released in 2021 by Amazon together with the MIT Center for Transportation & Logistics. It contains historical delivery routes operated by Amazon drivers from delivery stations in several US metropolitan areas (including Los Angeles, Seattle, Chicago, Boston and Austin), and is publicly available through the AWS Open Data Registry.

For each route the dataset provides:

- **Route data:** station, date, departure time, vehicle capacity and stop coordinates
- **Package data:** package dimensions, service times and delivery time windows
- **Travel times:** stop-to-stop travel-time matrix
- **Actual sequences:** the order in which the driver visited the stops

Reference:
> Merchán, D., Arora, J., Pachon, J., Konduri, K., Winkenbach, M., Parks, S., & Noszek, J. (2024). 2021 Amazon Last Mile Routing Research Challenge: Data Set. *Transportation Science*, 58(1), 8–11.

#### Why this dataset matters for the EVRPTW

The classical benchmarks stop at 100 customers, which says little about how a method behaves at the scale of a real delivery station. The Amazon data offers realistic spatial distributions (dense urban clusters instead of synthetic uniform or clustered layouts), real time windows, real service times, and daily demand volumes well above 1,000 stops per station. This makes it well suited for testing whether a learned construction policy plus local search scales to operational problem sizes.

#### Conversion to EVRPTW instances

The raw dataset has no electric-vehicle information, so each instance is extended as follows:

1. **Daily aggregation.** Stops served from the same delivery station on the same day are merged into a single daily instance, with the station as the depot.
2. **Customer sampling.** From each daily instance, subsets are drawn at several sizes: **100, 500, 1,000 and 1,500 customers**, plus the full daily instance (up to about **1,769 customers**).
3. **Demand and time windows.** Customer demand and time windows are derived from the package data.
4. **EV parameters.** Battery capacity, energy consumption rate, charging rate and charging-station locations are added. The vehicle parameters are:

```python
C = 13.8 * 0.78     # m^3, usable cargo capacity
Q = 100             # kWh, battery capacity
h = 0.45            # kWh/km, energy consumption rate
g = 120 / 3600.0    # kWh/s, charging rate (120 kW)
V = 35 / 3600.0     # km/s, travel speed (35 km/h)
```

5. **Filtering.** Instances with fewer than 100 customers are excluded: at that size, routes stay within battery range and never need a charging station, so the EV-specific part of the problem is not exercised.

The resulting test set covers **29 daily instances** from **20.07.2018 to 17.08.2018**, each sampled at multiple customer-set sizes.

---

## Results

| Instance set | Sizes | Comparison |
|---|---|---|
| Schneider et al. (2014) | 100 customers | Best-known solutions from SSG, GS, HPH, KÇ |
| Amazon Last Mile | 100 – ~1,769 customers | Large-scale evaluation |
