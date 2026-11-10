# Analysis notebook

Start with `churnprediction.ipynb` and run its cells in order using the project virtual environment.

The notebook contains the completed Phase 1 audit and all three Phase 2 steps. `prepared_df` retains all 7,043 records and 25 fields, including the churn flag, missing-total flag, and ordered tenure/charge bands; 11 unknown totals stay missing. Run all 81 code cells to recreate and verify `outputs/data/customer_analytical.csv`, `outputs/reports/prepared_data_dictionary.csv`, and `outputs/reports/phase_2_validation_summary.md`. Raw data and the reviewed raw dictionary stay unchanged. Phase 3 exploratory analysis and SQL validation are next.

Run with the working directory at the repository root or inside this repository. Paths are resolved from the location of `datasets/TelecomChurnData.csv`, so no machine-specific path is required.

Keep raw data unchanged. Generated customer tables in `outputs/data/` are ignored by Git; rerun the notebook after a fresh checkout. Step 3 demonstrates literal text loading, explicit numeric conversion, empty-total handling, and restored category order from the prepared-data dictionary. Restart the kernel and run all cells before saving reviewed aggregate outputs. Do not display or save individual customer records or identifiers in notebook outputs.
