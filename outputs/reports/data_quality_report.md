# Phase 1 Data Quality Report

**Reviewed:** 6 November 2026 (Asia/Calcutta)

**Decision:** Ready for Phase 2 preparation of the supplied snapshot, with the limitations below carried forward. Phase 1 is complete because the business scope, audit evidence, and each issue's treatment or unresolved status are documented. This does not verify the original source, licence, field semantics, or operational suitability.

## Evidence and input identity

- Input: `datasets/TelecomChurnData.csv`, preserved unchanged.
- SHA-256: `88be4b93fbe0cc83421af1c503794c97c342eca914c1576db7c276e61d61358a`.
- Evidence: [executed notebook](../../notebooks/churnprediction.ipynb), Phase 1 Steps 2.1-2.7; [audit log](analysis_log.md); [data dictionary](data_dictionary.csv); [analysis brief](analysis_brief.md).
- Environment: Python 3.14.6, pandas 3.1.1, NumPy 2.5.4. UTF-8 comma-delimited input; inferred and literal string views distinguish pandas nulls from blank text. The notebook records the exact execution time and loading settings.
- Reproduction: open the notebook in VS Code, select the project `.venv` kernel, restart it, run all cells in order, inspect outputs, and save. All manipulation and future preparation remain in notebook cells.
- Step 3 verification: a temporary Python script executed the seven existing code cells twice in fresh IPython namespaces, checked independent CSV totals and local report links, and refreshed the saved aggregate outputs without changing analytical source. Dictionary bytes and raw checksum were unchanged on both runs. The script was deleted after checks; no permanent runner is required.

## Data suitability and audit summary

| Area | Reviewed result | Interpretation |
|---|---|---|
| Shape/schema | 7,043 rows and 21 fields; all expected fields present, no extras or duplicate header names | Suitable structure for the planned snapshot analysis |
| Observed grain | 7,043 distinct nonblank `customerID` values; zero exact duplicates, repeated IDs, conflicting IDs, or normalisation collisions | One row per observed identifier; whether this means a person, account, or subscription remains unverified |
| Outcome | 1,869 `Yes`, 5,174 `No`; zero null, blank, or unexpected labels | All 7,043 records meet the documented ID/label eligibility rule; recorded churn prevalence is 26.54% |
| Nulls/blanks | No pandas-inferred nulls, literal empty strings, or nonblank NA tokens parsed as null; 11 whitespace-only `TotalCharges` values | Standard null checks alone miss the blank totals |
| Numeric validity | No nonblank parse failures, non-finite tokens, negative values, or fractional tenure; TotalCharges has 7,032 finite values | Numeric conversion is feasible while preserving unknown totals |
| Numeric ranges | Tenure 0-72; MonthlyCharges 18.25-118.75; finite TotalCharges 18.8-8,684.8 | Units/currency and domain upper bounds remain unknown; these are observed ranges |
| Review flags | No values outside the 1.5-IQR fences; no finite TotalCharges below MonthlyCharges | These diagnostic checks do not prove business validity |
| Category/service checks | No category normalisation collisions, unexpected service states, or phone/internet dependency mismatches | Retain legitimate no-service states; source-allowed vocabulary remains unverified |
| Field coverage | Dictionary covers all 21 fields with observed types, values/ranges, missingness, roles, and source-semantic caveats | Raw observed metadata is complete; source definitions remain open |
| Preservation/repeatability | Notebook assertions check both raw views and source checksum; repeated execution reproduced the dictionary byte-for-byte | No raw-data modification or prepared customer table was needed to finish Phase 1 |

The 11 blank totals occur on all 11 zero-tenure records and nowhere else. This is an association, not proof that their accumulated charge should be zero. Financial summaries involving TotalCharges have 7,032/7,043 observed-value coverage (99.84%). MonthlyCharges is complete for all 7,043 records, including the 1,869 labelled churners.

## Issue register and decisions

Row denominators are 7,043 unless stated otherwise. Issues can overlap: DQ01 and DQ02 concern the same 11 records, while source limitations affect the dataset as a whole. Never sum these rows as separate exclusions.

