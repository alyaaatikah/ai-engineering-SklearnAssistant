# **Part B \- Define Evaluation Criteria**

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| O3 / NFR2 | Answer Correctness | Domain-specific capability | Evaluate 30 in-scope scikit-learn questions using a correct / partly correct / incorrect rubric against the documentation. | Correct response rate | ≥ 80% | Users receive sufficiently reliable answers for routine "how do I / what does this do" questions. |
| O1 / FR2 / RAI3 | Source Citation and Support | Instruction-following capability | Inspect answers for citation presence (page title and URL) and verify whether the cited page supports the answer. | Citation presence rate; citation-source match rate | 100% presence; ≥ 90% match | Users can verify every answer against the official documentation. |
| O2 / FR5 / RAI2 | Out-of-Scope Refusal | Instruction-following capability | Test the system with a defined scope query set of at least 20 out-of-scope queries (other libraries, non-machine-learning topics, full-program debugging). | Refusal-and-redirect success rate | ≥ 95% | Keeps the assistant inside the documented scope so users are not given ungrounded answers. |
| O2 / FR1 / RAI4 | Unsupported API Detail Rate | Domain-specific capability | Extract class, function and parameter names from answers and check them against the indexed documentation. | Share of answers with an unsupported identifier | ≤ 5% | Users do not waste time on code that uses functions or parameters that do not exist. |
| O3 / RAI1 | Topic-Area Consistency | Domain-specific capability | Compare correctness rates across five scikit-learn topic areas (6 questions each). | Correctness-rate gap between best and worst topic area | ≤ 10 percentage points | Users get similar quality of help whatever part of scikit-learn they ask about. |
| O3 / NFR3 | Retrieval Accuracy vs Keyword-Search Baseline | Domain-specific capability | Run the same questions through the assistant's retrieval and through plain keyword search over the same documentation, and check whether the supporting page is in the top 5 results. | Retrieval hit rate at top 5 (assistant and baseline) | ≥ 85% and higher than the baseline | Shows the assistant adds value over simple search. |
| O3 / NFR1 | Response Latency | Cost and latency | Measure end-to-end response time across the full evaluation workload. | p90 latency | \< 5 s | The assistant is faster than manually searching the documentation. |
| O1 / FR7 | Response Clarity | Generation capability | Score a sample of 20 answers on a simple 1-3 rubric (clear / partly clear / unclear). | Share of answers rated "clear" | ≥ 80% | Students can understand the explanation without opening several pages. |

---

## **Reflection**

**Which evaluation criterion is most important for determining whether the AI application will be useful to its intended users? Why?**

Answer Correctness, together with Source Citation and Support. A fast assistant that gives wrong answers is worse than no assistant, and citations let users catch mistakes by checking the documentation page. Correctness is also the main measure of whether the project meets O3.