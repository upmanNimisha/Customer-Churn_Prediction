# Project outputs

| Folder | Intended contents | Version control |
|---|---|---|
| `data/` | Generated analytical customer tables | Ignored; reproduce from the raw input |
| `charts/` | Reviewed charts for reports and the README | Track selected exports |
| `reports/` | Data dictionary, audit notes, cleaning decisions, findings, and recommendations | Track reviewed documents |
| `models/` | Generated model artifacts from Phase 5 | Ignored; document reproduction steps |

The reports contain the completed Phase 1 brief, raw-data dictionary, quality report, and analysis log. The notebook exports/reloads `data/customer_analytical.csv` and generates `reports/phase_2_validation_summary.md`. The tracked `reports/prepared_data_dictionary.csv` is reviewed documentation, read and checked without regeneration. The analytical table has 7,043 rows and 25 fields; metadata documents derivations, types, bands, and null rules. Regression checks confirmed the simplified notebook preserves the original results and customer CSV bytes. Customer-level exports remain ignored by Git and must be regenerated from the notebook after checkout. Source semantics remain unverified; Phase 3 business findings and later recommendations remain planned.
