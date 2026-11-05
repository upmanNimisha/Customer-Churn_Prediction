# SQL analysis plan

Phase 3 will add queries after the analytical table and SQL engine are defined. No database or executable queries have been created yet.

| Planned file | Purpose |
|---|---|
| `01_overview.sql` | Eligible customers, churn count, churn rate, and charge exposure proxy |
| `02_segment_analysis.sql` | Contract, tenure, billing, and service comparisons with denominators |
| `03_retention_priorities.sql` | Candidate segments with churn volume and overlap checks |

Before adding queries, document the engine, connection steps, schema, import command, and null handling here. Load the prepared dataset from `outputs/data/`, preserve one row per customer, and reconcile the main results with pandas before using them in the dashboard.
