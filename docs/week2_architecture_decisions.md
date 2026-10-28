# V1 Architecture Decisions and Traceability: AI Incident and Risk Explorer

The V1 architecture diagram is in `docs/week2_architecture_v1.png`. V1 is conceptual: components are labelled by function, and specific technologies will be chosen in later topics.

## Step C1: Simplest architecture

**User Query → AI Application → AI Capability → Response**

The main interaction is a user typing a question about AI incidents. The AI capability is a language model that writes the answer. This simple flow cannot satisfy several requirements: it has no access to the incident records (FR1, FR4, NFR2), cannot verify that cited records exist (RAI1), cannot tell when no supporting record exists (FR5), has no control over out-of-scope or unsafe requests (FR6, RAI4), has no taxonomy reference for classification (FR3), and has no place to measure results (Part B).

## Step C2: Architecture needs from requirements

| Requirement ID | What does the requirement need? | Architecture Response |
|---|---|---|
| FR1, FR2, NFR2 | Answers and similar-case search must use real incident records. | Knowledge Access over an Incident & Risk Knowledge Store |
| FR4, RAI1 | Every cited record must exist and support the claim. | Grounding & Citation Control on the output; records carry IDs and source links |
| FR5 | The system must know when evidence is insufficient. | Evidence-sufficiency check inside Knowledge Access, with a "not found" path |
| FR3, NFR3 | Classification must use agreed risk categories. | Risk Taxonomy Reference supplied to the Language Model Service |
| FR6, RAI4 | Out-of-scope and unsafe requests must be refused; sensitive incidents handled carefully. | Scope / Safety Control on the input, plus output checks |
| RAI2 | Wording must be neutral and source-attributed. | Output control rules in Grounding & Citation Control |
| RAI3 | Coverage limits must be stated and regional quality measured. | Coverage notes stored with the knowledge store; stratified evaluation point |
| FR7 | Filtering needs structured metadata. | Metadata (year, domain, sector, region) kept in the knowledge store; filters in the interface |
| NFR1, NFR4 | Latency and cost must stay within targets. | A short single-pass path from application to model; usage logging |
| NFR5 | New data snapshots must be ingested without manual edits. | Offline Data Ingestion & Refresh process |
| Part B criteria | Results must be measurable. | Evaluation Point & Logging with measurement points E1–E5 |

## Step C4: Architecture Decision Records

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
|---|---|---|---|---|
| **ADR1: Include knowledge access and answer only from retrieved records** | FR1, FR4, FR5, RAI1: answers must be grounded and traceable to real incidents. | (a) Language model alone; (b) keyword search over records only; (c) dynamic knowledge access with semantic retrieval and metadata. | (c) Knowledge Access over a curated Incident & Risk Knowledge Store. | More system complexity and a weaker ability to answer questions outside the corpus, in return for grounding and traceability. |
| **ADR2: Use explicit scope/safety controls before and after the model** | FR6, RAI2, RAI4: unsafe or out-of-scope requests must be refused; wording must be neutral. | (a) Instructions in the prompt only; (b) explicit control on the input only; (c) explicit controls on both input and output. | (c) Scope / Safety Control (input) and Grounding & Citation Control (output). | Possible false refusals and extra processing time (NFR1), in return for more reliable control and a checkable citation step. |
| **ADR3: Use an offline data snapshot refreshed by script** | NFR5, NFR1, and the source-terms constraint (no redistribution of article text). | (a) Query the live source websites for every request; (b) one-off manual download; (c) offline snapshot ingested by a refresh script. | (c) Offline Data Ingestion & Refresh into the knowledge store. | The data can be out of date between refreshes, in return for speed, reproducibility and compliance with source terms. |
| **ADR4: Keep a single-pass retrieve-then-answer flow** | NFR1, NFR4: latency and cost targets; project is one semester. | (a) Multi-step agent that plans and calls tools repeatedly; (b) single-pass retrieve-then-answer. | (b) Single-pass flow with one retrieval and one model call per query. | Weaker handling of complex multi-hop questions, in return for lower latency, lower cost and a simpler system to evaluate. |

## Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 | FR1, FR2, NFR2 | Retrieval relevance (hit rate@5 ≥ 80%) | Knowledge Access; Incident & Risk Knowledge Store (E1) | Retrieval test results vs keyword baseline |
| O1 | FR3, NFR3 | Classification accuracy (≥ 70%) | Language Model Service; Risk Taxonomy Reference (E2) | Confusion matrix on the labelled set |
| O2 | FR4, RAI1 | Citation validity and support (0 invalid; ≥ 95%) | Grounding & Citation Control (E3) | Invalid-ID check and manual audit sheet |
| O2 | FR5 | "Not found" handling (≥ 90%; 0 fabricated) | Evidence-sufficiency check in Knowledge Access (E4) | Results on the not-in-corpus question set |
| O2 | FR6, RAI4 | Refusal and safe handling (≥ 95%) | Scope / Safety Control (input); output checks (E4) | Safety/refusal test results |
| O2 | RAI2 | Neutral wording (0 of 20 unsupported fault statements) | Grounding & Citation Control (E4) | Blame-prompt test results |
| O3 | RAI3 | Regional hit-rate gap (≤ 15 points) | Knowledge store coverage notes; stratified evaluation (E5) | Stratified retrieval results |
| O3 | NFR1, NFR4 | Latency (p90 < 6 s) and cost (< US$1.00 per 100 queries) | Single-pass path; usage logging (E5) | Latency and cost log |
| O3 | NFR5 | Refresh without manual steps (< 30 min) | Data Ingestion & Refresh | Refresh run log |

## Final review checklist

- **Does each major component support a requirement or evaluation need?** Yes. Each component in the diagram carries requirement tags, and the table in Step C2 gives the reason for each.
- **Is any important data source, safety control or evaluation point missing?** The OECD monitor is marked optional, and personal-data logging (RAI5) is handled by configuration, not a separate component. Both can be revisited in later topics.
- **Have I added any technology or component that is not yet justified?** No specific technologies are named. The one component that could be questioned is the output control, which is justified by RAI1 and RAI2.
- **Is there a simpler architecture that could still satisfy the requirements?** Dropping the output control or the taxonomy reference would remove support for RAI1, RAI2 and FR3, so they remain in V1.

## Reflection

**Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off did you accept?**

I think ADR1 had the most important trade-off. This is the decision to answer only from the records the system retrieves. It came from FR4, FR5 and RAI1, which say every claim needs a source and the system must not invent incidents. The price is that the system can't use general knowledge to fill gaps. If someone asks about a case that is not in my data, it will just say "not found", so it will feel less helpful than a normal chatbot and it will also be more complicated to build because of the retrieval part. I was happy to accept this because the main point of my project is that users can trust the citations. A tool that sometimes says "I don't know" is more useful for research than one that sounds sure and is wrong.

## Commit

```
git add docs/week2_architecture_v1.png docs/week2_architecture_decisions.md
git commit -m "Week 2: Add V1 architecture, ADR, and traceability"
git push
```
