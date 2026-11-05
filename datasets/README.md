# Source data

`TelecomChurnData.csv` is the raw project input. Preserve it unchanged; save derived customer tables in `outputs/data/`.

- Expected grain: one customer per row; verify identifier uniqueness in Phase 1.
- Outcome: `Churn`; its observation window and business definition need verification.
- Source URL, licence, collection period, and field units: not yet verified.
- Draft field descriptions: `outputs/reports/data_dictionary.csv`.

Do not interpret tenure groups as acquisition cohorts or infer time trends without dated observations.
