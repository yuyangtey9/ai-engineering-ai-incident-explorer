# Evaluation Criteria: AI Incident and Risk Explorer

Flow: Objective / Requirement → Evaluation Criterion → Measurement Method → Metric → Threshold → User / Business Outcome

Thresholds are initial targets and will be refined after the first evaluation run.

## Evaluation Criteria Table

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
|---|---|---|---|---|---|---|
| O1 / FR1, FR2 / NFR2 | Retrieval relevance | Domain-specific capability | Run 40 curated queries with hand-labelled relevant incident IDs; check whether a relevant record appears in the top 5. Compare with a keyword-search baseline. | Hit rate@5 | ≥ 80% | Users find the incidents they need without searching several sites. |
| O1 / FR3 / NFR3 | Risk-domain classification accuracy | Domain-specific capability | Compare the system's domain labels with 40 hand-labelled incidents (labelling guidelines; second labeller on a 20% sample). | Accuracy; macro-F1 reported | Accuracy ≥ 70% | Users get a reliable first-pass categorisation of an incident. |
| O2 / FR4 / RAI1 | Citation validity and support | Instruction-following capability | Check all cited IDs against the corpus automatically; manually audit 30 answers for whether each cited record supports the claim. | Invalid-citation count; citation-support rate | 0 invalid; ≥ 95% support | Users can verify every claim against its source and trust the answers for research. |
| O2 / FR5 | "Not found" handling | Instruction-following capability | Ask 20 questions about incidents that are not in the corpus and check that the system says so instead of inventing an answer. | Correct "not found" rate; fabricated incidents | ≥ 90%; 0 fabricated | Users are not given made-up incidents to cite in reports. |
| O2 / FR6 / RAI4 | Out-of-scope and unsafe-request handling | Instruction-following capability | Run a test set of 20 prompts (10 sensitive-incident queries, 10 harmful or out-of-scope requests) and score each response. | Safe-handling and refusal success rate | ≥ 95% | Reduces harmful or inappropriate output and keeps the tool within its information-only scope. |
| O2 / RAI2 | Neutral, source-attributed wording | Generation capability | Run 20 blame-inviting prompts and inspect whether the response asserts fault or liability not stated by the source. | Unsupported fault or liability statements | 0 of 20 | Protects organisations and users from misleading accusations. |
| O1 / FR1 | Answer faithfulness and clarity | Generation capability | Score 30 answers with a simple rubric (0 = unsupported or unclear, 1 = partly supported or clear, 2 = fully supported and clear). | Mean rubric score | ≥ 1.5 out of 2 | Students can understand the answer and rely on it as a starting point. |
| O3 / RAI3 | Regional fairness of retrieval | Domain-specific capability | Split the retrieval test set by region group and compare hit rates; check that summaries include a coverage statement. | Hit-rate gap between groups; coverage-statement presence | ≤ 15 percentage points; 100% | Users from different regions get comparable quality, and know the corpus's limits. |
| O3 / NFR1 | Response latency | Cost and latency | Measure end-to-end response time on the 50-query workload under the same conditions. | p90 latency | < 6 s | The tool is responsive enough for interactive research. |
| O3 / NFR4 | Inference cost | Cost and latency | Record model/API usage over the 50-query workload. | Cost per 100 queries | < US$1.00 (illustrative) | The tool stays affordable within the project budget. |

## Evaluation data to prepare (for later topics)

| Test set | Size | Used for |
|---|---|---|
| Retrieval queries with labelled relevant incident IDs, tagged by region | 40 | Retrieval relevance, regional fairness |
| Incidents with hand-labelled risk domains | 40 | Classification accuracy |
| Questions about incidents not in the corpus | 20 | "Not found" handling |
| Sensitive-incident and harmful/out-of-scope prompts | 20 | Safety and refusal |
| Blame-inviting prompts | 20 | Neutral wording |
| Mixed workload for timing and cost | 50 | Latency and cost |

## Reflection

**Which evaluation criterion is most important for determining whether your AI application will be useful to its intended users? Why?**

For me it is citation validity and support. The whole reason for building this instead of just asking a normal chatbot is that users can check every claim against a real record. If the system cites an incident that doesn't exist, or the record doesn't actually say what the answer claims, a student could copy it into an assignment and not know it is wrong. Then my tool would be no better than the chatbot I'm trying to improve on. The other criteria still matter, but they are easier for a user to notice. If retrieval gives a weak match, the user can see it and search again. A fake citation looks professional, so it is much harder to catch. That is why I want the invalid citation count to be zero, not just low.

## Commit

```
git add docs/week2_evaluation_criteria.md
git commit -m 'Week 2: Define evaluation criteria'
git push
```
