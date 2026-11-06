# Analysis Brief: Customer Churn and Retention

**Review date:** 6 November 2026 (Asia/Calcutta)

**Status:** Phase 1 Steps 1 and 2 complete. Business framing and context uncertainties are documented here; the [analysis log](analysis_log.md) records the executed audit. Observed identifiers are unique and labels are Yes/No for all 7,043 records. Final treatment review and data suitability conclusions remain Step 3 work; source semantics remain unverified. No validated retention findings are published in this brief.

## Business decision and audience

Retention managers and customer experience teams need to decide which customer segments to investigate first, which retention actions to test, and how to measure the results. Business stakeholders need a reproducible explanation of the evidence and its limits.

The analysis will describe the scale and concentration of recorded churn, compare customer characteristics, and produce evidence for a retention priority list. Later work will translate supported patterns into proposed pilots. Predictive ranking is a supporting question for Phase 5.

Success means that every core question has a reproducible answer or an explicit data limitation; reported rates have clear customer denominators; and recommendations can be traced to evidence and a measurable test. Retention improvement cannot be established from this dataset alone.

## Questions, decisions, and required evidence

All descriptive comparisons below require validated `customerID` and `Churn`. Each segment output must show eligible customer count, churn count, churn rate, share of all churners, and the percentage-point gap from the overall rate where defined.

| ID | Business question | Decision supported | Additional required fields | Planned evidence |
|---|---|---|---|---|
| BQ1 | What proportion of eligible customers are labelled as churned? | Establish the baseline for comparisons | None | Customer count, churn count, churn rate, label eligibility and exclusion summary |
| BQ2 | Which groups contribute the most churn? | Prioritise groups with meaningful scale and elevated rates | `Contract`, `tenure`, `InternetService`, `PaymentMethod` | Segment table showing both churn volume and rate; report overlapping groups separately |
| BQ3 | Is recorded churn concentrated among newer customers? | Investigate onboarding and early engagement | `tenure` | Churned versus retained tenure distributions and documented tenure bands; verify units before labelling bands |
| BQ4 | How does churn vary by contract? | Identify contract groups for retention research | `Contract`, `tenure` | Contract comparisons overall and within tenure bands to inspect customer mix |
| BQ5 | Are higher monthly charges associated with churn? | Investigate pricing or perceived value hypotheses | `MonthlyCharges`, `Contract`, `InternetService`, service subscriptions | Charge distributions and documented charge bands within comparable contract and service groups; report missing charges |
| BQ6 | How do service combinations relate to churn? | Identify service experience hypotheses to investigate | `PhoneService`, `MultipleLines`, `InternetService`, `OnlineSecurity`, `OnlineBackup`, `DeviceProtection`, `TechSupport`, `StreamingTV`, `StreamingMovies` | Service comparisons and focused combinations, including internet service by technical support and online security |
| BQ7 | Do billing methods show different churn patterns? | Investigate billing experience | `PaymentMethod`, `PaperlessBilling`, `Contract`, `tenure` | Billing comparisons followed by checks within contract and tenure groups |
| BQ8 | Which segments warrant outreach research first? | Develop a feasible retention priority list | Segment fields above, `MonthlyCharges`; operational feasibility information still needed | Evidence table combining customer volume, rate, charge exposure coverage, segment overlap, uncertainty, and feasibility assumptions |
| BQ9 | Can a simple model improve outreach prioritisation? | Decide whether predictive ranking adds value | Candidate features with verified availability at scoring time; outreach capacity still needed | Phase 5 comparison of a segmentation rule, dummy classifier, and logistic regression at equal contact capacity; exploratory classification if timing stays unknown |

Phase 1 establishes the scope and audits suitability. Cleaning belongs to Phase 2, descriptive findings and SQL validation to Phase 3, recommendations and pilot design to Phase 4, modelling to Phase 5, and stakeholder reporting to Phase 6.

## Evidence reviewed and dataset context

Repository references reviewed on the date above:

