# Analysis working log

Status: Phases 1 and 2 complete. Phase 2 Step 1 completed on 7 November; Steps 2 and 3 completed and verified on 10 November 2026. Final export/reload and repeatability checks passed. Ready for Phase 3 exploratory analysis and SQL validation. Documented source limitations still apply.

## Phase 1: Business brief and quality audit

### Step 1: Business framing and dataset context

Completed on 6 November 2026 (Asia/Calcutta). See the [analysis brief](analysis_brief.md) for the question-to-evidence matrix, metric definitions, context register, and open questions OQ1-OQ8.

- Stakeholders: retention managers, customer experience teams, and business stakeholders.
- Decision: which segments to investigate first, which retention actions to test, and how to measure the results.
- Evidence reviewed: project README, dataset README, provisional dictionary, notebook code, and raw CSV header. No audit results or business findings are asserted from code inspection.
- Confirmed structure: the input has `customerID`, `Churn`, and the fields required for the planned descriptive questions. The header has no observation dates, cancellation dates, or campaign outcomes.
- Intended grain: one customer per row. The meaning and uniqueness of `customerID` remain provisional pending source clarification and Step 2 checks (OQ4).
- Expected labels: `Yes` and `No`, matching current notebook logic. Observed label validity and the operational churn definition/window remain unverified (OQ3, OQ6).
- Provenance: original URL/reference, provider, acquisition date, licence, collection context, and field units are not verified by the reviewed repository documentation (OQ1, OQ2, OQ5). Obtain original source records; a familiar schema does not establish provenance.
- Proposed metric policy: count unique eligible customers with resolved records and valid labels. Report missing/unexpected outcomes separately; do not map them to retained customers. Missing predictor or charge values alone do not change overall churn eligibility. Charge proxies must disclose valid-value coverage.
- Interpretation: recorded snapshot prevalence, associations, and proposed tests only. No monthly trends, acquisition cohorts, causal effects, verified lost revenue, or future-window predictions are established. Sensitive characteristics alone will not justify targeting.
- Next: run the Step 2 audit, validate eligibility and field meanings, and document segment-size reporting rules. Later outreach and pilot assumptions require operational input (OQ7, OQ8).

Step 1 completion checks were met through documented evidence and explicit unresolved notes. Subsequent Steps 2 and 3 below record the audit and final review.

### Step 2: Audit evidence

Executed on 6 November 2026 (Asia/Calcutta) using Python 3.14.6, pandas 3.1.1, and NumPy 2.5.4. See the executed [notebook](../../notebooks/churnprediction.ipynb) for field-level completeness, category frequencies, numeric checks, and preservation assertions. The original seven code cells were first reorganised into 19 beginner-friendly cells and now into 43 single-purpose cells with the same audit results; see the readability review below. The [data dictionary](data_dictionary.csv) covers all 21 fields with observed types, values/ranges, missingness, roles, units/coding notes, and verification status. Source semantics remain explicitly unverified.

**Input:** `datasets/TelecomChurnData.csv`

**SHA-256:** `88be4b93fbe0cc83421af1c503794c97c342eca914c1576db7c276e61d61358a`

Loading uses UTF-8, comma delimiter, and the first row as header. The inferred view uses pandas defaults. The literal string view disables NA parsing (`dtype="str", keep_default_na=False, na_filter=False`) to preserve empty strings, whitespace, and NA-like tokens. Neither view is cleaned. Counts below use 7,043 raw rows unless otherwise stated; flag populations can overlap.

