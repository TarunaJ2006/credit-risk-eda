# Credit Risk EDA – Loan Default Analysis

This repository contains an exploratory data analysis (EDA) notebook focused on credit risk and loan default behavior. The analysis is implemented in the Jupyter notebook `creditEDA.ipynb` and uses the `application_train.csv` dataset (and the related `previous_application.csv` archive) to explore borrower characteristics, missing-data patterns, feature relationships, and simple grouped analyses.

## What this notebook does

- Loads applicant and previous application datasets derived from the Home Credit-style dataset: `application_train.csv` and `previous_application.csv.zip`.
- Performs data cleaning and missing-value handling (column drops based on missingness thresholds, mode/median imputation for selected columns, simple categorical fill-ins).
- Engineers a few time-based features (converting DAYS_* columns into YEARS_*), and creates categorical bins for credit amount and age.
- Conducts univariate and bivariate analyses (pie charts, bar charts, boxplots, distribution plots), correlation heatmaps, scatterplots and pairplots.
- Merges current and previous application tables to analyze relationships between contract status and default rates.

## Key objectives

- Understand the distribution of features that relate to loan default (`TARGET`).
- Identify missing-value patterns and demonstrate pragmatic imputation choices.
- Visualize relationships between income, requested credit, annuity, and goods price.
- Compare characteristics of accepted vs refused/approved previous applications.
- Produce grouped pivot tables and heatmaps to surface segments with elevated default rates.

## Dataset overview

- Primary dataset: `application_train.csv` — applicant-level records with demographic, financial and loan application attributes.
- Secondary dataset: `previous_application.csv.zip` — previous credit applications for the same clients. This is loaded and merged (left join on `SK_ID_CURR`).

Important features used in the notebook (non-exhaustive):

- `SK_ID_CURR` — unique applicant identifier.
- `TARGET` — binary target where 1 indicates loan default and 0 indicates non-default.
- Demographic and identity features: `CODE_GENDER`, `NAME_INCOME_TYPE`, `CNT_FAM_MEMBERS`, `OCCUPATION_TYPE`, `DAYS_BIRTH` (converted to `YEARS_BIRTH`).
- Financial features: `AMT_INCOME_TOTAL`, `AMT_CREDIT`, `AMT_ANNUITY`, `AMT_GOODS_PRICE`.
- External scores: `EXT_SOURCE_2`, `EXT_SOURCE_3` (used and imputed where missing).

The notebook documents decisions such as dropping columns with excessive missingness (47% threshold on the main dataset, 49% on the previous applications dataset) and column-specific imputations (mode/median). It also creates derived features: `YEARS_BIRTH`, `YEARS_EMPLOYED`, `AMT_CREDIT_Category`, and `AGE_Category`.

## Tools and libraries

- pandas — data loading, cleaning and grouping.
- numpy — numeric utilities and percentiles.
- matplotlib — plotting primitives.
- seaborn — high-level statistical visualizations (boxplots, heatmaps, pairplots, distplots).
- Jupyter Notebook / IPython magic (`%matplotlib inline`) for interactive visualization.

Refer to `requirements.txt` for a concrete list of packages and versions used to run the notebook reproducibly.

## High-level workflow

1. Data loading: read `application_train.csv` and `previous_application.csv.zip` into pandas DataFrames.
2. Data auditing: inspect dtypes, missing-value percentages and basic statistics.
3. Cleaning:
   - Drop columns that exceed a missing-value threshold.
   - Fill categorical missing values (e.g., `OCCUPATION_TYPE`) with a reasonable placeholder (`Others`) or the column mode.
   - Fill numeric missing values for score columns and monetary fields using median or mode depending on distribution.
