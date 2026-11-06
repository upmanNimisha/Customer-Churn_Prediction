# Analysis working log

Status: Phase 1, Step 1 business framing completed on 6 November 2026. Data audit and later-phase sections remain pending; replace their placeholders with reviewed evidence as work progresses.

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

Step 1 completion checks are met through documented evidence and explicit unresolved notes. Overall Phase 1 remains in progress until the audit and quality report are complete.

### Step 2: Audit evidence (pending)

| Check | Observed result | Affected rows | Follow-up or limitation |
|---|---|---|---|
| To complete after profiling | Pending | Pending | Pending |

## Phase 2: Cleaning decisions

| Field or issue | Evidence | Rule and rationale | Rows affected | Validation |
|---|---|---|---|---|
| To complete after investigation | Pending | Pending | Pending | Pending |

Record counts before and after preparation, exclusions, band boundaries, and output filenames. Preserve unknown values until a treatment is justified.

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