| Check | Observed result | Affected rows | Follow-up or limitation |
|---|---|---|---|
| Shape and schema | 7,043 rows, 21 fields; no missing/unexpected fields or duplicate header names against the dictionary | 0 schema deviations | Expected schema is repository-defined, not a verified source contract |
| Identifier completeness | 7,043 distinct nonblank IDs; no inferred null, empty, whitespace-only, or outer-whitespace IDs | 0 flagged | Observed identifier integrity passes; person/account/subscription meaning remains open |
| Duplicate records | No excess copies or participating duplicate rows | 0 | Literal full-row comparison |
| Repeated/conflicting identifiers | No repeated ID groups or rows; no conflicting ID groups or rows | 0 | No duplicate resolution required for this file |
| Normalised identifier collisions | No distinct IDs collapse after strip/casefold | 0 collisions | Diagnostic only; identifiers not modified |
| Inferred nulls and empty strings | Zero inferred nulls, literal empty strings, and nonblank NA-like tokens parsed as null in every field | 0 per field | This does not include whitespace-only strings |
| Whitespace-only values | `TotalCharges` contains 11 whitespace-only values; no other field contains blanks or outer whitespace | 11 (0.1562%) | Preserve as unknown pending Step 3 treatment review; default null checks alone miss these values |
| Category consistency | All observed categorical values and frequencies are in the notebook/dictionary; zero outer-whitespace rows and strip/casefold collision groups | 0 per categorical field | Observed vocabulary is not proof of source-allowed vocabulary |
| SeniorCitizen coding | `0`: 5,901 rows; `1`: 1,142 rows; no unexpected codes | 0 unexpected | Source definition of the flag remains unverified |
| Numeric conversion | `tenure` and `MonthlyCharges` parse throughout; `TotalCharges` has 7,032 finite values and 11 blanks; no nonblank parse failures or non-finite tokens | 11 blank totals; 0 malformed values | Numeric conversion is diagnostic; raw `TotalCharges` remains text |
| Numeric ranges | Tenure: 0-72; MonthlyCharges: 18.25-118.75; TotalCharges: 18.8-8,684.8 among finite values | 0 negative values; 0 fractional tenure values | Units/currency and source-supported upper bounds remain unverified; fractional charges are legitimate |
| Distributional review | No finite numeric values outside the documented 1.5-IQR fences in any of the three numeric fields | 0 per field | Distributional flags are not domain validity guarantees |
| Blank totals by tenure | All 11 zero-tenure rows have blank TotalCharges; no blank totals at other tenure values | 11 | Relationship observed; cause and whether zero would be correct remain unknown |
| Charge relationship review | No finite TotalCharges below MonthlyCharges | 0 | Comparison is a review flag, not a verified billing rule |
| Service vocabulary and dependencies | No unexpected service values or phone/internet dependency mismatches in any documented check | 0 | Preserve legitimate no-service states; rules remain subject to source definitions |
| Structural no-service states | `No phone service`: 682 MultipleLines rows; `No internet service`: 1,526 rows in each of six internet-dependent service fields | Valid states; not missing | These six populations overlap and must not be summed |
| Outcome labels | `Yes`: 1,869; `No`: 5,174; no missing, blank, or unexpected labels | 0 invalid labels | Operational churn definition/window remains unknown |
| Provisional eligibility | 7,043 eligible unique customers; 0 excluded by the audit's ID/label rule | 0 exclusions | Require nonblank ID without outer whitespace, no repeated ID, and exact Yes/No label |
| Provisional churn prevalence | 1,869 / 7,043 = 26.536987%, displayed as 26.54%; retained: 5,174 | All 7,043 eligible customers | Recorded-label prevalence only; not monthly churn or a future-period forecast |
| Source preservation | SHA-256 unchanged before/after execution; both in-memory raw views equal their snapshots | 0 source changes | No imputation, capping, cleaning, or customer-level export performed |

**Reproduction:** Open `notebooks/churnprediction.ipynb` in VS Code, select the project's `.venv` kernel, restart the kernel, and run all cells from top to bottom. Review results and save the notebook to retain its outputs. The final audit cells read the reviewed dictionary, check field coverage, and confirm source preservation without exporting files. During the original Step 2 verification, a second execution reproduced the dictionary byte-for-byte; the latest simplified notebook leaves this reviewed artifact unchanged.

