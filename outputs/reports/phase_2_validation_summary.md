# Phase 2 Validation Summary

Reviewed: 10 November 2026 (Asia/Calcutta).

Status: notebook acceptance checks passed; repeatability verification is recorded in analysis_log.md.

Produced by notebooks/churnprediction.ipynb, Phase 2 Steps 1-3; unchanged supplied snapshot; all records retained.
Environment: Python 3.14.6, pandas 3.1.1, NumPy 2.5.4.
Input SHA-256: 88be4b93fbe0cc83421af1c503794c97c342eca914c1576db7c276e61d61358a
Export: outputs/data/customer_analytical.csv (generated customer-level data; ignored by Git).
Export SHA-256: b27a6d5a9aa9cf730090a61c5d4779bdc7de15fff5f7dd2eed2d8c2948f374f2
Schema: [prepared_data_dictionary.csv](prepared_data_dictionary.csv), 21 cleaned source fields plus four derived fields.

| Check | Raw | Prepared |
|---|---|---|
| rows | 7043 | 7043 |
| unique_ids | 7043 | 7043 |
| churners | 1869 | 1869 |
| retained | 5174 | 5174 |
| missing_monthly | 0 | 0 |
| unknown_totals | 11 | 11 |

Prepared shape: 7043 rows, 25 uniquely named fields.
Recorded-label prevalence: 26.54% (calculated from exact counts).
Zero exclusions, categorical changes, or changes to observed numeric values; IDs and source order preserved.
TotalCharges: 7,032 numeric strings converted to numbers; 11 whitespace-only values converted to missing.
No imputation or capping. All 11 missing totals remain flagged and are included in churn denominators.
MonthlyCharges coverage: 7,043/7,043; TotalCharges coverage: 7,032/7,043. Charge summaries must disclose coverage.
Category frequencies, no-service states, numeric ranges, and service-dependency checks preserved; zero service flags.

| Band partition | Customers | Churners | Missing group |
|---|---|---|---|
| tenure_band | 7043 | 1869 | 0 |
| monthly_charge_band | 7043 | 1869 | 0 |

Reload passed with UTF-8, literal string loading, explicit numeric conversion, and restored ordered categories.
Only empty TotalCharges fields become numeric missing values; unknown categorical or band labels are not accepted.
Text/ID order, labels, flags, category order, and missingness match; numeric tolerance: rtol=1e-12, atol=1e-10.
Raw file checksum, raw notebook views, and reviewed raw dictionary bytes remain unchanged.

Reproduce: use the project .venv kernel, restart it, run the main notebook top to bottom, inspect and save aggregate outputs.
The notebook recreates the customer CSV, prepared-data dictionary, and this report using project-relative paths.
Repeatability evidence: see [analysis_log.md](analysis_log.md#step-3-final-reconciliation-export-and-phase-3-handoff).

Limitations: provenance/licence, business grain, churn event/window, tenure units, and charge currency remain unverified.
No monthly trends, causal claims, intervention outcomes, or verified lost-revenue claims follow from preparation.

Next: Phase 3 exploratory analysis and SQL validation; choose/document a SQL engine and loading steps.
Use the same schema, filters, null rules, and denominators; reconcile SQL with pandas before interpreting metrics.
Check band sensitivity, group sizes, and uncertainty; export reviewed charts and record question-to-evidence findings.
