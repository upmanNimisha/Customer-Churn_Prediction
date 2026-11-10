# Project outputs

| Folder | Intended contents | Version control |
|---|---|---|
| `data/` | Generated analytical customer tables | Ignored; reproduce from the raw input |
| `charts/` | Reviewed charts for reports and the README | Track selected exports |
| `reports/` | Data dictionary, audit notes, cleaning decisions, findings, and recommendations | Track reviewed documents |
| `models/` | Generated model artifacts from Phase 5 | Ignored; document reproduction steps |

The reports contain the completed Phase 1 brief, raw-data dictionary, quality report, and analysis log. Phase 2 Step 2 exports `reports/prepared_data_dictionary.csv` from the main notebook, documenting all 25 analytical fields, derivations, types, bands, and null rules. Source semantics remain unverified. The customer-level analytical table is still in memory; its export is planned for Step 3. Business findings and recommendations remain planned. Record each generated artifact's source, filters, and producing notebook section before publishing it.