**Workflow preference (6 November 2026):** The user prioritises a notebook-first data science workflow, with data manipulation and analysis implemented directly in `.ipynb` cells and minimal separate Python scripts. The optional permanent external runner was removed. Users can run the notebook interactively; agent verification uses temporary Python scripts that are deleted after checking outputs, without computer control. Existing audit code and evidence remain in the notebook. See [project priorities](../../AGENTS.md).

**Step 2 completion:** All 21 fields are covered and every planned audit area has recorded evidence, including zero-issue checks. Observed grain and label coding support a provisional baseline. Source provenance, licence, business grain, field units, and churn definition/window remain open; no unsupported source claims were added.

### Step 3: Quality review and Phase 2 readiness

Completed on 6 November 2026. The [quality report](data_quality_report.md) consolidates issues DQ01-DQ07, their evidence and overlap, proposed preparation rules, metric definitions, validation checks, and the readiness decision. Reviewed the analysis brief and dictionary against the audit and brought their interpretation notes into agreement.

- **Treatments:** Retain all 7,043 records and original identifiers. In Phase 2, convert TotalCharges in a prepared copy and keep the 11 whitespace-only values missing, with a missing-total flag. Do not infer zero from the zero-tenure association. Retain legitimate no-service categories. No cleaning or row exclusions were performed during Phase 1.
- **Metric eligibility:** All 7,043 observed IDs have valid labels; counts are 1,869 Yes and 5,174 No, giving recorded-label prevalence of 26.54%. Charge completeness does not affect churn denominators. Missing segment values must be shown explicitly and charge summaries must disclose valid-value coverage.
- **Reporting choice:** Flag segment N < 30 as an analyst-selected convention, not a guarantee of precision. Phase 3 will report 95% Wilson intervals for key rates and assess sensitivity where the convention affects priorities. Rates and percentage-point gaps use two display decimals.
- **Verification:** A temporary Python script executed all seven existing notebook code cells twice in fresh IPython namespaces. Both executions passed the notebook assertions and aggregate checks; the data dictionary was identical byte-for-byte, and raw SHA-256 stayed unchanged. Independent standard-library CSV checks reconciled IDs, labels, and the 11 blank totals. Saved aggregate outputs contain no individual IDs. The temporary script was deleted after verification.
- **Workflow clarification:** Verification uses temporary scripts instead of computer control; permanent analytical logic stays in the notebook. An older open notebook buffer was preserved separately in the ignored `.ipynb_checkpoints/churnprediction-before-step3.ipynb` before verification continued. No computer control was used for the final checks.
- **Open limitations:** Source URL/provider/version, licence/acquisition details, population/collection context, business grain, churn event/window, and units remain unverified. The absence of dates and intervention outcomes limits trends, future-window prediction, and causal claims. Each limitation has a follow-up action in the brief/quality report.
- **Readiness:** Ready for deterministic Phase 2 preparation of this supplied snapshot. No observed quality issue blocks those preparation rules. This decision does not establish source usage terms or operational readiness.

**Phase 2 acceptance checks:** Preserve raw SHA-256 and the exact set of 7,043 IDs; retain 1,869 Yes/ones and 5,174 No/zeros; expect zero exclusions, zero missing MonthlyCharges, and 11 missing TotalCharges/missing-total flags. Preserve categories, service consistency, and finite numeric ranges. Reconcile bands and exported tables to the same customer and churn counts. Document actual transformations and export paths in the notebook and log.

**Phase 1 completion:** Business scope, data grain, label coding versus unresolved outcome meaning, quality evidence, issue treatments, limitations, and Phase 2 acceptance criteria are documented. Phase 2 implementation has not started.

### Phase 1 readability review

The user requested Python written from the perspective of a senior software engineer with strong SQL, business judgement, and planning skills who is new to Python libraries and data engineering. This preference is recorded in the consolidated [project rules and priorities](../../AGENTS.md).

