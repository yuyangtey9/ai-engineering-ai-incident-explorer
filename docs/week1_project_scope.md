# Week 1 Project Scope: AI Incident and Risk Explorer

| Field | Content |
|---|---|
| **Project Name** | AI Incident and Risk Explorer |
| **Domain** | Responsible AI / AI Safety Knowledge Management |

---

## Problem Statement

People who need to learn from real-world AI failures (students, AI practitioners, journalists, and policy staff) face documentation that is large, growing quickly, and spread across separate catalogues that each use their own structure and vocabulary. Finding "cases similar to mine" or "which risk category does this failure belong to" currently means manually searching several sites with keyword or field filters. AI engineering makes it possible to build a document-grounded assistant that retrieves semantically similar incidents, maps them to a shared risk taxonomy, and cites the exact incident record for every claim, which a plain keyword search or a general-purpose chatbot cannot do reliably.

---

## Evidence of the Problem

**Evidence 1: AI incidents are being reported at a rapidly increasing rate.**
The Stanford 2025 AI Index reports, citing the AI Incident Database, that reported AI-related incidents reached 233 in 2024, a record high and a 56.4% increase over 2023. (A secondary source citing the 2026 AI Index reports 362 incidents for 2025. Verify this against the primary 2026 report before citing it.)
- Source / citation: Stanford HAI, *The 2025 AI Index Report*, Responsible AI chapter. https://hai.stanford.edu/ai-index/2025-ai-index-report/responsible-ai (also summarised at https://hai.stanford.edu/news/ai-index-2025-state-of-ai-in-10-charts)

**Evidence 2: Knowledge about AI risks is fragmented across many frameworks and databases.**
The MIT AI Risk Repository authors state that a lack of shared understanding of AI risks impedes comprehensive discussion and response, and they built a database of 1,725 risks extracted from 74 existing taxonomies and frameworks to address this. A separate study of AI incident databases likewise notes that the absence of a unified taxonomy makes it harder to draw actionable insights from incident data.
- Source / citation: Slattery, P., Saeri, A. K., Grundy, E. A., et al. (2024). *The AI Risk Repository: A Comprehensive Meta-Review, Database, and Taxonomy of Risks From Artificial Intelligence*. arXiv:2408.12622. https://futuretech.mit.edu/publication/the-ai-risk-repository
- Source / citation: *Standardised schema and taxonomy for AI incident databases in critical digital infrastructure* (2025). arXiv:2501.17037. https://arxiv.org/html/2501.17037v1

---

## Initial Related Work

**Study / solution 1: AI Incident Database (AIID)**
- Source / citation: McGregor, S. (2021). *Preventing Repeated Real World AI Failures by Cataloging Incidents: The AI Incident Database*. arXiv:2011.08512. https://arxiv.org/pdf/2011.08512 and https://incidentdatabase.ai
- Key limitation: Navigation relies on keyword search, tables, and taxonomy filters. The detailed CSET harm taxonomy appears to be applied to only a subset of incidents (verify the current number on the taxonomy page), and the project itself notes that incident collection contains reporting biases and that totals cannot be extrapolated to the real incident rate. There is no natural-language, cited question answering.

**Study / solution 2: OECD AI Incidents and Hazards Monitor (AIM)**
- Source / citation: OECD.AI. *Overview and methodology of the AI Incidents and Hazards Monitor*. https://oecd.ai/en/incidents-methodology
- Key limitation: Incidents are detected automatically from news articles and classified by LLMs. The OECD states it does not independently verify the accuracy of third-party information, and the monitor only captures publicly reported events, which are a subset of all incidents. It offers search and filters rather than grounded, conversational analysis across sources.

**Study / solution 3: MIT AI Risk Repository**
- Source / citation: Slattery et al. (2024), arXiv:2408.12622. https://airisk.mit.edu
- Key limitation: It catalogues *risks* extracted from frameworks, not *real incidents*, and it does not assess likelihood or severity. Its website search matches exact text in the description field only, so differently worded descriptions of the same risk are missed. It is not linked to real incident records.

**Study / solution 4: MIT AI Incident Tracker**
- Source / citation: MIT AI Risk Initiative. https://airisk.mit.edu
- Key limitation: It classifies incidents from the AI Incident Database by risk, cause, harm and severity, but it is a classification and browsing resource, not a natural-language assistant that answers questions with cited records.
---

