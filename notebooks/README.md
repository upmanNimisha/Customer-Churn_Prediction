# Analysis notebook

Start with `churnprediction.ipynb` and run its cells in order using the project virtual environment.

The notebook contains the completed Phase 1 audit and Phase 2 Steps 1 and 2. `prepared_df` retains all 7,043 records, keeps 11 unknown totals missing, and adds the churn flag, missing-total flag, and ordered tenure/charge bands for 25 fields in total. Step 2 exports/reloads `outputs/reports/prepared_data_dictionary.csv`; the raw dictionary stays unchanged. Final customer-table export and later-phase analyses remain planned.

Run with the working directory at the repository root or inside this repository. Paths are resolved from the location of `datasets/TelecomChurnData.csv`, so no machine-specific path is required.

Keep raw data unchanged. Save reviewed reports in `outputs/reports/`, charts in `outputs/charts/`, and derived customer tables in `outputs/data/` when export is implemented. Restart the kernel and run all cells before saving reviewed aggregate outputs. Do not display or save individual customer records or identifiers in notebook outputs.