- Split seven large code cells into 19 focused cells, each with a short explanation of its purpose. Introduced DataFrame, Series, Boolean masks, groupby, and dictionary updates using SQL comparisons.
- Replaced the compact path search, nested conditional expressions, comprehensions, and dense checks with explicit variables, basic loops, and intermediate steps. Reuse the dictionary's existing descriptions and caveats instead of rebuilding metadata definitions during each audit.
- Preserved every audit area, eligibility rule, privacy constraint, and business interpretation. Phase 1 remains complete; Phase 2 preparation is still planned.
- A temporary regression script executed the committed original notebook and the simplified notebook, then compared identifier, missingness, category, numeric, service, outcome, eligibility, and dictionary results. All matched. A second fresh execution of the simplified notebook passed; independent CSV counts agreed. Raw CSV and dictionary bytes were unchanged, and saved outputs contain no customer IDs. The temporary script was deleted after verification.

**Further simplification:** Removed automatic dictionary regeneration and custom category-frequency table building. The dictionary is now read-only documentation; category counts are displayed directly with value_counts(). Missingness summaries use standard pandas column operations, category spelling checks start from distinct values, and the blank-total investigation displays only tenure groups containing blanks. Service failure counts use a direct column sum. These changes remove 41 code lines while retaining all audit checks and results.

A temporary check compared this version with the preceding 19-cell version across all audit results and per-category counts, then executed it again from a fresh namespace. Both runs passed. Independent CSV checks confirmed the central counts; raw data and dictionary bytes stayed unchanged. The temporary script and its baseline snapshot were deleted after verification.

**Guided cell layout:** Split the 19-cell notebook into 43 code cells, with a maximum of 15 lines per cell. Every new cell has a Markdown explanation of its purpose and the intermediate Python/pandas operations it uses. Separated schema preparation/comparison/display, ID completeness/duplicates/conflicts, missingness summaries, numeric validity/ranges, service setup/dependencies/results, and eligibility/counts/rate/reconciliation. Kept loops and conditionals intact; numeric validity and range checks use separate focused loops. Later-phase plans and all audit rules remain intact.

A temporary regression check compared all audit tables, masks, category counts, and baseline measures before and after the split. Results matched exactly. Two fresh runs of the 43-cell notebook passed; independent CSV counts, unique cell IDs, aggregate-only outputs, the raw checksum, and unchanged dictionary bytes were verified. Saved outputs reflect the new cell order. The temporary script and baseline snapshot were deleted.

## Phase 2: Cleaning decisions

### Step 1: Prepared copy and documented cleaning rules

Completed and verified on 7 November 2026 (Asia/Calcutta). Implementation is in `notebooks/churnprediction.ipynb`, Phase 2 Step 1: 14 focused code cells, each at most 15 lines, with Markdown explanations. `prepared_df` is a separate in-memory table with the original 21 fields in source order. No files or customer-level records are exported by these cells.

| Field or issue | Evidence | Implemented rule and rationale | Rows affected | Validation |
|---|---|---|---|---|
| Source identity and raw views | Phase 1 checksum and snapshots | Require the reviewed checksum before preparation; preserve raw views and dictionary bytes | 0 source changes | SHA-256 remains `88be4b93fbe0cc83421af1c503794c97c342eca914c1576db7c276e61d61358a`; both raw views and dictionary bytes unchanged |
| IDs, duplicates, and labels | Unique nonblank IDs without outer whitespace; exact Yes/No labels | Stop on invalid IDs, repeated IDs, or unexpected labels; no arbitrary deduplication or silent exclusions | 0 altered IDs; 0 exclusions | Exact ID values and order retained; 7,043 unique IDs; 1,869 Yes and 5,174 No |
| Text categories | Zero outer-whitespace changes in audited categorical fields | Inspect trim counts before assignment; preserve spelling, case, and category meanings; stop if new whitespace needs review | 0 categorical value changes | Row-by-row equality with raw categories; SeniorCitizen retains original integer coding |
| Tenure | 7,043 valid whole numeric values | Explicit conversion from literal text to whole integers; reject negative or fractional values | 7,043 values validated and assigned; 0 numerical changes | No missing values, parse failures, or non-finite values; range 0-72 retained |
| MonthlyCharges | 7,043 valid numeric values | Explicit numeric conversion; preserve values without imputation or capping | 7,043 values validated and assigned; 0 numerical changes | 0 missing; range 18.25-118.75 retained |
| TotalCharges (DQ01/DQ02) | 7,032 numeric strings and 11 whitespace-only totals | Convert numeric text; represent known blank totals as missing numeric values, without imputing zero | 7,032 text-to-number conversions and 11 blank-to-missing conversions; 0 changes to observed numerical values | Exactly 11 missing totals, matching the raw blank mask row by row; all at zero tenure; observed range 18.8-8,684.8 retained |
| No-service states (DQ03) | 682 No phone service states; 1,526 No internet service states per dependent field | Retain legitimate states; repeat vocabulary and dependency checks on the prepared copy | 0 state changes | Zero service flags; state counts retained across all six internet-dependent fields |

