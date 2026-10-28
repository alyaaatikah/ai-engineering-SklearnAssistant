# **Part A \- Refine Project Farming, Define Application Scope and Write Requirements**

Version: 1.0

## **Section 0 — Project Framing & Application Scope Checkpoint**

| Element | Details |
| ----- | ----- |
| **Problem Evidence** | 1\) 61% of respondents in the 2024 Stack Overflow Developer Survey spend more than 30 minutes a day searching for answers or solutions to problems (https\://survey.stackoverflow.co/2024/). The scikit-learn documentation is large and spread across the user guide, API reference and FAQ, so this search time applies directly. 2\) In the same survey, 79% of developers named misinformation and disinformation in AI results as a top ethical concern, so an AI assistant for developers must be grounded and show its sources. |
| **Existing Approaches** | 1\) Retrieval-Augmented Generation (Lewis et al., 2020, arXiv:2005.11401): grounds language model answers in a retrievable document index, but was built on Wikipedia, not software documentation. 2\) RAGAs (Es et al., 2024, arXiv:2309.15217): a framework to evaluate RAG pipelines (faithfulness, answer relevance, context relevance); it is an evaluation tool, not an assistant. 3\) Jain et al. (2024, arXiv:2407.09726): shows API documentation can reduce API hallucinations in code LLMs, but studies cloud SDKs and code generation. 4\) The scikit-learn website search: authoritative, but keyword-based. 5\) General-purpose AI assistants: flexible, but may not cite the official documentation. |
| **Project / Engineering Gap** | General-purpose AI assistants can answer scikit-learn questions but may invent parameters, mix up versions, or give no source. Plain keyword search over the docs is authoritative but does not understand questions phrased in everyday language. What remains unresolved is a small, checkable assistant that answers only from one documentation version, cites the page, and refuses when the documentation does not contain the answer. |
| **Objective O1** | To design a document-grounded assistant that answers scikit-learn questions using its official documentation. |
| **Objective O2** | To develop the assistant with retrieval, source citation and guardrails (out-of-scope refusal, "insufficient evidence" response). |
| **Objective O3 (if relevant)** | To evaluate answer correctness, groundedness, retrieval accuracy, latency and refusal behaviour against a manually written question set and a keyword-search baseline. |
| **Intended Users** | Students and junior developers learning or using scikit-learn. |
| **Supported Tasks** | Answer "what is / what does / how do I" questions about scikit-learn classes, functions, parameters and concepts; point to the relevant documentation page; show short code examples taken from the cited page; state the documentation version used. |
| **Information / Knowledge Domain** | A selected set of pages from the official scikit-learn documentation (User Guide and FAQ), fixed to one recorded version. |
| **Operating Context** | A web chat interface used by a student or developer while coding or studying, as a quick lookup tool alongside the official documentation. |
| **Important Constraints** | One semester; free or low-cost model access; only publicly available documentation (only URLs and download scripts stored in the repository); documentation changes between versions; the question set is written and checked manually by one student. |
| **Explicit Out-of-Scope Tasks** | Debugging or rewriting a user's full programs; executing code; questions about libraries other than scikit-learn; general programming or non-machine-learning questions; installation or hardware troubleshooting; any claim that is not supported by the indexed documentation. |
| **Evaluation Focus** | Does the assistant give correct, source-supported answers to in-scope scikit-learn questions without invented API details, refuse out-of-scope questions, admit when the documentation does not cover a question, and respond fast enough to be more useful than manual searching? |

---

## **Section 1 \- Functional Requirements — Sklearn Docs Assistant**

Version: 1.0

| \# | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
| ----- | ----- | ----- |
| **FR1** | Answer questions about scikit-learn classes, functions, parameters and concepts using the indexed official documentation. | Invent classes, functions, parameters or behaviour that are not supported by the indexed documentation. |
| **FR2** | Provide a source reference (page title and URL) for each answer based on the documentation. | Present unsupported information as if it came from the official documentation. |
| **FR3** | State the scikit-learn documentation version that each answer is based on. | Present an answer as valid for all scikit-learn versions. |
| **FR4** | State clearly when the indexed documentation does not contain enough information to answer, and suggest where the user can look. | Guess or answer from general model knowledge when no supporting documentation is found. |
| **FR5** | Decline queries outside the defined scope (other libraries, non-machine-learning topics, full-program debugging, installation troubleshooting) and redirect the user to the relevant official resource. | Execute code or debug a user's full program. |
| **FR6** | Include short code examples taken from the cited documentation page when the user asks how to do something. | Present untested or invented code as if it were documented. |
| **FR7** | Present answers in clear, concise language suitable for students. | \- |

---

## **Section 2 \- Non-Functional Requirements**

| \# | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| ----- | ----- | ----- | ----- | ----- | ----- |
| **NFR1** | Performance | The system shall keep interactive responses within the agreed latency target. | Measure end-to-end latency on a defined test workload (the full evaluation question set). | p90 latency | \< 5,000 ms |
| **NFR2** | Correctness | Answers shall be factually consistent with the indexed documentation. | Score a manually written set of 30 in-scope questions using a correct / partly correct / incorrect rubric. | Correct response rate (%) | ≥ 80% |
| **NFR3** | Reliability (retrieval) | The system shall retrieve the documentation page that supports the answer. | For each evaluation question with a known source page, check whether that page appears in the retrieved results. | Retrieval hit rate at top 5 | ≥ 85% |
| **NFR4** | Cost | Inference cost shall remain within the project budget. | Record model/API usage over the evaluation workload. | Cost per 100 queries | \< US\$0.50 per 100 queries (illustrative) |
| **NFR5** | Maintainability | Documentation for a new scikit-learn version shall be incorporated without rebuilding unrelated components. | Run a controlled documentation-update test. | Update time | \< 15 min |

---

## **Section 3 \- Responsible AI Requirements**

| \# | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| ----- | ----- | ----- | ----- | ----- | ----- |
| **RAI1** | Fairness (uneven quality across topic areas) | System performance shall not differ beyond the agreed tolerance across scikit-learn topic areas. | Evaluate a stratified test set (supervised learning, unsupervised learning, preprocessing, model selection and evaluation, pipelines and utilities; 6 questions each) and compare correctness rates. | Absolute correctness-rate gap between best and worst topic area | ≤ 10 percentage points |
| **RAI2** | Safety / Scope (out-of-scope answers) | The system shall refuse out-of-scope queries and redirect users appropriately. | Run a defined scope test set containing at least 20 out-of-scope queries (other libraries, non-machine-learning topics, full-program debugging). | Refusal-and-redirect success rate | ≥ 95% |
| **RAI3** | Transparency / Grounding | The system shall provide a verifiable source reference for documentation-based answers and indicate when supporting evidence cannot be found. | Check answers for citation presence and whether the cited page supports the answer; test 10 questions the documentation cannot answer. | Citation presence rate; citation-source match rate; correct "insufficient evidence" rate | 100% presence; ≥ 90% match; ≥ 90% |
| **RAI4** | Hallucinated API details | Answers shall not contain classes, functions or parameters that do not exist in the cited documentation. | Extract code identifiers from each answer and check them against the indexed documentation text. | Share of answers containing an unsupported identifier | ≤ 5% |

---

## **Reflection**

**Which Responsible AI requirement in this project will be the most difficult to measure or enforce technically? Why?**

RAI4 (hallucinated API details) is the most difficult. A language model can write parameter names that look correct, and checking each identifier against the documentation needs a reliable way to extract identifiers and a complete documentation index. A valid parameter that is missing from the indexed pages could be flagged by mistake, so some manual review is still needed.

