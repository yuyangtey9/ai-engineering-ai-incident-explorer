# Week 3 Pipeline Decisions: AI Incident and Risk Explorer

Generated from the Week 3 notebook run on 2026-11-04 20:06 UTC. Notebook: `notebooks/week3_pipeline.ipynb`. Data type and pathway: structured tables, **Pathway B (CSV/Excel)**, run for two sources.

## 1. Topic 2 requirements and data needs

| Requirement | Data it needs | Source |
|---|---|---|
| FR1, FR2 (answer questions, find similar incidents) | Incident ID, title, description, date | S01 |
| FR3 (classify into risk domains) | Domain, sub-domain, risk description | S02 |
| FR4, RAI1 (cite a verifiable record) | Unique, non-missing IDs; a source link | S01, S02 |
| NFR5 (refresh without manual steps) | Snapshot date; source and output fingerprints | S01, S02 |

The architecture component expanded here is **Data Ingestion & Refresh** feeding the **Incident & Risk Knowledge Store** in `docs/week2_architecture_v1.png`. No embeddings or vector index are built in Week 3.

## 2. Selected sources
- **S01:** `incidents.csv` (AIID incidents, 1786 rows).
- **S02:** `The AI Risk Repository V4_03_12_2025.xlsx` (MIT AI Risk Repository, 2574 rows).
- Access, version and rights caveats are in `docs/week3_data_inventory.md`.

## 3. Part B comparison: same input, two configurations

**S01 (AIID incidents)**

| Metric | A: as loaded | B: prepared |
|---|---|---|
| rows | 1786 | 1786 |
| exact duplicate rows | 0 | 0 |
| rows missing id | 0 | 0 |
| missing or blank description | 0 | 0 |
| cells with edge whitespace | 92 | 0 |

**S02 (MIT AI Risk Repository)**

| Metric | A: as loaded | B: prepared |
|---|---|---|
| rows | 2574 | 2574 |
| exact duplicate rows | 0 | 0 |
| rows missing id | 0 | 0 |
| missing or blank description | 926 | 926 |
| cells with edge whitespace | 2765 | 0 |

**Selected approach:** configuration B (prepared).
- **Benefit:** it removed 0 S01 and 0 S02 rows without a usable ID and 0 S01 and 0 S02 exact duplicate rows, so every prepared record can be cited and no result is duplicated (FR2, FR4, RAI1).
- **Accepted limitation:** rows with missing descriptions are kept and only flagged (0 in S01, 926 in S02), so retrieval quality for those rows is unknown until the Topic 4 quality review. The pipeline also does not reduce the reporting bias already in the source data.

## 4. Chosen preparation configuration
- Standardise column names (`record_id`, `title`, `description`, `date`, and domain fields for S02).
- Trim whitespace; blank text becomes missing; no imputation, no value changes.
- Drop rows without a record ID; remove exact duplicate rows.
- S01 only: build `source_url` from the incident ID (`https://incidentdatabase.ai/cite/<id>`; check that links open).
- Duplicate IDs remaining after cleaning: S01 0, S02 5 (flagged, not removed).

## 5. ETL / ELT decision
**ETL (transform, then store).** The tables are small, the transformations are light, and no data warehouse is used, so cleaning the data before saving it as CSV keeps the Week 4 retrieval input simple. An ELT design (load raw data into a database, then transform inside it) would add infrastructure that this single-developer project does not need.

## 6. Batch versus streaming
**Batch.** The AIID publishes weekly snapshots, NFR5 asks for a scripted refresh, and real-time monitoring is explicitly out of scope in `docs/week2_requirements.md`. Streaming would add complexity without a requirement.

## 7. Stage contracts and failure points

| Stage | Contract / evidence | Failure response |
|---|---|---|
| Ingest | The chosen source exists and is the only match (`select_source`) | Stop with the list of candidates |
| Inspect | Required columns are found (`resolve_columns`) | Stop and print the available columns |
| Prepare | Prepared table is not empty and has a record ID on every row | Stop; report the transformation failure |
| Validate | Output exists, is non-empty, reads back with the same rows and columns, SHA-256 recorded | Stop for missing or empty output |
| Record lineage | Each source maps to its exact output; checksums re-verified (Cell 6B) | Stop and report the missing mapping |

**Failure point 1: source schema changes (Inspect).** A new snapshot renames or removes a column such as `incident_id`. *Response:* `resolve_columns` stops the run with the list of available columns, and the column spec in the notebook is updated.

**Failure point 2: records without a usable ID (Prepare).** Rows with no ID could not be cited. *Response:* they are dropped and counted (S01 0, S02 0 in this run), and the count is recorded so a sudden jump is visible after a refresh.

## 8. Output locations
- Part B prepared outputs: `data/processed/prepared_incidents.csv`, `prepared_risks.csv`; evidence in `data/processed/a3_inspection_evidence.json` and `b3_comparison.json`.
- Part B lineage: `data/week3_manifest.json`.
- Part C outputs: `data/pipeline_v1/incidents/` and `data/pipeline_v1/risk_taxonomy/`, each with `pipeline_manifest.json`.
- Repeatability: Cell 9 reran both pipelines and the source fingerprints, output fingerprints and mappings matched.

## 9. Provenance and access caveats
- SHA-256 proves a file is unchanged. It does not prove permission to share or data quality.
- Source version and rights are recorded as "not verified" in the manifests until confirmed in the inventory.
- Raw data and prepared data are not committed to GitHub unless the source terms allow it.

## 10. Traceability update

| Week 2 item | Week 3 evidence |
|---|---|
| ADR3 (offline snapshot refreshed by script) | The notebook ingests a dated snapshot and records fingerprints (`week3_manifest.json`) |
| FR1, FR2 / NFR2 (retrieval) | `prepared_incidents.csv` is the input for later retrieval; inspection evidence in the inventory |
| FR3 / NFR3 (classification) | `prepared_risks.csv` provides the domain labels |
| FR4 / RAI1 (citations) | Unique, non-missing `record_id` and `source_url` on every prepared record |
| NFR5 (maintainability) | One notebook run reproduces the outputs; Cell 9 checks repeatability |

## 11. Adaptations from the lab starter code
- Pathway B was run twice (S01, S02), so the two Part C runs are named `incidents` and `risk_taxonomy`.
- Added: column-name resolution (the real files' headers may differ), Excel header-row detection, an ID requirement, and a derived `source_url` for S01.
- Cell 1 uses `/content/week3_project` in Colab; a local fallback exists only for testing.
