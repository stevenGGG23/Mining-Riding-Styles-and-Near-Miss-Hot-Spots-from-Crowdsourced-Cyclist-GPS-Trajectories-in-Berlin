# Team 3 Final Project (P5): Mining Behavior Patterns from GPS Trajectories of Cyclists

Steven Gobran and Kevin Yassa, CSCI 4900/6000, Dr. Arpan Sainju, Fall 2026

## Folder structure

| Folder | What is inside |
|---|---|
| `1_report/` | Final report (`.docx` and `.pdf`) and all 8 figures as PNG files |
| `2_data/` | Processed data used by the notebook, plus the lists of raw SimRa files we downloaded |
| `3_code/` | `final_project.ipynb` (main code, already run with outputs) and `src/` (the same code as Python scripts) |
| `4_previous_deliverables/` | Our P1, P2, P3 and P4 submissions |

## Data (`2_data/`)

Source: SimRa dataset, TU Berlin, https://github.com/simra-project/dataset (folders `Berlin_2023_05`, `Berlin_2023_06`, `Berlin_2023_07`).

| File | Rows | Description |
|---|---|---|
| `p4_ride_features.csv` | 18,520 | One row per ride per smoothing setting (2,315 rides x 8 settings): speed, stops, turns, acceleration, GPS error, phone position |
| `p4_points.csv` | 1,022,956 | Every cleaned and smoothed GPS point (ride id, month, lat, lon), used for the hot spot grid |
| `p4_incidents.csv` | 1,282 | Every near miss tagged by riders (location, type, scary flag, who was involved) |
| `p4_cleaning_log.csv` | 3 | Fixes and rides dropped in cleaning, per month |
| `p4_example_ride.csv` | 1 ride | Raw GPS of the ride in Figure 2 |
| `raw_file_lists/` | | Direct download links for all raw ride files |

The raw files (about 2 GB) are not included. They can be downloaded with `3_code/src/download_data.sh`, or by setting `RUN_REBUILD = True` in the notebook.

## Code (`3_code/`)

**Main file: `final_project.ipynb`.** Every section has a markdown header that explains it. It runs top to bottom in a few minutes using the CSVs in `2_data/`:

0. Setup: imports, file paths and parameters
1. Pipeline functions: cleaning, rolling-median smoothing, ride features (Algorithm 1), phone-controlled clustering (Algorithm 2), empirical Bayes and Gi* hot spot scoring (Algorithm 3)
2. Load data (with a check that every file is complete)
3. Q1: GPS smoothing experiment
4. Q2: clustering, baseline vs. proposed, and riding styles
5. Q3: hot spot scoring with temporal holdout (lift@k, capture@k)
6. Q4: runtime

**`src/`** has the same functions as plain Python modules, used for the full batch run that produced the report numbers:

| File | Purpose |
|---|---|
| `pipeline.py` | Parameters, haversine distance, cleaning, smoothing, ride features, grid cell ids |
| `clustering.py` | Feature design with phone control, k-means, cluster metrics |
| `hotspots.py` | Grid tables, empirical Bayes rate, Gi*, holdout evaluation |
| `download_data.sh` | Step 0: download raw ride files from the lists in `2_data/raw_file_lists/` |
| `extract.py` | Step 1: parse raw ride files of one month into a pickle |
| `build.py` | Step 2: clean, smooth and compute features for all months |
| `exp_load.py`, `run_experiments.py` | Step 3: run Q1 to Q4 and save results and figures |

Libraries: Python 3, pandas, NumPy, SciPy, scikit-learn, Matplotlib (`pip install pandas numpy scipy scikit-learn matplotlib jupyter`).

## Main results

- Q1: smoothing cut the link between acceleration and GPS error from 0.63 to 0.45; median speed stayed 18.4 to 18.5 km/h.
- Q2: phone control cut the link between clusters and phone position from 0.43 to 0.11 (June) and removed the pocket-phone artifact cluster.
- Q3: empirical Bayes hot spots had 18% higher future risk than raw counts (lift@50 6.5 vs. 5.5) at the same capture (35%).
- Q4: 2.9 ms per ride.
