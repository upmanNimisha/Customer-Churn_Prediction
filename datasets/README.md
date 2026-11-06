# Source data

`TelecomChurnData.csv` is the raw project input. Preserve it unchanged; save derived customer tables in `outputs/data/`.

- Observed grain: 7,043 rows with 7,043 distinct nonblank `customerID` values and no duplicates. Whether an ID identifies a person, account, or subscription remains unverified.
- Outcome: `Churn`; its observation window and business definition need verification.
- Source URL, licence, collection period, and field units: not yet verified.
- Observed field profiles and provisional source meanings: [data dictionary](../outputs/reports/data_dictionary.csv).
- Phase 1 review and preparation rules: [quality report](../outputs/reports/data_quality_report.md). Retain the 11 blank totals as unknown in the prepared dataset; do not impute zero without supporting evidence.

Do not interpret tenure groups as acquisition cohorts or infer time trends without dated observations.
