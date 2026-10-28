# Requirements Specification: AI Incident and Risk Explorer

Version: 1.0 (Week 2)

---

## Section 0: Project Framing and Application Scope Checkpoint

| Element | Content |
|---|---|
| **Problem Evidence** | **1.** Reported AI incidents are rising: the Stanford 2025 AI Index, citing the AI Incident Database, reports 233 incidents in 2024, a 56.4% increase over 2023 (Stanford HAI, *The 2025 AI Index Report*, Responsible AI chapter). **2.** Knowledge about AI risks is fragmented: the MIT AI Risk Repository team compiled 1,725 risks from 74 existing frameworks because of the lack of a shared understanding of AI risks (Slattery et al., 2024, arXiv:2408.12622). Together these show that the volume of cases is growing while the knowledge about them is spread across separate resources. |
| **Existing Approaches** | **1. AI Incident Database (AIID):** a catalogue of real incident reports that users can browse, search and filter, with a harm taxonomy applied to a subset of incidents (McGregor, 2021). **2. OECD AI Incidents and Hazards Monitor:** detects incidents and hazards from news articles and classifies them automatically, with search and filters. **3. MIT AI Risk Repository:** a database and taxonomy of AI risks drawn from existing frameworks, with domain and causal classifications. **4. MIT AI Incident Tracker:** classifies real-world incidents from the AI Incident Database by risk, cause, harm and severity using a language model (MIT AI Risk Initiative, airisk.mit.edu). |
| **Project / Engineering Gap** | Existing resources are incident catalogues (AIID, OECD), a risk catalogue (MIT repository), or classified views of incidents (MIT AI Incident Tracker). They are browsed, filtered or searched by keyword rather than queried in natural language. A user cannot yet ask a question, find semantically similar incidents, and receive an answer in which every claim is traceable to a specific record, with a clear "not found" when the corpus has no support. General-purpose chatbots answer fluently but may invent incidents. The gap is practical and technical (grounded, cited question answering and safe handling), not a claim of scientific novelty. |
| **Objective O1** | To design a document-grounded AI assistant that retrieves AI incidents and maps them to a shared risk taxonomy. |
| **Objective O2** | To develop the assistant with semantic retrieval, taxonomy classification and guardrails that require every answer to cite a verifiable record or state that none was found. |
| **Objective O3** | To evaluate retrieval relevance, classification accuracy, groundedness, latency, cost, safety and fairness against defined thresholds. |
| **Intended Users** | Primary: students and early-career AI engineers learning Responsible AI from real cases. Secondary: journalists, researchers and policy staff who need cited examples. |
| **Supported Tasks** | (1) Answer factual questions about incidents and risks in the corpus. (2) Find incidents similar to a described situation. (3) Classify an incident or scenario into a risk domain. (4) Filter incidents by year, domain, sector and region. (5) Show the source record for every claim. |
| **Information / Knowledge Domain** | Publicly documented AI incidents and AI risk taxonomies, drawn from the AI Incident Database and the MIT AI Risk Repository (OECD monitor is an optional extension). The corpus is a fixed snapshot, not live news. |
| **Operating Context** | A web chat interface used by one user at a time for research, study or background reading. It is an information tool: it supports a person's research and does not make decisions. Prototyped and run in Google Colab. |
| **Important Constraints** | Semester time frame and a single developer. Free or low-cost tools and a small API budget. Source terms of use: article text and raw third-party data are not redistributed, so only IDs, short original summaries, metadata and download scripts are stored. Coverage is limited by the sources' reporting bias (mostly English-language, widely reported cases). No personal data is collected. |
| **Explicit Out-of-Scope Tasks** | The system must not: give legal, compliance or regulatory advice; decide who is at fault or legally liable; predict whether a user's own AI system will fail; certify or score the risk of a user's system; give medical or mental health advice; provide instructions for causing harm; monitor incidents in real time; or answer from general knowledge when the corpus has no supporting record. |
| **Evaluation Focus** | Does the Explorer retrieve relevant incidents and classify them into risk domains accurately enough for routine research, while giving a verifiable citation for every claim, refusing unsupported or out-of-scope requests, and responding within acceptable latency and cost? Results should also be compared with a simple keyword-search baseline. |

**Traceability check:** O1 leads to FR1–FR3 and FR7. O2 leads to FR4–FR6 and RAI1–RAI5. O3 leads to the NFRs and to the evaluation criteria in `week2_evaluation_criteria.md`.

---

## Section 1: Functional Requirements

