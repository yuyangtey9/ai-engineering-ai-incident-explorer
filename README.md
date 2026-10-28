# AI Incident and Risk Explorer

> A document-grounded assistant that answers questions about real-world AI incidents, maps them to a shared risk taxonomy, and cites the source record for every answer, for students, AI engineers, journalists and policy staff.

## Problem Statement
People who want to learn from real-world AI failures must search several separate catalogues that use different structures and vocabularies. Existing tools rely on keyword or exact-text search, and general-purpose chatbots may invent incidents that never happened. AI engineering makes it possible to build a retrieval-based assistant that finds semantically similar incidents, classifies them into a shared risk taxonomy, and cites a verifiable record for every claim.

## Evidence of the Problem
- Reported AI incidents are rising: the Stanford 2025 AI Index, citing the AI Incident Database, reports 233 incidents in 2024, a 56.4% increase over 2023. Source: Stanford HAI, *The 2025 AI Index Report*, Responsible AI chapter. https://hai.stanford.edu/ai-index/2025-ai-index-report/responsible-ai
- Knowledge about AI risks is fragmented: the MIT AI Risk Repository team compiled 1,725 risks from 74 existing frameworks because of the lack of a shared understanding of AI risks. Source: Slattery et al. (2024), arXiv:2408.12622. https://futuretech.mit.edu/publication/the-ai-risk-repository

## Initial Related Work / Existing Approaches
- **AI Incident Database (AIID)**: catalogue of real incidents. McGregor (2021), arXiv:2011.08512. https://incidentdatabase.ai. *Limitation:* keyword, table and taxonomy-filter navigation; reporting biases; no cited natural-language question answering.
- **OECD AI Incidents and Hazards Monitor**: news-based incident monitor. https://oecd.ai/en/incidents-methodology. *Limitation:* machine-detected from news, third-party information not independently verified, search and filters only.
- **MIT AI Risk Repository**: database and taxonomy of AI risks. Slattery et al. (2024). https://airisk.mit.edu. *Limitation:* catalogues risks rather than real incidents; website search matches exact text in the description field only.
- **MIT AI Incident Tracker**: classifies AIID incidents by risk, cause, harm and severity. Limitation: classification and browsing only, with no cited natural-language question answering.

## Engineering Gap / Limitation
Existing resources are incident catalogues (AIID, OECD), a risk catalogue (MIT AI Risk Repository), or classified views of incidents (MIT AI Incident Tracker). They are browsed, filtered or searched by keyword rather than queried in natural language. No tool lets a user ask a question and receive an answer in which every claim cites a specific record, with a clear “not found” when the corpus has no support. General chatbots answer fluently but may fabricate incidents.

## Project Objectives
- O1: To design a document-grounded AI assistant that retrieves AI incidents and maps them to a shared risk taxonomy.
- O2: To develop the assistant with semantic retrieval, classification, and guardrails that require every answer to cite a verifiable record.
- O3: To evaluate retrieval relevance, classification accuracy, groundedness, latency, and safety.

## Target Users
Students and early-career AI practitioners learning Responsible AI through real cases; also journalists, researchers and policy staff who need cited examples.

## Data Sources
- AI Incident Database, incident records and taxonomy data: https://incidentdatabase.ai
- MIT AI Risk Repository, risk database and taxonomies: https://airisk.mit.edu
- OECD AI Incidents and Hazards Monitor (optional extension): https://oecd.ai/en/incidents

> Data handling: third-party article text and raw data are not committed to this repository. Only IDs, short original summaries, metadata and download scripts are stored. Check each source's terms of use.

## Responsible AI Risks (initial)
- Hallucinated incidents or IDs
- Misattribution of blame to organisations
- Reporting bias towards widely covered, English-language cases
- Distressing or personal content in incident reports

## Repository Structure
`docs/` documentation and Week 1 evidence (observation sheet, system map, project scope, evidence screenshots) · `data/` data notes and scripts · `src/` source code · `notebooks/` Colab notebooks · `evaluation/` test sets and results · `prompts/` prompt versions

## Weekly Progress
| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | ✅ |