Counts refer to different field-level operations and must not be summed as distinct affected customers. The 11 blank-total records overlap the 11 zero-tenure records; none is excluded. Numeric assignment is distinct from changing an observed numerical value.

**Verification:** The project `.venv` ran successfully with execution access to its base interpreter; no environment repair or dependency change was needed. Python 3.14.6, pandas 3.1.1, and NumPy 2.5.4 were used. A temporary script executed all 57 notebook code cells twice in fresh IPython namespaces. Both runs passed, and the prepared tables matched exactly, including types and order. Independent standard-library CSV counts agreed with 7,043 IDs, 1,869 Yes labels, and 11 blank totals. Raw CSV and dictionary bytes remained unchanged. Saved aggregate outputs contain no customer identifiers. Additional checks introduced an unknown churn label, malformed TotalCharges text, and infinity into isolated copies; each was rejected by an assertion before numeric assignment. Temporary editing and verification scripts were removed after verification.

**Step 1 result:** 7,043 rows and 21 columns; zero exclusions; 11 missing totals; all observed numeric values, identifier order, labels, categories, and service relationships preserved. These missing charges do not alter churn denominators. Provenance, usage conditions, business grain, churn window, and field units remain unresolved.

**Step 1 handoff:** Step 2 below adds the churn indicator, missing-total flag, analytical bands, and prepared-data dictionary. Step 3 remains planned; Phase 2 is not yet complete.

### Step 2: Derived fields, bands, and prepared-data dictionary

Completed and verified on 10 November 2026 (Asia/Calcutta). Thirteen focused notebook code cells append four fields to `prepared_df`, resulting in 7,043 rows and 25 columns. The original 21 cleaned fields, their types, and identifier order remain unchanged. No customer-level table has been exported.

| Field | Rule and rationale | Validation result |
|---|---|---|
| `churn_flag` | Exact Yes -> integer 1; No -> integer 0; reject unknown labels first | 1,869 ones; 5,174 zeros; no missing values |
| `total_charges_missing` | Integer 1 for a missing prepared TotalCharges value; 0 otherwise; no imputation | 11 ones; exact row-by-row agreement with missing totals |
| `tenure_band` | Fixed ordered labels: 0; 1-12; 13-24; 25-48; 49+; Missing | Zero has its own group; remaining intervals include their upper edge; all records assigned |
| `monthly_charge_band` | Fixed ordered labels: Below 35; 35 to below 70; 70 to below 100; 100+; Missing | Lower edges inclusive, upper edges exclusive; all records assigned |

The fixed boundaries were specified in the preparation plan before segment churn comparisons. They are analyst conventions, not source-verified business thresholds. Tenure uses recorded units; charge currency remains unverified. The missing groups are defined explicitly and currently contain zero records. Negative/non-finite observed inputs and fractional tenure fail validation. Phase 3 will check sensitivity to reasonable alternative boundaries before interpreting priorities.

