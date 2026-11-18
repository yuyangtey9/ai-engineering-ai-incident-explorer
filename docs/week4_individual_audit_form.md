# Individual Data Quality Worksheet

Individual Project Audit Form · SAIA3113 AI Engineering · Topic 4

**Name:** Tey Yu Yang · **Student ID:** A24AI0122


## A. Project context

| Item | Findings |
|---|---|
| **Project title** | AI Incident and Risk Explorer |
| **Project purpose** | A document-grounded assistant that answers questions about real AI incidents and risks and cites a source record for every claim, so that users can learn from real-world AI failures. |
| **Application requirements** | FR4: every claim must show a record ID and a working source link, so each record needs a unique, non-missing ID.<br>FR3: each incident must be mapped to a domain in the MIT AI risk taxonomy, so each risk record needs a description and a domain. |
| **Data sources and types** | S01: AI Incident Database (AIID) weekly snapshot, 2026-11-02. Prepared table prepared_incidents.csv: 1,786 rows, 8 columns. Licence CC BY-SA 4.0 (report contents not used).<br>S02: MIT AI Risk Repository, version 4, updated 03 December 2025. Prepared table prepared_risks.csv: 2,574 rows, 9 columns. Licence CC BY 4.0.<br>Both are structured tables (CSV and Excel) with text as their main content. |
| **Sample inspected** | All rows of both tables were checked by code. A seeded sample (seed 42) of 19 incident and 20 risk records was drawn, including edge cases (oldest and newest incident, sensitive topics, risk rows sharing an ID). 20 records are listed in the report appendix. Three incident records (550, 1158, 1362) were also compared with the live AIID pages by hand. |

## B. Data quality assessment

| Item | Findings |
|---|---|
| **Completeness** | S01 has no missing values in required fields (0 of 1,786). S02 does: 926 rows (36.0%) have no description and 972 (37.8%) have no domain. Impact: those rows cannot be used for classification (FR3). The cause (structural rows or real gaps) is unconfirmed. |
| **Consistency** | S01 is consistent: 0 unparseable dates, 0 dates after the snapshot, all 1,786 links match their ID. S02 domains agree with subdomains (0 mismatches in 1,600 rows), but only 1,908 of 2,574 IDs follow the dd.dd.dd format; the rest are mixed (such as 01.01.00.a). Impact: joins and filters on ID are fragile. |
| **Accuracy** | The 3 S01 records checked (550, 1158, 1362) match the live AIID pages in title and date. This does not cover the other 1,783 records, S02 (not compared with its source) or whether a report's claims are true. |
| **Timeliness** | S01 is current: latest incident 2026-10-30, three days before the snapshot. S02 is dated 2025-12-03, about 11 months older, so newer kinds of incident may be missing from the taxonomy. |
| **Uniqueness** | S01 has no duplicate rows, IDs or titles. S02 has no copied rows but reuses 5 IDs (49.01.02, 62.31.00 to 62.31.03) with different text; 98 descriptions repeat. Impact: a citation could point to the wrong risk record (FR4). |

## C. Two validation rules

| Item | Findings |
|---|---|
| **Rule 1: unique, non-empty ID** | Problem detected: a record that cannot be cited, or a citation that could point to two records.<br>Condition checked: record_id is not empty and appears once in the table.<br>Result: S01 PASS (0 empty, 0 duplicates in 1,786). S02 FAIL: 0 empty, 5 duplicated IDs.<br>Action: compare each pair in the MIT source; if they are different entries, make the ID unique by adding the category; otherwise record which row is kept and why. |
| **Rule 2: description and domain present** | Problem detected: a risk record that cannot be retrieved by meaning or placed in a domain.<br>Condition checked: description is not empty and domain is one of the 7 MIT domains.<br>Result: S02 FAIL. 1,430 of 2,574 rows pass (55.6%); 926 lack a description, 972 lack a domain, 754 lack both, 1,144 fail at least one.<br>Action: flag failing rows needs_review and keep them; check whether they are structural rows, then decide which to leave out of retrieval and classification.<br>Why human review, not auto-deletion: deleting would remove 44.4% of the table, many failing rows hold a category name so may be part of the taxonomy's structure, and the repeated IDs have different text, so deletion would silently lose real entries. |

## D. Coverage and potential bias

| Item | Findings |
|---|---|
| **Coverage findings** | Time (S01): 60.0% of incidents are from the last 3 years; 463 records in 2025 against 45 in 2019; the earliest is from 1983. Evidence is sufficient for recent incidents and thinner for earlier ones.<br>Region (S01, keyword proxy, not a region field): United States 134, China 60, United Kingdom 48, India 36, Africa 25, Europe/EU 14, Latin America 14, Malaysia 7, out of 1,786. Malaysia has probably too few records to test well. |
| **Potential bias and its effect** | Historical and representation bias: AIID reports come mainly from widely covered, English-language news, and the rise in recent years may reflect more reporting, not only more incidents.<br>Effect: the assistant may answer recent and US-related questions better than older or Malaysian ones, and may say "not found" where there is only no coverage. This is a risk, not a confirmed effect; to test it, write 10 questions for each group (recent vs before 2020; US vs Malaysia) and compare how often it finds and cites a relevant record. |

## E. Responsible data practices

| Item | Findings |
|---|---|
| **Data practices** | Source and traceability: both tables come from public sources; each incident links to its AIID page by ID, and SHA-256 fingerprints and manifests tie each prepared file to its raw download.<br>Personal and sensitive data: the scan found 1 email-like row (incident 1680, a software package name, not an email), 0 phone numbers and 223 incidents (12.5%) with sensitive-topic words. The scan does not find names or other details.<br>Permission: reuse is allowed with credit under the published licences; the share-alike term of CC BY-SA 4.0 applies to the prepared incident table; report contents are not used. This is my reading of the licences, not legal advice.<br>Governance statement: the project owner is responsible for the data. Sensitive records are shown by title and source link only. Answers repeat "allegedly" wording and do not state allegations as fact. Each snapshot refresh is followed by a rerun of this audit, failing records are flagged for review and not deleted, and raw files, passwords and keys are never committed to the repository. |

## F. Engineering decision

| Item | Findings |
|---|---|
| **GO** | ☐ |
| **GO WITH CONDITIONS** | ☒  Selected |
| **HOLD** | ☐ |
| **Evidence-based justification** | S01 passed every check and can be used. S02 fails Rule 2 (44.4% of rows) and Rule 1 (5 reused IDs), but the problems are specific and can be contained, so a full HOLD is not needed, and a plain GO would ignore the S02 results. Until the improvements are done, classification (FR3) uses only the 1,430 S02 rows that pass Rule 2. |
| **Two priority improvements** | 1. Review the 1,144 S02 rows that fail Rule 2: check in the MIT source whether they are structural rows or real gaps, flag them needs_review and decide which to leave out of the index. Done when every failing row has a recorded decision.<br>2. Make S02 record IDs unique: compare the 5 reused IDs with the source and add a key so each citation points to one record. Done when Rule 1 passes for S02. |
