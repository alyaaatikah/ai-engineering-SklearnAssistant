# Week 1 Project Scope

| Field | Details |
|---|---|
| **Project Name** | SklearnDocs Assistant |
| **Domain** | Software Documentation / Developer Support (scikit-learn) |

## Problem Statement
Developers and students learning machine learning spend a lot of time searching long documentation for the right class, parameter or example. The scikit-learn documentation is large and spread across the user guide, API reference and FAQ, so one question often needs several pages. AI engineering makes it possible to build a document-grounded assistant that answers scikit-learn questions in plain language, shows the exact documentation page it used, and says "I don't know" when the docs do not cover the question.

## Evidence of the Problem
- **Evidence 1:** In the 2024 Stack Overflow Developer Survey, 61% of respondents said they spend more than 30 minutes a day searching for answers or solutions to problems.
  - Source / citation: Stack Overflow (2024). *2024 Developer Survey*. https://survey.stackoverflow.co/2024/
- **Evidence 2:** In the same survey, 79% of developers named misinformation and disinformation in AI results as a top ethical concern, so an AI assistant for developers needs to be grounded and show its sources.
  - Source / citation: Stack Overflow (2024). *2024 Developer Survey*. https://survey.stackoverflow.co/2024/

## Initial Related Work (at least 3)
- **Study / solution 1:** Retrieval-Augmented Generation (RAG), which combines a pre-trained language model with a retrievable document index so answers can be grounded in external knowledge.
  - Source / citation: Lewis, P. et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. NeurIPS 2020. arXiv:2005.11401. https://arxiv.org/abs/2005.11401
  - Key limitation: Uses a Wikipedia index and general knowledge tasks, not software documentation with code, parameters and versions.
- **Study / solution 2:** RAGAs, a framework for evaluating RAG pipelines without needing human-labelled answers (measures faithfulness, answer relevance and context relevance).
  - Source / citation: Es, S. et al. (2024). *RAGAs: Automated Evaluation of Retrieval Augmented Generation*. EACL 2024 System Demonstrations. arXiv:2309.15217. https://arxiv.org/abs/2309.15217
  - Key limitation: It is an evaluation tool, not an assistant, and its scores come from LLM judgement, so they should be checked against a manually verified question set.
- **Study / solution 3:** Jain et al. study API hallucinations in code LLMs, introduce the CloudAPIBench benchmark, and test using API documentation to reduce hallucinations.
  - Source / citation: Jain, N. et al. (2024). *On Mitigating Code LLM Hallucinations with API Documentation*. arXiv:2407.09726. https://arxiv.org/abs/2407.09726
  - Key limitation: Focuses on cloud SDK APIs (AWS, Azure) and code generation, not question answering over scikit-learn documentation.

## Engineering Gap / Limitation
General-purpose AI assistants can answer scikit-learn questions but may invent parameters, mix up versions, or give no source. Plain keyword search over the docs is authoritative but does not understand questions phrased in everyday language. What remains unresolved is a small, checkable assistant that answers only from one documentation version, cites the page, and refuses when the docs do not contain the answer.

## Initial Project Objectives (2-3)
- **O1:** To design a document-grounded assistant that answers scikit-learn questions using its official documentation.
- **O2:** To develop the assistant with retrieval, source citation and guardrails (out-of-scope refusal, "I don't know" when no relevant page is found).
- **O3:** To evaluate answer correctness, groundedness (correct source cited), retrieval accuracy, latency and refusal behaviour against a manually written question set and a plain keyword-search baseline.

## Target Users
Students and junior developers learning or using scikit-learn who need quick, sourced answers to "how do I...?" and "what does this parameter do?" questions.

## Data Sources (at least 2)
- **Source 1:** scikit-learn User Guide — https://scikit-learn.org/stable/user_guide.html
- **Source 2:** scikit-learn Frequently Asked Questions — https://scikit-learn.org/stable/faq.html
- **Source 3 (if any):** scikit-learn source repository, `doc/` folder (documentation source files) — https://github.com/scikit-learn/scikit-learn

> Check that each URL opens before submitting. The scikit-learn repository is under the 3-Clause BSD licence, but to stay safe only URLs and a download script are kept in this repo, not copies of the documentation. Fix one version (for example "stable" on a chosen date) and record it so answers can be checked.

## Similar to EduAdvisor
- Document-grounded question answering over official public documents.
- Same pipeline idea: load and clean documents, chunk, embed, retrieve, generate, then apply guardrails.
- Answers must cite their source, and evaluation covers answer quality, groundedness, latency and safety.

## Different from EduAdvisor
- Documents are web pages and `.rst` files with code blocks, not PDFs.
- Questions are technical (parameters, functions, examples), so code snippets must stay intact when chunking.
- Documentation changes between versions, so version control of the source docs matters.
- I can check answers myself because I already know the subject, which makes the test set easy to build.

## Responsible AI Risks (at least 2)
- **Risk 1: Hallucinated API details.** The assistant invents a parameter or function that does not exist, so the user writes code that fails or behaves wrongly.
- **Risk 2: Version mismatch.** The answer is correct for one scikit-learn version but wrong for the user's version, causing errors or deprecated usage.
- **Risk 3: Out-of-scope answers.** Asked about other libraries or general topics, the assistant answers from its own memory instead of the docs, losing the grounding guarantee.
- **Risk 4: Over-trust.** Users may copy code without testing it. Mitigation: always show the source page and a short "check the docs" note.