4. Feature engineering: convert DAYS_* fields into YEARS_*, bin credit and age into categories.
5. Univariate analysis: value counts, pie charts and boxplots for individual features.
6. Bivariate analysis: compare feature distributions for `TARGET == 0` vs `TARGET == 1` (bar plots, boxplots, distplots).
7. Correlation analysis: compute and plot correlation matrices for selected numeric features; separate heatmaps for default vs non-default subsets.
8. Previous applications analysis: summarize contract status breakdowns and compare statistics across approved/cancelled/refused/unused groups.
9. Merge current and previous records to create pivot tables and heatmaps that help identify segments with higher mean `TARGET` (default) rates.

## Key findings and example insights

The notebook's exploratory steps surface several practical observations (examples below are drawn directly from the notebook's analyses and groupings):

- Class imbalance: The `TARGET` variable is imbalanced — the majority of records are non-defaults (`TARGET == 0`). This should be considered when training models (resampling or adjusted evaluation metrics may be required).
- Missing data: Several columns contain substantial missingness and are removed early (threshold-based). For remaining important columns, the notebook uses median/mode imputation (for example `EXT_SOURCE_3` is imputed with its median and `OCCUPATION_TYPE` is filled with `Others`).
- Feature relationships: Strong relationships appear between application monetary fields (`AMT_INCOME_TOTAL`, `AMT_CREDIT`, `AMT_ANNUITY`, `AMT_GOODS_PRICE`) — the notebook visualizes these with pairplots and correlation heatmaps to identify likely multicollinearity.
- Segment signals: Grouped pivot tables (for example by `NAME_INCOME_TYPE` and `NAME_CLIENT_TYPE` or by `CODE_GENDER` and `NAME_SELLER_INDUSTRY`) reveal differences in mean default rate and total goods price exposure across segments. These aggregated signals can guide feature creation for downstream modeling.

Note: the notebook focuses on EDA and does not build predictive models. The insights are intended to inform feature engineering, data-quality improvements, and sampling strategies for later modeling.

## How to run the notebook

These instructions assume you have the repository checked out locally.

1. Create a Python virtual environment (recommended):

   - Windows PowerShell:

     ```powershell
     python -m venv .venv; .\.venv\Scripts\Activate.ps1
     ```

2. Install dependencies:

   ```powershell
   pip install -r requirements.txt
   ```

3. Place the data files in a `data/` directory at the repository root (create it if necessary):

   - `data/application_train.csv`
   - `data/previous_application.csv` or `data/previous_application.csv.zip`

   The notebook's original code references absolute paths on the author’s machine. To run the notebook locally, either:
   - Update the file path references at the top of `creditEDA.ipynb` to point to `./data/...`, or
   - Place the original files in the same paths used in the notebook. Editing the notebook is not required, but updating paths makes the project portable.

4. Start Jupyter Notebook and open `creditEDA.ipynb`:

   ```powershell
   jupyter notebook
   ```

5. Work through the notebook cells to reproduce the exploratory figures. Visualizations will render inline.

## Recommended next steps (optional)

- Parameterize the notebook file paths or add a small helper script to load data from `data/` to make the project more portable.
- Save key figures to an `outputs/` directory for use in reports.
- Add a simple unit test or smoke test to validate that the core data files can be loaded and that key columns exist.

## Repository / folder structure

Suggested layout for a cleaned project:

```
.
├─ data/                       # raw CSVs (not tracked in git by default)
│  ├─ application_train.csv
│  └─ previous_application.csv.zip
├─ notebooks/
│  └─ creditEDA.ipynb          # the analysis notebook (already present at repo root)
├─ outputs/                    # figures, exported tables, reports
├─ README.md
├─ requirements.txt
├─ CONTRIBUTING.md
├─ LICENSE.md
└─ .gitignore
```

## Credits / Author

This documentation was created to accompany the `creditEDA.ipynb` exploratory notebook found in this repository. The notebook uses a Home-Credit-style public dataset (commonly distributed for credit risk challenges). If you are the original notebook author, add your name and contact details here for attribution.

If you reuse data or code from other sources, please cite those sources in the notebook and this README.
