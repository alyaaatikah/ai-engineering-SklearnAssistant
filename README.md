# SklearnDocs Assistant

> Answers scikit-learn questions in plain language, using only the official documentation and always showing the source page, for students and junior developers.

## Problem Statement
Developers and students spend a lot of time searching long documentation for the right class, parameter or example. The scikit-learn docs are spread across the user guide, API reference and FAQ. SklearnDocs Assistant gives short, sourced answers grounded in the official docs, and says "I don't know" when the docs do not cover the question.

## Evidence of the Problem
- 61% of respondents in the 2024 Stack Overflow Developer Survey spend more than 30 minutes a day searching for answers or solutions. https://survey.stackoverflow.co/2024/
- 79% of developers in the same survey named misinformation in AI results as a top ethical concern, which is why answers here are grounded and cited.

## Initial Related Work / Existing Approaches
- RAG (Lewis et al., 2020, arXiv:2005.11401) — grounds language model answers in a retrievable index, but used Wikipedia, not software documentation.
- RAGAs (Es et al., 2024, arXiv:2309.15217) — evaluates RAG pipelines, but is an evaluation tool and relies on LLM-based scoring.
- Jain et al. (2024, arXiv:2407.09726) — shows documentation can reduce API hallucinations, but studies cloud SDKs and code generation, not scikit-learn Q&A.

## Engineering Gap / Limitation
General AI assistants may invent parameters or mix versions without giving a source, and keyword search does not understand everyday-language questions. This project builds a small assistant that answers from one documentation version, cites the page, and refuses when the docs do not contain the answer.

## Project Objectives
- O1: To design a document-grounded assistant for scikit-learn questions.
- O2: To develop it with retrieval, source citation and guardrails (out-of-scope refusal, "I don't know").
- O3: To evaluate answer correctness, groundedness, retrieval accuracy, latency and refusal behaviour against a keyword-search baseline.

## Target Users
Students and junior developers learning or using scikit-learn.

## Data Sources
- scikit-learn User Guide (v1.9.1) — https://scikit-learn.org/stable/user_guide.html
- scikit-learn FAQ (v1.9.1) — https://scikit-learn.org/stable/faq.html
- scikit-learn source repository (`doc/` folder, git tag `1.9.1`) — https://github.com/scikit-learn/scikit-learn

## Weekly Progress
| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | `README.md` + initial docs | ✅ |
| W2 | System architecture + requirements (`FR1`–`FR3`, `RAI1`–`RAI4`) | `docs/week2_architecture_v1.png` + requirements spec | ✅ |
| W3 | Data ingestion, paragraph-aware chunking & V1 lineage pipeline | [`notebooks/week3_pipeline.ipynb`](notebooks/week3_pipeline.ipynb), [`docs/week3_data_inventory.md`](docs/week3_data_inventory.md), [`docs/week3_pipeline_decisions.md`](docs/week3_pipeline_decisions.md), [`docs/week3_pipeline_diagram.png`](docs/week3_pipeline_diagram.png), and `data/` manifests | ✅ |

## Week 3 Pipeline Overview
- **Corpus Sample:** Six official `scikit-learn` v1.9.1 documentation files (`linear_model`, `compose`, `cross_validation`, `clustering`, `preprocessing`, and `faq`) tracked in [`docs/week3_data_inventory.md`](docs/week3_data_inventory.md).
- **Preparation Method:** Pathway A ETL pipeline using paragraph-aware chunking (`500`-character limit) to preserve code examples (doctests) and mathematical formulas (`225` chunks exported for `linear_model.txt`).
- **Provenance & Repeatability:** All inputs and prepared outputs are SHA-256 verified across [`data/week3_manifest.json`](data/week3_manifest.json) and [`data/pipeline_v1/documents/pipeline_manifest.json`](data/pipeline_v1/documents/pipeline_manifest.json).
