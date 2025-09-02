# SpaceX Falcon 9 — First‑Stage Landing Prediction 🚀
_A Machine Learning capstone built from data collection → wrangling → EDA/SQL → visualization → geospatial mapping → classification._

## Overview
This repository predicts **whether a Falcon 9 first stage lands successfully** (`Class ∈ {0,1}`). It combines real data collection (API + web scraping), rigorous wrangling, exploratory analysis (including SQL-in-notebook), visualization, geospatial mapping with Folium, and a final ML model trained and evaluated on engineered features. The repo also includes an optional **Dash** dashboard for interactive exploration.

**Why this project matters:** recovering the booster saves millions. Predicting success helps analyze risk, identify drivers (payload, site, booster version), and communicate insights to stakeholders.

---

## What’s in this repo (your working files)
- **Data**
  - `dataset_part_2.csv` — enriched dataset with core fields like `FlightNumber, Date, BoosterVersion, PayloadMass, Orbit, LaunchSite, Outcome, Flights, GridFins, Reused, Legs, LandingPad, Block, ReusedCount, Serial, Longitude, Latitude, Class` plus one‑hot columns for booster serials.
  - `dataset_part_3.csv` — modeling‑ready matrix with numerical/categorical features (including one‑hot columns for `Serial_*`) used in training/evaluation.

- **Notebooks (by stage)**
  1. `jupyter-labs-spacex-data-collection-api.ipynb` — Data collection via API (requests), JSON to tabular, initial cleaning.
  2. `jupyter-labs-webscraping.ipynb` — Supplement data with web scraping (BeautifulSoup), parsing HTML tables/pages.
  3. `labs-jupyter-spacex-Data wrangling.ipynb` — Cleaning, type casting, feature derivation, handling missingness; export to `dataset_part_2.csv`.
  4. `jupyter-labs-eda-sql-coursera_sqllite.ipynb` — EDA using **SQLite** inside the notebook (SQL cells alongside pandas) to answer analytical questions.
  5. `Data_Visualization_lab.ipynb` — Visual analytics: distributions, relationships, categorical contrasts; plots ready for reports.
  6. `lab_jupyter_launch_site_location.ipynb` — **Folium** map of launch sites (MarkerCluster), success/failure coloring, tooltips/popups.
  7. `SpaceX_Machine Learning Prediction_Part_5.ipynb` — Model training & evaluation for the landing classification task; confusion matrix, ROC/PR‑AUC, calibration and threshold search.

- **Apps / Scripts**
  - `SpaceX.py` — Plotly **Dash** app (“SpaceX Launch Records Dashboard”) for interactive filtering and scatter/pie views of payload vs success. Run with `python SpaceX.py`.  *(Code structure mirrors the standard Coursera lab app.)*

---

## Project structure (suggested)
```
.
├── data/
│   ├── raw/                         # API/HTML outputs before cleaning
│   ├── interim/                     # temporary merged/intermediate
│   └── processed/                   # dataset_part_2.csv, dataset_part_3.csv
├── notebooks/
│   ├── 01_data_collection_api.ipynb
│   ├── 02_webscraping.ipynb
│   ├── 03_data_wrangling.ipynb
│   ├── 04_eda_sql.ipynb
│   ├── 05_visualization.ipynb
│   ├── 06_geospatial_folium.ipynb
│   └── 07_ml_prediction.ipynb
├── src/                             # optional: move reusable code here
│   ├── features.py
│   ├── train.py
│   ├── evaluate.py
│   └── infer.py
├── app/
│   └── dash_app.py                  # (rename of SpaceX.py if you prefer)
├── reports/
│   ├── figures/                     # confusion matrix, ROC/PR, calibration
│   └── tables/                      # metrics summaries
└── README.md
```

> You already have files organized; the above is a “cleaned” layout you can adopt progressively.

---

## Environment & setup

