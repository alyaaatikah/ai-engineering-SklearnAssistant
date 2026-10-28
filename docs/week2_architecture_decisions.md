# **Part C \- Identify Architecture Needs**

## **Simplest Architecture**

**User Query → AI Application → AI Capability (language model) → Response**

## **Architecture Needs from Requirements**

| Requirement ID | What does the requirement need? | Architecture Response |
| ----- | ----- | ----- |
| FR1 / FR2 / RAI3 | Answers must come from the indexed documentation and carry a verifiable source reference. | Documentation knowledge source with source tracking; knowledge access (retrieval) component |
| FR3 | Each answer must state the documentation version. | Version information stored with the documentation and passed to the response |
| FR4 / RAI3 | The system must say when the documentation does not support an answer. | Grounding check on the retrieved evidence and the generated answer |
| FR5 / RAI2 | Out-of-scope queries must be refused and redirected. | Scope / safety control at the input |
| RAI4 | Answers must not contain unsupported classes, functions or parameters. | Output check comparing identifiers with retrieved documentation |
| NFR1 | Responses must meet the latency target. | Efficient interaction path with lightweight checks |
| NFR5 | A new documentation version must be added without rebuilding unrelated components. | Documentation knowledge source kept separate from the application |
| NFR2 / NFR3 / RAI1 | Correctness, retrieval and topic-area results must be measurable. | Evaluation point with logging and a manually written test set |

## **Architecture Decision Record (ADR)**

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
| ----- | ----- | ----- | ----- | ----- |
| Include knowledge access over the indexed documentation | FR1, FR2, RAI3: answers must be grounded and cited | Language model only; keyword search only; knowledge access feeding the language model | Knowledge access feeding the language model | More system complexity and some added latency, but grounded and traceable answers |
| Fix the assistant to one recorded scikit-learn version | FR3, NFR5: keep answers checkable against one known version and keep updates simple | Several versions at once; latest version automatically; one fixed version | One fixed, recorded version | Less coverage of other versions, but simpler indexing and verifiable answers |
| Add an explicit grounding and output check | RAI3, RAI4, FR4: no unsupported citations or API details | Prompt instructions only; explicit check after generation | Explicit output check | Extra processing time (NFR1) and possible false flags, but fewer hallucinated API details |
| Add an explicit scope control at the input | FR5, RAI2: out-of-scope queries must be refused | Prompt instruction only; explicit scope-control component | Explicit scope control | Possible false refusals on borderline questions, but more reliable refusal |

## **Initial Requirements Traceability Matrix**

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
| ----- | ----- | ----- | ----- | ----- |
| O1 | FR1 / FR2 / RAI3 | Source Citation and Support | Documentation Knowledge Source; Knowledge Access; Grounding & Output Check | Citation presence and source-match results on the 30-question set |
| O2 | FR5 / RAI2 | Out-of-Scope Refusal | Scope / Safety Control | Refusal-and-redirect results on 20 or more out-of-scope queries |
| O2 | RAI4 | Unsupported API Detail Rate | Grounding & Output Check | Identifier-check results across all answers |
| O3 | NFR2 / NFR3 | Answer Correctness; Retrieval Accuracy vs Keyword-Search Baseline | Knowledge Access; Language Model Service; Evaluation Point | Correctness rubric scores and retrieval hit rate compared with the keyword-search baseline |
| O3 | RAI1 | Topic-Area Consistency | Evaluation Point | Per-topic-area correctness table and gap between best and worst area |
| O3 | NFR1 | Response Latency | Whole pipeline; Evaluation Point | p90 latency measurement on the evaluation workload |

## **Final Review**

* Each component supports a requirement or an evaluation need (see Architecture Needs table).  
* The Evaluation Point receives latency and usage data, which covers NFR1 and NFR4.  
* No specific model, database, framework or user-interface technology has been committed at V1.  
* A simpler option (language model with retrieval only) was rejected because it cannot satisfy FR5, RAI2 or RAI4.

## **Reflection**

**Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off was accepted?**

The explicit grounding and output check. It was driven by RAI4 and RAI3 (no unsupported API details, verifiable citations). The trade-off accepted is extra processing time against the latency target (NFR1), plus some false flags on valid answers.

