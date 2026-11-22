# AgroIntel

An end-to-end agricultural market analytics and data engineering project on **Databricks** that explores **monthly mandi wheat prices**, **wheat arrivals**, and **weather data** across states, districts, and months. The project combines Python extraction and validation scripts, medallion-style Databricks pipeline runners, exploratory and analytical Jupyter/Databricks notebooks, structured project documentation, and an interactive **AgroIntel** AI/BI dashboard.

---

## Table of Contents

* [Overview & Problem Context](#overview--problem-context)
* [Project Structure](#project-structure)
* [Datasets & Data Flow](#datasets--data-flow)
* [Component Breakdown](#component-breakdown)
  * [Databricks Pipeline Scripts](#databricks-pipeline-scripts)
  * [Extraction & Validation Scripts](#extraction--validation-scripts)
  * [Notebooks](#notebooks)
  * [Documentation Suite](#documentation-suite)
* [Running the Project](#running-the-project)
  * [Running on Databricks](#running-on-databricks)
  * [Running Locally](#running-locally)
* [Dashboard & Showcase (`AgroIntel`)](#dashboard--showcase-agrointel)
* [Known Quirks & Practical Notes](#known-quirks--practical-notes)
* [Future Improvements](#future-improvements)

---

## Overview & Problem Context

Agricultural commodity markets—especially staples like wheat—are shaped by seasonal harvest cycles, physical arrival volumes at mandis (agricultural markets), regional price dispersion, and weather conditions. Analyzing these dimensions together helps connect supply inflows and climate context with price behavior across locations and months.

This repository organizes a multi-source wheat market intelligence workflow around:
* **Mandi wheat price metrics** (`monthly_mandi_wheat_prices.csv`)
* **Wheat arrival quantities** (`monthly_wheat_arrivals.csv`)
* **Monthly weather records** (`monthly_weather.csv`)
* **Combined analytical intelligence** (`monthly_combined_intelligence.csv`)

Supporting design documents in `documentation/` outline the project's problem statement, scope, dataset sources, methodology, architecture, and data dictionary.

---

## Project Structure

Below is the layout of the project files contained in the archive (excluding macOS `._*` and `__MACOSX` archive metadata entries):

```text
Mandi-Price-Intelligence/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── processed/
│       ├── monthly_combined_intelligence.csv
│       ├── monthly_mandi_wheat_prices.csv
│       ├── monthly_weather.csv
│       └── monthly_wheat_arrivals.csv
├── databricks_pipeline/
│   ├── execute_medallion.py
│   ├── local_medallion_runner.py
│   └── mandi_medallion_pipeline.py
├── documentation/
│   ├── architecture.md
│   ├── data_dictionary.md
│   ├── dataset_sources.md
│   ├── methodology.md
│   ├── problem_statement.md
│   └── project_scope.md
├── notebooks/
│   ├── 01_Data_Ingestion_and_Exploration.ipynb
│   └── 02_Data_Analysis_and_Insights.ipynb
└── scripts/
    ├── arrival_extraction.py
    ├── arrival_validation.py
    ├── legacy_convert_to_parquet.py
    ├── legacy_data_integration.py
    ├── mandi_validation.py
    └── weather_extraction.py
```

---

## Datasets & Data Flow

The `data/processed/` directory provides four monthly CSV datasets used for ingestion, exploration, and analysis:

| File | Purpose |
| :--- | :--- |
| `data/processed/monthly_mandi_wheat_prices.csv` | Monthly wheat price records across markets/regions. |
| `data/processed/monthly_wheat_arrivals.csv` | Monthly wheat arrival volumes (including tonnage fields such as `arrival_quantity_tonnes`). |
| `data/processed/monthly_weather.csv` | Monthly weather measurements (such as rainfall and growing-condition metrics). |
| `data/processed/monthly_combined_intelligence.csv` | Unified monthly dataset combining price, arrival, and weather fields for cross-domain analysis and dashboarding. |

### High-Level Flow

```text
[Extraction & Validation Scripts (scripts/)]
                 │
                 ▼
[Processed Monthly CSVs (data/processed/)]
                 │
       ┌─────────┴─────────┐
       ▼                   ▼
[Databricks Pipeline]   [Notebooks (01 & 02)]
(databricks_pipeline/)   ├─ Ingestion & Schema/Record Checks
       │                 └─ Monthly Trends, Arrivals & Weather Insights
       ▼
[AgroIntel AI/BI Dashboard]
(KPIs, Filters, Trend & Regional Charts)
```

---

## Component Breakdown

### Databricks Pipeline Scripts
Located in `databricks_pipeline/`:
* `mandi_medallion_pipeline.py` — Core medallion-style pipeline logic for processing mandi price, arrival, and weather datasets.
* `local_medallion_runner.py` — Runner script tailored for local execution of the medallion workflow.
* `execute_medallion.py` — Execution entry point / orchestration script for running medallion stages.

### Extraction & Validation Scripts
Located in `scripts/`:
* `arrival_extraction.py` — Extracts/prepares wheat arrival records.
* `weather_extraction.py` — Extracts/prepares weather data.
* `arrival_validation.py` — Validates arrival data quality.
* `mandi_validation.py` — Validates mandi price data quality.
* `legacy_data_integration.py` — Earlier/legacy data integration script kept from prior iterations.
* `legacy_convert_to_parquet.py` — Earlier/legacy helper script for converting data to Parquet format.

### Notebooks
Located in `notebooks/`:
* `01_Data_Ingestion_and_Exploration.ipynb` — Loads the mandi price, arrivals, and weather datasets, inspects schemas and record counts, and performs initial data exploration.
* `02_Data_Analysis_and_Insights.ipynb` — Computes monthly trends (such as average modal prices and total wheat arrivals across months and districts) and generates analytical insights.

### Documentation Suite
Located in `documentation/`:
* `problem_statement.md` — Defines the business and analytical problem being solved.
* `project_scope.md` — Documents what is in scope and out of scope for the project.
* `dataset_sources.md` — Describes data origins and source characteristics.
* `methodology.md` — Explains analytical and data-processing methods.
* `architecture.md` — Details system and pipeline architecture.
* `data_dictionary.md` — Documents fields, meanings, and data definitions.

---

## Running the Project

### Running on Databricks

1. **Prepare the project files**
   * Extract the archive and remove `__MACOSX` and `._*` files.
2. **Upload datasets to a Unity Catalog Volume**
   * Upload the four CSV files from `data/processed/` (`monthly_mandi_wheat_prices.csv`, `monthly_wheat_arrivals.csv`, `monthly_weather.csv`, and `monthly_combined_intelligence.csv`) into a Unity Catalog Volume (for example, `/Volumes/workspace/default/agro/`).
3. **Import notebooks and scripts into the Workspace**
   * Create a project folder in **Workspace** and import `01_Data_Ingestion_and_Exploration.ipynb`, `02_Data_Analysis_and_Insights.ipynb`, and the scripts from `databricks_pipeline/` and `scripts/`.
4. **Configure paths and run notebooks in order**
   * Attach cluster or serverless compute and point dataset read paths to your volume path (such as `/Volumes/workspace/default/agro/`).
   * Run `01_Data_Ingestion_and_Exploration.ipynb` top-to-bottom first, then run `02_Data_Analysis_and_Insights.ipynb` top-to-bottom.

### Running Locally

1. **Set up Python environment**
   * Extract the project folder and install the dependencies listed in `requirements.txt`:
     ```bash
     pip install -r requirements.txt
     ```
2. **Run scripts or notebooks**
   * Use `local_medallion_runner.py` or inspect the scripts in `scripts/` and `databricks_pipeline/` depending on the workflow stage you want to run locally.

---

## Dashboard & Showcase (`AgroIntel`)

The project includes an interactive Databricks AI/BI dashboard named **AgroIntel** built on `monthly_combined_intelligence.csv`:
* **Global Filters:** Filter interactively by `Month`, `State`, and `District`.
* **KPI Cards:** High-level metrics including **Average Modal Price**, **Price Spread (Max − Min)**, **Total Wheat Arrivals (tonnes)**, and **Active Districts**.
* **Visualizations:**
  * **Average Modal Price Trend** across months.
  * **Monthly Wheat Arrivals** showing seasonal arrival spikes.
  * **Regional & Weather Breakdowns** such as **Top States by Average Modal Price** and **Monthly Rainfall**.

To showcase the project to reviewers, recruiters, or instructors:
* Keep notebook outputs rendered and export static copies (HTML/PDF or screenshots) alongside the repository.
* Publish the `AgroIntel` dashboard with default (`All`) filters active and include screenshots in your portfolio or repo.

---

## Known Quirks & Practical Notes

* **Path & format alignment:** Early notebook cells may originally reference a `processed/` subfolder or Parquet volume path; update those to your actual Unity Catalog Volume path and `.csv` filenames in `data/processed/`.
* **Column naming casing:** Some columns in the CSVs use lowercase names such as `month` and `arrival_quantity_tonnes` rather than title-cased names like `Month` or `Total_Arrival_Quantity`. Verify column names with `.printSchema()` if any aggregation throws an unresolved-column error.
* **Archive artifacts:** Unzipping on macOS creates `__MACOSX/` and `._*` files—delete them before committing to Git.

---

## Future Improvements

* **Consolidate pipeline & legacy code:** Merge overlapping logic across `databricks_pipeline/` (`execute_medallion.py`, `local_medallion_runner.py`, `mandi_medallion_pipeline.py`) and move `legacy_data_integration.py` and `legacy_convert_to_parquet.py` into a clear `archive/` folder with single-source path configuration.
* **Strengthen automated validation:** Expand `mandi_validation.py` and `arrival_validation.py` with explicit schema checks, null/duplicate checks, and range assertions (such as non-negative prices and arrival quantities).
* **Standardize on Delta / Parquet for analytical layers:** Persist curated outputs as Delta tables in Unity Catalog for faster querying, schema enforcement, and version history across notebooks and dashboards.
* **Deepen analytical takeaways:** Add lag/correlation analysis between monthly rainfall/weather, arrival surges, and modal price movements in `02_Data_Analysis_and_Insights.ipynb`.