- [Project README](../../README.md): business objectives, scope, metrics, and phase criteria.
- [Dataset README](../../datasets/README.md): raw input policy and explicitly unverified source context.
- [Provisional dictionary](data_dictionary.csv): candidate field meanings and verification notes.
- [Notebook](../../notebooks/churnprediction.ipynb): initial loading and audit logic, including expected `Yes` / `No` labels. Code inspection does not establish that its checks pass.
- `datasets/TelecomChurnData.csv`: header inspected to confirm the presence of the named fields. Full profiling remains Step 2 work.

| Context item | Current evidence or working assumption | Verification status and next action |
|---|---|---|
| Input | Local file `datasets/TelecomChurnData.csv`; preserve unchanged | File present; identify the audited version with a checksum in Step 2 |
| Source URL or reference | No original source citation is supplied in the reviewed repository documentation | Unresolved: obtain the original download reference or acquisition record from the dataset supplier; do not infer provenance from familiar column names |
| Provider | No provider is identified in the reviewed documentation | Unresolved: verify against the original source and establish that it describes this exact file |
| Source access date | Original download or access date is not recorded | Unresolved: obtain the acquisition record; the review date above is a local documentation review date |
| Licence and usage conditions | Dataset README explicitly says the licence is unverified | Unresolved: locate applicable terms for the exact source/version and record attribution or redistribution conditions |
| Collection period and observation context | No dates or collection methodology are documented | Unresolved: obtain source documentation describing population, sampling, observation timing, and whether data is real or synthetic |
| Grain | Intended to be one customer per row; `customerID` is documented provisionally as the customer identifier | Assumption: Step 2 must check blanks, uniqueness, exact duplicates, and conflicting repeated identifiers; source documentation must clarify whether this identifies a person, account, or subscription |
| Outcome field | `Churn` exists; current notebook expects `Yes` and `No` | Expected coding only: Step 2 must profile observed labels; original source must confirm the operational meaning |
| Churn definition and window | No cancellation rule, event timing, or observation window is verified | Unresolved: verify whether churn means cancellation, non-renewal, inactivity, or another event, and the period to which it applies |
| Field units | `tenure`, `MonthlyCharges`, and `TotalCharges` exist; dictionary definitions remain provisional | Unresolved: verify tenure units, charge currency, billing basis, and accumulation timing before using unit-specific labels |
| Dated and operational outcomes | CSV header has no observation date, cancellation date, campaign assignment, or campaign outcome fields | Confirmed header limitation: dated trends and intervention effects require additional data |

## Working eligibility and metric definitions

These are the proposed analytical rules to validate in Steps 2 and 3. They do not establish the number of eligible customers or authorise unreviewed cleaning.

- An eligible customer must have a usable, nonblank identifier, a resolved customer-level record, and a valid outcome label. Count each eligible customer once. Investigate duplicate identifiers before choosing a resolution; never select an arbitrary record from a conflict.
- Use expected labels `Yes` and `No` pending validation. Missing, blank, and unexpected labels are unresolved outcomes; do not silently map them to `No`. Exclude them from churn-rate denominators and report their counts and reasons separately. Any later label standardisation needs a documented rule.
- Record raw rows, unique identifiers, duplicate/conflict populations, and customer exclusions separately. Overlapping issue populations must not be added as if mutually exclusive. Reconcile the final eligible population before publishing metrics.
- Missing predictor fields do not by themselves exclude an otherwise eligible customer from overall churn metrics. Show missing segment values as an explicit group; if a comparison excludes that group, state the filter and denominator.
- Missing or invalid charges do not remove a customer from churn-rate metrics. Charge summaries use valid observed amounts and disclose omitted customer counts and coverage; do not substitute zero for unknown values.

Let **N** be the number of unique eligible customers, **C** the number of those customers with `Churn = Yes`, and **N_s** and **C_s** the corresponding counts within segment **s**.

