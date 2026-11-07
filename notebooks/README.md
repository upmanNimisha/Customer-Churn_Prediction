# Analysis notebook

Start with `churnprediction.ipynb` and run its cells in order using the project virtual environment.

The notebook contains the completed Phase 1 audit and Phase 2 Step 1 preparation. Step 1 creates `prepared_df` in memory, preserving all 7,043 records and keeping 11 unknown totals missing. Derived fields, bands, final export, and later-phase analyses remain planned.

Run with the working directory at the repository root or inside this repository. Paths are resolved from the location of `datasets/TelecomChurnData.csv`, so no machine-specific path is required.

Keep raw data unchanged. Save reviewed reports in `outputs/reports/`, charts in `outputs/charts/`, and derived customer tables in `outputs/data/` when export is implemented. Restart the kernel and run all cells before saving reviewed aggregate outputs. Do not display or save individual customer records or identifiers in notebook outputs.