| # | Requirement: The system SHALL | Scope Boundary: SHALL NOT (where relevant) |
|---|---|---|
| **FR1** | Answer natural-language questions about AI incidents and risks (for example date, entities involved, harm type and description) using records retrieved from the corpus. | State incident details that are not supported by a retrieved record. |
| **FR2** | Return the most similar incidents (up to five) to a situation described by the user, with a one-line reason for each match. | Present similarity as a prediction that the user's own system will fail. |
| **FR3** | Classify an incident or described scenario into one risk domain of the MIT AI Risk Repository taxonomy and name the domain. | Present its classification as the official label of any source database. |
| **FR4** | Provide, for every incident mentioned in an answer, the record ID, title and source link. | Cite a record ID that does not exist in the corpus. |
| **FR5** | State clearly that no supporting record was found when the corpus does not support an answer. | Answer from general model knowledge as if it were supported by the corpus. |
| **FR6** | Decline out-of-scope requests (legal or compliance advice, fault or liability judgements, medical or mental health advice, harmful instructions) and redirect the user to the supported tasks. | Give legal verdicts, state that a company is liable, or provide instructions for causing harm. |
| **FR7** | Let users filter results by year, risk domain, sector and region where that metadata exists. | Guess missing metadata; it shall show "unknown" instead. |

---

## Section 2: Non-Functional Requirements

Thresholds are initial targets set from intended use and will be refined after the first evaluation run.

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| **NFR1** | Performance | The system shall keep interactive responses within the agreed latency target. | Measure end-to-end latency (query submitted to full answer shown) on a defined workload of 50 queries. | p90 latency | < 6,000 ms |
| **NFR2** | Correctness (retrieval) | The system shall retrieve at least one relevant incident for supported queries. | Run a curated set of 40 queries, each with hand-labelled relevant incident IDs. | Hit rate@5 (share of queries with a relevant record in the top 5) | ≥ 80% |
| **NFR3** | Correctness (classification) | Domain classifications shall agree with reference labels. | Compare outputs with 40 hand-labelled incidents (labelled using written guidelines; a second labeller checks a 20% sample). | Accuracy; macro-F1 also reported | Accuracy ≥ 70% |
| **NFR4** | Cost | Inference cost shall remain within the project budget. | Record model/API usage over the 50-query evaluation workload. | Cost per 100 queries | < US$1.00 per 100 queries (illustrative) |
| **NFR5** | Maintainability | A refreshed data snapshot shall be ingested without manual editing or rebuilding unrelated components. | Run the documented refresh script on a new snapshot in Colab. | Manual steps required; refresh time | 0 manual steps; < 30 min |

---

## Section 3: Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| **RAI1** | Hallucination / grounding | Every cited record shall exist in the corpus and shall support the claim it is cited for. | Automatically check cited IDs against the corpus for all test answers; manually audit a sample of 30 answers for whether the cited record supports the claim. | Invalid-citation count; citation-support rate | 0 invalid citations; ≥ 95% support |
| **RAI2** | Misattribution / reputational harm | The system shall present incidents as reported by the source, in neutral wording, without stating fault or liability that the source does not state. | Run at least 20 prompts that invite blame ("who is to blame", "is company X guilty") and inspect each response. | Responses asserting unsupported fault or liability | 0 of 20 |
| **RAI3** | Reporting bias / fairness | Retrieval quality shall not differ beyond tolerance across regions, and the system shall state the corpus's coverage limits when giving summaries or comparisons. | Stratify the retrieval test set by region group and compare hit rates; inspect summary answers for a coverage statement. | Absolute hit-rate gap between region groups; coverage-statement presence | Gap ≤ 15 percentage points; 100% presence |
| **RAI4** | Safety / sensitive content | The system shall summarise distressing incidents (for example self-harm, abuse) at a high level and shall refuse requests for harmful or out-of-scope instructions. | Run a test set of at least 20 prompts (10 sensitive-incident queries, 10 harmful or out-of-scope requests). | Safe-handling and refusal success rate | ≥ 95% |
| **RAI5** | Privacy | The system shall not reveal personal details of private individuals beyond what is needed to describe the incident, and shall not store user queries beyond the session. | Inspect 30 sampled answers for personal identifiers; review logging configuration. | Answers containing unnecessary personal identifiers; stored queries | 0; 0 |

---

## Reflection

**Which Responsible AI requirement in your project will be the most difficult to measure or enforce technically? Why?**

I think RAI3 (fairness across regions) will be the hardest one for me. The first problem is the data. A lot of the incidents don't have a clear region, so I can't always put them into a group, and the groups that I can make will probably only have a few test queries each. That means the gap I measure could be just noise. The second problem is bigger. Most of the incidents in the databases come from English news about well-known cases, so the bias is already in the data before my system even touches it. My system could score the same on every region and still leave some regions out, just because nothing was reported about them. So I don't think the hit-rate gap really proves the system is fair. The best I can do is tell the user about the coverage limits in the answer, and be honest in my evaluation that this only checks part of the problem.

## Commit

```
git add docs/week2_requirements.md
git commit -m 'Week 2: Requirements specification (FR, NFR, RAI)'
git push
```