## Engineering Gap / Limitation

**Gap / Limitation:** Existing resources are incident catalogues (AIID, OECD), a risk catalogue (MIT AI Risk Repository), or classified views of incidents (MIT AI Incident Tracker). They are browsed, filtered or searched by keyword rather than queried in natural language. No tool lets a user ask a question and receive an answer in which every claim cites a specific record, with a clear “not found” when the corpus has no support. General chatbots answer fluently but may fabricate incidents.

---

## Initial Project Objectives

- **O1:** To design a document-grounded AI assistant that retrieves and explains real-world AI incidents and maps them to a shared AI risk taxonomy.
- **O2:** To develop the assistant with semantic retrieval, taxonomy classification, and guardrails that require every answer to cite a verifiable incident or risk record and to say "not found in the corpus" when no record supports an answer.
- **O3:** To evaluate the assistant in terms of retrieval relevance, classification accuracy against a hand-labelled test set, groundedness (citation validity), latency, and safety.

---

## Target Users

- Students and early-career practitioners learning Responsible AI through real cases.
- AI engineers and product teams doing pre-deployment risk reviews who want "similar past failures".
- Journalists, researchers, and policy staff who need cited, traceable examples.

---

## Data Sources

- **Source 1:** AI Incident Database, incident records and taxonomy data (complete database snapshot available for download) — URL: https://incidentdatabase.ai
- **Source 2:** MIT AI Risk Repository, risk database with Causal and Domain taxonomies — URL: https://airisk.mit.edu
- **Source 3 (if any):** OECD AI Incidents and Hazards Monitor, incident and hazard summaries with metadata — URL: https://oecd.ai/en/incidents

**Data handling note:** Before committing anything, check each source's terms of use. Incident records link to or quote third-party news articles, so do not commit article text. Commit only incident IDs, short project-written summaries, metadata, and a download script that re-fetches the data from the original source.

---

## Similar to EduAdvisor

- Retrieval-augmented, document-grounded question answering over public documents.
- Same engineering stack concepts: ingestion pipeline, chunking and embedding, vector store, foundation model with context, guardrails, and monitoring.
- Evaluated on groundedness, answer quality, latency, and safety.
- Low-risk, information-only scope with refusals when the sources do not contain the answer.

## Different from EduAdvisor

- The corpus is structured incident and risk records (with fields such as date, harm type, and entities), not long PDF handbooks, so ingestion and chunking decisions differ.
- Adds a classification task: mapping each incident to the MIT Domain Taxonomy (7 domains), which can be scored objectively against a hand-labelled set.
- Supports analytical queries such as "find similar incidents" and "compare incidents across sectors", rather than only factual lookup.
- Fairness risk is about *whose* incidents are reported (reporting bias), not uneven coverage of faculties.
- The subject matter itself is sensitive (harm to people), which requires safety handling in the content, not just in the user's query.

---

## Responsible AI Risks

- **Risk 1: Hallucination (fabricated incidents).** The model invents an incident, company, or incident ID that does not exist. Users who cite it in a report, policy brief, or news article spread misinformation and the organisation named is harmed. *Control:* require incident-ID citations and verify that each cited ID exists in the corpus before display.
- **Risk 2: Misattribution and reputational harm.** The assistant blames the wrong company, overstates responsibility, or presents an allegation as a proven fact. The named organisations and individuals are harmed. *Control:* use neutral, source-attributed wording ("according to report X"), show the report date, and avoid verdicts.
- **Risk 3: Reporting bias and unfair coverage.** Incident databases over-represent English-language, Western, and widely covered cases, so the assistant may under-report incidents in other regions and make some risks look rarer than they are. Users in under-represented regions are poorly served and risk analyses become skewed. *Control:* state coverage limits in every answer and measure retrieval quality across regions and languages.
- **Risk 4: Safety and privacy of sensitive content.** Some incidents involve self-harm, abuse, deepfake imagery, or named private individuals. The assistant could reproduce distressing detail or expose personal information. *Control:* summarise at a high level, avoid reproducing identifying detail, and add a content guardrail for distressing topics.