| Metric | Definition | Reporting rule |
|---|---|---|
| Customer count | N | Unique eligible customers with a valid churn label |
| Churned customers | C | Observed labelled churn volume within N |
| Overall churn rate | C / N | Display as a percentage with N and C; undefined when N is zero |
| Segment churn rate | C_s / N_s | Display with segment customer count and churn count; undefined for an empty segment |
| Share of all churners | C_s / C | Undefined when C is zero; overlapping segment shares must not be summed |
| Gap from overall rate | 100 x ((C_s / N_s) - (C / N)) | Percentage points, not relative percentage change; undefined if either rate is undefined |
| Monthly charge exposure proxy | Sum of valid `MonthlyCharges` for eligible customers labelled `Yes` | Descriptive billing exposure only; disclose valid/missing/invalid charge counts and coverage. If no valid amounts exist, report unavailable. Currency remains unverified |

Counts should be exact. Calculate rates from unrounded counts and round only for display, consistently across Python, SQL, and Power BI. A minimum segment size must be chosen and documented before interpreting comparisons; its value remains open pending the audit. Show small-group flags and consider uncertainty intervals for key comparisons.

## Interpretation boundaries

- Describe recorded churn prevalence in the supplied dataset. Do not call it a monthly churn rate or a forecast of churn within a future period while the observation window is unknown.
- Treat tenure bands as relationship-length segments. Observation dates and acquisition dates are needed for acquisition cohort or time-trend claims.
- Associations can motivate investigation and experiments; they do not establish why customers left or that an intervention will retain them. Check tenure, contract, and service mix before interpreting headline differences.
- Monthly charge exposure is not verified lost revenue, profit, customer lifetime value, or achievable savings. Financial outcomes require verified currency, timing, realised revenue or margin, and explicit assumptions.
- Demographic fields may describe the population and support comparisons. Sensitive characteristics alone will not justify retention targeting.
- Outreach feasibility, customer contact permissions, campaign history, treatment assignment, costs, and follow-up outcomes are not established by this file. Recommendation feasibility must be labelled as an assumption until operational evidence is available.
- A future classifier needs feature timing checks. Unknown timing limits it to exploratory classification rather than an operational early-warning system; churn risk alone does not measure response to an intervention.

## Open questions and follow-up

Roles below are suggested follow-up owners, not confirmed assignments.

| ID | Open question | Evidence needed / suggested owner | Effect until resolved |
|---|---|---|---|
| OQ1 | Where did this exact file come from, and under which terms? | Original URL, provider, version, acquisition date, and licence / dataset supplier | Attribution and permitted reuse remain unverified; do not claim a source or licence |
| OQ2 | What population and collection period does the file represent? | Sampling, collection methodology, real/synthetic status, and dates / dataset supplier | Limit findings to the supplied dataset; do not generalise to a current customer base |
| OQ3 | What does `Churn` mean, and over what period? | Operational event rule, coding, and observation window / source documentation or business data owner | Report recorded labels only; no monthly or future-period churn claims |
| OQ4 | Does `customerID` identify a unique customer, account, or subscription? | Source definition plus uniqueness and conflict checks / source documentation and Step 2 analyst | Customer grain and eligible counts remain provisional |
| OQ5 | What are the tenure and charge units and timing? | Field definitions, currency, billing basis, and accumulation rules / source documentation | Avoid unverified month/currency labels and realised-loss interpretations |
| OQ6 | Which values and records require treatment? | Full audit of labels, IDs, nulls/blanks, numeric conversion, categories, and plausibility / Step 2 analyst | Final eligibility, exclusions, and preparation rules remain pending |
| OQ7 | What group size and uncertainty reporting are appropriate? | Observed segment sizes and a documented reporting choice / analyst with stakeholder input | Do not prioritise groups from unstable rates alone |
| OQ8 | What outreach capacity and pilot measures are realistic? | Contact capacity, costs, eligibility, feasible actions, and follow-up outcomes / retention team | Later prioritisation, benefit scenarios, and model comparisons require explicit assumptions |

## Step 1 completion review

- Business decision, audience, success criteria, and phase boundaries are defined.
- Every README business question maps to required fields and planned evidence.
- Source, usage conditions, grain, label meaning, and units have evidence notes or explicit unresolved entries with follow-up actions.
- Metric definitions include eligibility, missing outcomes, missing charges, overlap, and zero-denominator handling.
- Scope limits and open questions are documented. Step 2 audit evidence is now available in the analysis log; the context register above records the source uncertainties established during Step 1. Phase 1 as a whole remains in progress pending Step 3.
