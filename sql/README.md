# SQL analysis plan

Phase 2 is complete. Its notebook recreates `outputs/data/customer_analytical.csv` (7,043 rows, 25 fields) and documents its schema in `outputs/reports/prepared_data_dictionary.csv`. Phase 3 will select/document a SQL engine and add queries. No database or executable queries have been created yet.

| Planned file | Purpose |
|---|---|
| `01_overview.sql` | Eligible customers, churn count, churn rate, and charge exposure proxy |
| `02_segment_analysis.sql` | Contract, tenure, billing, and service comparisons with denominators |
| `03_retention_priorities.sql` | Candidate segments with churn volume and overlap checks |

Before adding queries, document the engine, connection steps, schema, import command, and null handling here. Load the prepared dataset from `outputs/data/`, preserve one row per customer, and reconcile the main results with pandas before using them in the dashboard.

Treat identifiers and category labels as text; import empty TotalCharges as SQL NULL without excluding those customers from churn denominators. Apply numeric types and band sort order from the prepared-data dictionary. Reconcile to 7,043 unique IDs, 1,869 churners, 5,174 retained labels, zero missing MonthlyCharges, and 11 missing/flagged totals. The Phase 2 validation summary records the input/export hashes and acceptance evidence. SQL/pandas metric reconciliation itself remains Phase 3 work.