**Assignment reconciliation (not a ranking or business finding):**

| Partition | Band | Customers | Churners |
|---|---|---|---|
| Tenure | 0 | 11 | 0 |
| Tenure | 1-12 | 2,175 | 1,037 |
| Tenure | 13-24 | 1,024 | 294 |
| Tenure | 25-48 | 1,594 | 325 |
| Tenure | 49+ | 2,239 | 213 |
| Tenure | Missing | 0 | 0 |
| MonthlyCharges | Below 35 | 1,731 | 188 |
| MonthlyCharges | 35 to below 70 | 1,721 | 407 |
| MonthlyCharges | 70 to below 100 | 2,683 | 1,017 |
| MonthlyCharges | 100+ | 908 | 257 |
| MonthlyCharges | Missing | 0 | 0 |

Each partition separately totals 7,043 customers and 1,869 churners; do not add the two partitions together. All eligible IDs and valid labels stay in churn denominators, including customers with missing totals. Keep exact counts and unrounded calculations, show missing groups explicitly, and disclose charge coverage separately. Overlapping segments must not be summed as distinct customers.

**Metadata deliverable:** [prepared_data_dictionary.csv](prepared_data_dictionary.csv), exported from the Step 2 notebook cells with UTF-8 and no index. It describes all 25 fields, actual pandas types, reused source descriptions and observed values/ranges, transformations, null handling, interval edges, category order, and interpretation caveats. The reviewed raw dictionary is unchanged. Observed vocabulary is not a verified source-wide list of permitted values. SQL and Power BI must apply this schema and order explicitly because CSV does not retain pandas types or ordered categories.

**Verification:** A temporary script executed all 70 notebook code cells twice in fresh IPython namespaces using the existing project environment. Prepared tables matched exactly, including types, categories, and row order; exported dictionary bytes matched between runs and reloaded metadata matched the in-memory dictionary. Independent standard-library CSV calculations agreed with each band's customer counts. Tests ran the actual notebook band cells on zero, interval edges, adjacent values, higher values, and missing inputs. Exact 35/70/100 charges entered their next band; exact 12/24/48 tenure entered their upper-inclusive band. Negative/infinite inputs, fractional tenure, and an unknown churn label were rejected. Source bytes, raw dictionary bytes, raw views, and the 21 cleaned source columns remained unchanged. Saved outputs contain aggregate results without customer identifiers. Temporary editing and verification scripts were removed after checking.

**Step 2 handoff:** Step 3 below completes final reconciliation, export/reload verification, the validation summary, and Phase 3 handoff. No learned preprocessing, models, or Phase 3 business conclusions were added in Step 2.

### Step 3: Final reconciliation, export, and Phase 3 handoff

Completed and verified on 10 November 2026 (Asia/Calcutta). Eleven focused code cells in the main notebook complete preparation without separate permanent Python helpers. All new cells have Markdown explanations and at most 15 lines. The analytical table is exported only after source-field, count, flag, partition, and preservation checks pass.

| Deliverable | Contents and verification |
|---|---|
| `outputs/data/customer_analytical.csv` | UTF-8, header, no pandas index, fixed source row order and 25 fields; 11 unknown totals stored as empty fields; generated customer-level data ignored by Git |
| [prepared_data_dictionary.csv](prepared_data_dictionary.csv) | All 25 fields, types, transformations, null rules, exact band edges/order, and interpretation caveats; unchanged by the Step 3 verification |
| [phase_2_validation_summary.md](phase_2_validation_summary.md) | Notebook-generated aggregate reconciliation, input/export hashes, environment, acceptance results, reload rules, limitations, and next-phase tasks |