| ID | Classification / fields | Evidence and affected population | Impact | Phase 2 treatment or unresolved action | Validation needed / status |
|---|---|---|---|---|---|
| DQ01 | Verified representation issue: `TotalCharges` | Raw dtype is text; 7,032 numeric strings and 11 whitespace-only values | Arithmetic on raw text is unsuitable | Convert in a separate prepared table; strip numeric text and represent blanks as missing. Preserve all rows | Exactly 7,032 finite numeric totals and 11 missing totals; no nonblank conversion failures. Treatment specified; not implemented |
| DQ02 | Unresolved meaning: zero tenure and blank totals | Same 11 records (0.1562%); all have tenure zero | Zero imputation would assert an unsupported business meaning | Retain zero tenure and unknown totals; flag missing totals; request billing/tenure definitions before considering another rule | No zero, mean, median, or formula imputation; check missingness and record counts. Cause remains unresolved |
| DQ03 | Legitimate service states | 682 `No phone service` values in MultipleLines; 1,526 `No internet service` values in each of six related fields | Mapping these to null would lose subscription information | Retain exact states and repeat dependency checks; no blanket missing-value recoding | State counts unchanged; zero dependency flags. Decision complete |
| DQ04 | Open provenance/usage: whole dataset | Original URL, provider, acquisition date, version, and licence absent from reviewed documentation | Source attribution and reuse terms cannot be asserted | Obtain the exact acquisition reference and applicable terms from the supplier; retain an unresolved note until evidence is available | Match evidence to this exact file/version. Open; does not prevent technical preparation |
| DQ05 | Open business grain/outcome/population: all IDs and `Churn` | Unique IDs and Yes/No coding verified; customer/account meaning, churn event, window, collection period, and real/synthetic status unknown | Limits population generalisation and operational interpretation | Use one record per observed ID and describe recorded-label prevalence; request original definitions | No monthly churn, future-window, acquisition-cohort, or current-customer-base claims. Open limitation |
| DQ06 | Open units/timing/domain bounds: tenure and charges | Ranges plausible under common interpretations, but no source units, currency, billing basis, or upper bounds verified | Unit-specific labels, billing conclusions, and loss estimates would be unsupported | Keep original field names; label tenure as recorded units and charges as currency unverified; do not cap values from distribution alone | Preserve finite values and ranges; attach source-supported definitions when available. Open limitation |
| DQ07 | Absent dated and intervention fields | No observation/cancellation dates, campaign assignment, or campaign outcomes in the header | Trends, causes, realised retention effects, and operational early warning cannot be validated | Restrict this dataset to descriptive associations and later exploratory classification if feature timing stays unknown; obtain operational data for pilots | No causal or campaign-impact claims; learned preprocessing remains Phase 5. Open scope limitation |

No duplicate, invalid-identifier, invalid-label, category-collision, numeric-malformation, or service-conflict issue was observed. No row exclusions or outlier removals are justified by this audit.

## Phase 2 preparation rules

Implement these rules in new Phase 2 cells in the existing notebook, using a copy of the raw data. The current dictionary describes the raw file and should remain an audit record; document any derived fields separately.

| Field or issue | Preparation rule | Expected effect for this input |
|---|---|---|
| Identifier | Preserve identifier spelling and case. Require a nonblank ID without outer whitespace and one resolved record per ID. Hold ambiguous future records for investigation rather than arbitrary deduplication | Retain 7,043 IDs unchanged; no deduplication |
| Text categories | Inspect before any standardisation; trim only justified incidental whitespace in the prepared copy, log actual changes, and retain no-service states. Do not casefold or merge meaningful categories without a mapping | No categorical changes expected |
| Numeric fields | Convert tenure, MonthlyCharges, and TotalCharges explicitly; preserve finite values. Treat nonblank conversion failures/non-finite tokens as review failures, not silent missingness | Tenure stays whole-valued; MonthlyCharges complete; TotalCharges becomes numeric with 11 missing values |
| Missing charges | Keep the 11 totals missing and add a clearly documented missing-total flag; keep missing charge values separate from zero | 11 flags; no imputation or record loss |
| Outcome | Map exact `Yes` to 1 and `No` to 0 in a derived churn indicator. Never map missing or unknown outcomes to 0 | 1,869 ones and 5,174 zeros |
| Eligibility | Include unique, usable IDs with valid labels. Investigate and document future conflicts or invalid labels; report exclusion reasons and their union | All 7,043 included; zero exclusions |
| Bands | Choose interpretable tenure and MonthlyCharges boundaries in Phase 2, record them before comparisons, use unverified-unit labels, and include explicit missing groups where needed | Every eligible record assigned once per partition; check sensitivity to boundaries in Phase 3 |
| Export | Produce the prepared customer table under `outputs/data/` from notebook cells after validations pass; document filename and field types | Same customer IDs and churn totals across Python, SQL, and Power BI |

