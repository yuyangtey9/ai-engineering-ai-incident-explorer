# Week 3 Data Inventory: AI Incident and Risk Explorer

Generated from the Week 3 notebook run on 2026-11-04 20:06 UTC.

## Requirements these sources serve (Step A1)

| Requirement | Needed information | Source | Why usable / constraint |
|---|---|---|---|
| FR1, FR2 | Incident ID, title, description, date | S01 | Public incident catalogue; reporting bias towards widely covered, English-language cases |
| FR4, RAI1 | Unique, non-missing record IDs and a source link | S01, S02 | Records without an ID cannot be cited, so they are removed during preparation |
| FR3 | Risk domain, sub-domain and description | S02 | Defines the shared risk taxonomy for classification |
| NFR5 | Source fingerprint and snapshot date | S01, S02 manifests | SHA-256 shows when a source changes between refreshes |

## Data inventory (Step A4)

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `data/raw/incidents.csv`; https://incidentdatabase.ai/research/snapshots/ | Structured table (CSV). AIID incident records: ID, title, description, date. Needed for FR1, FR2, FR4, RAI1. | 2026-11-02; owner: AI Incident Database (incidentdatabase.ai) | Public weekly snapshot from incidentdatabase.ai. The snapshot’s license file states that report contents have their own intellectual property rights and that, unless otherwise noted, the database is shared under CC BY-SA 4.0 (https://creativecommons.org/licenses/by-sa/4.0/). Attribution is required. Report text (reports.csv) is not used or redistributed.| 1786 rows × 9 columns, 1,336,750 bytes. Mapped fields: record_id→incident_id, title→title, description→description, date→date. Missing (mapped fields): {'record_id': 0, 'title': 0, 'description': 0, 'date': 0}. Exact duplicate rows: 0; duplicate IDs: 0. SHA-256: c8a2a69b16c718f4… . Representative records: id 23: Las Vegas Self-Driving Bus Involved in Accident; id 550: Tesla Allegedly on Autopilot Struck High School Student Exiting School Bus |
| S02 | `data/raw/The AI Risk Repository V4_03_12_2025.xlsx`; https://airisk.mit.edu | Structured table (Excel/CSV). MIT AI Risk Repository risk records with domain taxonomy. Needed for FR3, RAI1. | Version 4, updated 03 December 2025; owner: MIT AI Risk Initiative / MIT FutureTech | Public. airisk.mit.edu states that data from the MIT AI Risk Initiative is licensed under CC BY 4.0, so attribution is required if reused (Slattery et al., 2024, arXiv:2408.12622). Raw file not committed by default. | 2574 rows × 19 columns, 712,682 bytes. Mapped fields: record_id→Ev_ID, risk_category→Risk category, risk_subcategory→Risk subcategory, description→Description, domain→Domain, subdomain→Sub-domain. Missing (mapped fields): {'record_id': 0, 'risk_category': 74, 'risk_subcategory': 718, 'description': 926, 'domain': 972, 'subdomain': 736}. Exact duplicate rows: 0; duplicate IDs: 5. SHA-256: 7e87838e436b2b8a… . Representative records: id 01.00.00: (no title or description); id 24.09.02: Cooperation. |

## Raw versus prepared storage

| Item | Location in the Colab workspace | Committed to GitHub? |
|---|---|---|
| Raw sources S01, S02 | `data/raw/` | No (not needed as evidence; the files are large and temporary, and AIID report text has its own rights)  |
| Prepared tables | `data/processed/prepared_incidents.csv`, `prepared_risks.csv` | Yes, with attribution and licence notes (see `data/DATA_LICENSES.md`) |
| Lineage manifest | `data/week3_manifest.json` | Yes |
| Pipeline manifests | `data/pipeline_v1/<run>/pipeline_manifest.json` | Yes |

## Checkpoint A
- **Which requirement needs this data?** FR1/FR2 and FR4 need S01. FR3 needs S02. RAI1 needs stable IDs from both.
- **Loaded sample (not just "file loaded"):** two representative records per source are listed in the inspection evidence above.
- **Rights and replacement:** Source terms are confirmed: the AIID snapshot license file (CC BY-SA 4.0; report text has its own rights and is not used) and airisk.mit.edu (CC BY 4.0). Prepared tables are committed with attribution (see data/DATA_LICENSES.md); raw files are not.