**Final results:** 7,043 rows and unique IDs, 1,869 Yes/ones, 5,174 No/zeros, 26.54% recorded-label prevalence, zero exclusions, zero categorical changes, zero changes to observed numeric values. TotalCharges representation conversion accounts for 7,032 numeric strings and 11 blank-to-missing values. MonthlyCharges has zero missing values; all 11 missing totals remain flagged and included in churn denominators. No imputation or capping occurred. Both band partitions reconcile to 7,043 customers and 1,869 churners, with explicit empty Missing groups. Source row order, raw views, raw file checksum, reviewed dictionary bytes, and all cleaned source columns remain unchanged.

**Loading contract:** Start with literal string loading (UTF-8, `dtype=str`, `keep_default_na=False`, `na_filter=False`) to preserve IDs and category spelling. Replace only empty TotalCharges fields with numeric missing values, then explicitly convert the three numeric measures, SeniorCitizen, and the two integer flags using `errors="raise"`. Reject labels outside the documented band vocabulary before restoring ordered categoricals. Retain no-service states. Verify the complete table, IDs/order, flags, and missingness against preparation. Floating-point CSV comparisons permit only `rtol=1e-12`, `atol=1e-10`; text and categories remain exact.

**Repeatability verification:** A temporary script executed all 81 notebook code cells twice in fresh IPython namespaces using Python 3.14.6, pandas 3.1.1, and NumPy 2.5.4. Both executions passed. Prepared tables matched exactly, including types/order, and all three generated artifacts matched byte for byte. Independent standard-library CSV checks reconciled 7,043 rows, 25 fields, exact raw identifier order, labels/flags, and 11 physically empty totals. Isolated reload checks rejected an altered ID, an unknown tenure-band label, a changed missingness flag, and replacement of a missing total with zero. Aggregate outputs contain no customer identifiers. Temporary editing and verification scripts were removed after checking; analytical logic and exports remain in notebook cells.

**Input SHA-256:** `88be4b93fbe0cc83421af1c503794c97c342eca914c1576db7c276e61d61358a`.

**Analytical export SHA-256:** `b27a6d5a9aa9cf730090a61c5d4779bdc7de15fff5f7dd2eed2d8c2948f374f2`.

**Completion decision:** Phase 2 is complete. A fresh run from the unchanged raw file recreates the analytical table, metadata, and validation evidence; every alteration and exclusion is accounted for. Source provenance/licence, business grain, churn event/window, tenure units, and charge currency remain unresolved and constrain interpretation. These checks do not establish future-window prediction, causal effects, or operational readiness.

**Phase 3 handoff:** Recreate the customer CSV by running the notebook if absent after checkout. Select/document a SQL engine and import schema using the prepared-data dictionary; translate empty totals to SQL NULL without changing denominators. Establish baseline and segment metrics, examine customer mix, report group sizes and uncertainty, and check band sensitivity. Reconcile SQL with pandas under identical filters/null rules before publishing charts or question-to-evidence findings. No database, SQL query implementation, dashboard, or model was added during Phase 2.

## Phase 3: Business answers and reconciliation

| Business question | Finding and customer counts | Chart or query | Caveat | Decision supported |
|---|---|---|---|---|
| To complete after analysis | Pending | Pending | Pending | Pending |

| Metric and filters | Python result | SQL result | Difference and resolution |
|---|---|---|---|
| To complete after SQL implementation | Pending | Pending | Pending |

## Phase 4: Retention recommendations

| Segment and evidence | Proposed action | Suggested owner | Success measure | Assumptions and next test |
|---|---|---|---|---|
| To complete after analysis | Pending | Pending | Pending | Pending |

For each proposed pilot, define eligibility, treatment and comparison groups, follow-up period, cost assumptions, and required operational data. Record overlap between target segments.

## Phase 5: Predictive support

Record feature availability, exclusions, split strategy, random seed, validation procedure, benchmarks, contact capacity, threshold choice, and held-out results. Keep estimated churn risk separate from expected response to an intervention.

## Phase 6: Reporting and handoff

Record dashboard reconciliation, refresh instructions, final artifact locations, and reproduction checks. Draft the executive summary only after findings are validated.