Do not fit imputers, scalers, encoders, or models during preparation for descriptive analysis. Learned preprocessing belongs inside training pipelines in Phase 5.

## Metric and comparison contract

- Customer and churn-rate denominators use eligible unique IDs with valid labels, including customers whose totals are missing. For this file N = 7,043 and C = 1,869; C/N = 26.536987%, displayed as 26.54%. This is a validated count calculation for the supplied labels, with business meaning still provisional.
- Segment churn rate is C_s/N_s; share of all churners is C_s/C; the percentage-point gap is 100 x (C_s/N_s - C/N). Keep the overall baseline fixed to the same eligible population unless an explicit analysis filter changes it. Undefined denominators produce unavailable values, never zero.
- Show missing segment values as a separate group. Report customer count and churn count alongside every rate. Sum segment totals only for mutually exclusive partitions; overlapping priority groups are not additive.
- As an analyst-selected reporting convention, flag groups with fewer than 30 eligible customers as small and avoid ranking them as stable opportunities from the rate alone. This is not a significance or precision guarantee. Phase 3 should show 95% Wilson intervals for key rates and inspect sensitivity to the convention where it affects priorities.
- Charge sums use finite observed amounts without imputing unknowns; disclose valid/missing/invalid counts and coverage. For MonthlyCharges, coverage is 100% in the observed file; its sum among churners remains a billing exposure proxy, not verified lost revenue. For TotalCharges, coverage is 99.84% overall and must be recalculated within each filtered group. Report unavailable if no valid charge values exist.
- Preserve exact counts; calculate from unrounded values and display rates/gaps to two decimal places consistently across tools. Financial currency remains unverified.

## Handoff reconciliation and readiness

| Phase 2 acceptance check | Expected result for this input |
|---|---|
| Raw source identity | Same SHA-256 before and after preparation |
| Raw/prepared rows and unique IDs | 7,043 rows and 7,043 unique IDs; exact identifier set retained |
| Labels and indicator | 1,869 Yes/ones, 5,174 No/zeros; zero unknown outcomes |
| Exclusions and alterations | Zero exclusions; log actual field changes, numeric conversions, and added fields separately |
| Missing charges | MonthlyCharges: 0 missing; TotalCharges: 11 missing, all at zero tenure; missing flag total 11 |
| Categories and service dependencies | Original category frequencies retained; zero dependency flags |
| Numeric values | Finite ranges preserved; no nonblank parse failures or non-finite values; no unsupported imputation/capping |
| Bands and metrics | Band counts reconcile to 7,043; churn counts reconcile to 1,869; headline rate remains 26.54% |
| Reproduction | Fresh-kernel Run All from unchanged raw input reproduces the prepared table and reconciliation evidence |

There is no observed data-quality blocker to deterministic preparation under these rules. Source provenance/licence, business definitions, and units remain unresolved and constrain attribution, reuse claims, interpretation, and operational deployment. Additional operational evidence is required for real retention pilots; those limitations do not prevent completing the documented Phase 1 audit.

**Completion review:** Analysis brief, observed dictionary, quality report, and log are linked; grain and outcome coding are distinguished from unknown business definitions; every issue has a treatment or unresolved action; preparation rules, metric denominators, expected counts, and readiness are explicit. Phase 2 implementation has not started.
