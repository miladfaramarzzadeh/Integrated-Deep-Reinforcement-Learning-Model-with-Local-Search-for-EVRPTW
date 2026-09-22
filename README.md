# Integrated-Deep-Reinforcement-Learning-Model-with-Local-Search-for-EVRPTW
Hybrid Deep Reinforcement Learning with Local Search for the Electric Vehicle Routing Problem with Time Windows (EVRPTW). It is tested on the Schneider benchmarks and on large-scale Amazon Last Mile instances.

# EV parameters for Amazon Last Mile EVRPTW instances

C = 13.8 * 0.78     # m^3, usable cargo capacity (78% of 13.8 m^3)
Q = 100             # kWh, battery capacity (use 5 kWh for 10-customer instances)
h = 0.45            # kWh/km, energy consumption rate
g = 120 / 3600.0    # kWh/s, charging rate (120 kW)
V = 35 / 3600.0     # km/s, travel speed (35 km/h)