### Requirements (minimal)
```txt
python>=3.9
pandas
numpy
scikit-learn
matplotlib
seaborn
plotly
dash
folium
requests
beautifulsoup4
lxml
sqlalchemy
jupyter
joblib
```
Install:
```bash
pip install -r requirements.txt
# or, quickly:
pip install pandas numpy scikit-learn matplotlib seaborn plotly dash folium requests beautifulsoup4 lxml sqlalchemy jupyter joblib
```

### Trusting notebooks
If outputs (JS/HTML) don’t render, trust the notebook:
```bash
jupyter trust path/to/notebook.ipynb
```

---

## Reproducing the pipeline

### 1) Data collection
- Run `jupyter-labs-spacex-data-collection-api.ipynb` to pull launch data via an API, normalize JSON to tabular, and save a raw CSV.
- Run `jupyter-labs-webscraping.ipynb` to scrape supplemental attributes (e.g., tables with outcomes), merge with API data.

### 2) Wrangling & feature engineering
- Use `labs-jupyter-spacex-Data wrangling.ipynb` to clean types (dates/categoricals), fill/flag missingness, compute engineered features, and export **`dataset_part_2.csv`** to `data/processed/`.

### 3) EDA (including SQL)
- In `jupyter-labs-eda-sql-coursera_sqllite.ipynb`, run SQL queries against an in‑notebook SQLite DB and cross‑check with pandas for accuracy and performance.

### 4) Visualization
- Explore patterns in `Data_Visualization_lab.ipynb` (class imbalance, payload vs outcome, booster categories). Save charts to `reports/figures/`.

### 5) Geospatial mapping
- Open `lab_jupyter_launch_site_location.ipynb` to render a **Folium** map showing launch sites (MarkerCluster) colored by outcome; export to `reports/figures/launch_sites_map.html`.

### 6) Modeling & evaluation
- Train and evaluate in `SpaceX_Machine Learning Prediction_Part_5.ipynb` using `dataset_part_3.csv` (modeling‑ready features).
- Report: **Precision/Recall/F1**, **ROC‑AUC**, **PR‑AUC** (recommended under imbalance), confusion matrix, and **calibration** (Brier/log loss). Select threshold tied to the problem goal (e.g., favor recall).

---

## Example CLI (optional if you move logic to `src/`)
```bash
python -m src.train     --train data/processed/dataset_part_3.csv --model_out models/best_model.joblib --seed 42
python -m src.evaluate  --model models/best_model.joblib         --test  data/processed/dataset_part_3.csv --report_dir reports/
python -m src.infer     --model models/best_model.joblib         --input data/processed/dataset_part_3.csv --output predictions.csv
```

---

## Dash app (interactive)
Run the dashboard locally:
```bash
python SpaceX.py
# then open the URL shown in the terminal (usually http://127.0.0.1:8050)
```
The app provides:
- A **site dropdown** for “All Sites” or a specific launch site
- A **pie chart** summarizing success counts
- A **payload range slider** and **scatter plot** showing correlation between payload and success, colored by booster version

> This follows the standard “SpaceX Launch Records Dashboard” structure from the lab code. Update `spacex_launch_dash.csv` path if needed, and ensure columns like `Payload Mass (kg)`, `Launch Site`, `class`, and `Booster Version Category` exist in your data.

---

## Results & reporting
- Place figures (confusion matrix, ROC/PR curves, reliability diagram) in `reports/figures/`.
- Summarize metrics in `reports/tables/` and the README.
- When reporting, prefer **PR‑AUC** for imbalanced positive class and document the **chosen threshold** and its business trade‑off.

---

## Tips & quality
- Use **stratified splits** and avoid leakage (fit scalers/encoders inside pipelines).
- Set **random seeds** throughout (`numpy`, `sklearn`) to improve reproducibility.
- Consider `GridSearchCV` / `RandomizedSearchCV` for tuning and log results.
- Add `requirements.txt` and optionally a Conda `environment.yml` for portability.

---

## Acknowledgments
- Educational SpaceX dataset and open web sources used for learning.
- pandas, scikit-learn, matplotlib, seaborn, plotly/dash, folium communities.

---

## License
MIT (or your preferred license).

