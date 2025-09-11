# TrafficAssignmentModel‑Taipei

A collection of Jupyter Notebooks and Python utilities to build, solve, and visualize traffic assignment models for Taipei. This repository demonstrates data preparation, network building, demand creation, and numerical solution methods (e.g., All‑or‑Nothing, Frank‑Wolfe / User Equilibrium).

## Network and OD data
- TRTS-4S data: https://github.com/aKi-1201/ta-model-TRTS-4S
- Put the downloaded network and OD data files into: ta/Taipei

## Run
Run the main notebook:

   file name: `ta - VehType - auto.ipynb`

the main notebook uses data in ta/Taipei and runs the experiments automatically.

## Taipei-specific features (supported)
- Motorcycle restriction (禁行機車)
- Two-stage left turns (兩段式左轉): apply an additional 1-minute travel time penalty; apply a 2-minute penalty for large intersections.
