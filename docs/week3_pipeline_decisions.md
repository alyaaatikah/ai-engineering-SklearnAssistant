# Week 3 Pipeline Decisions — SklearnDocs Assistant

## 1. Traceability & Selected Source
- **Linked Topic 2 Requirements:** `FR1` (documentation-grounded answers), `FR2` (source citation), `FR3` (explicit version tracking `1.9.1`), `RAI1` (balanced topic coverage), and `RAI4` (preserving exact code/parameter syntax).
- **Selected Pathway:** Pathway A (Unstructured Text — `.txt` files fetched from official `scikit-learn` v1.9.1 `.rst` documentation sources).
- **Primary Pipeline Source:** `linear_model.txt` (`S01`, 86,036 bytes, SHA-256 `b9d050bf8b2ca47fb0b305255631629ef410ccf2944f198c7297f4e7b96266dc`).

## 2. Part B Preparation Comparison & Evidence

| Approach A | Approach B | Observed Evidence (`linear_model.txt`) | Selected Approach & Trade-off |
|---|---|---|---|
| **Fixed-size chunking** (`size=500` characters) | **Paragraph-aware chunking** (`size=500` characters, splitting on `\n\s*\n`) | Raw characters: `86,023` → Cleaned characters: `82,897`.<br>- **Fixed-size:** `166` chunks; cuts mid-sentence and splits LaTeX/doctest blocks across boundaries.<br>- **Paragraph-aware:** `225` chunks (`133,432` bytes exported); preserves complete paragraphs and math definitions (e.g., record `linear_model-0001` keeps the entire Linear Models intro and `\hat{y}` equation intact). | **Selected: Paragraph-aware (`paragraph-aware-char-500`).**<br>**Benefit:** Directly supports `RAI4` and `FR1` by preventing arbitrary mid-sentence/mid-equation splits.<br>**Accepted Limitation:** Produces more total chunks (`225` vs `166`), and any single paragraph or code block exceeding 500 characters falls back to fixed character slicing. |

## 3. Architecture & Engineering Trade-offs
- **ETL vs. ELT:** **ETL (Extract, Transform, Load)** is used. Raw documentation is extracted from GitHub, cleaned (horizontal whitespace normalization) and segmented into structured JSONL records (`record_id`, `text`, `source`, `source_sha256`, `processing`) before saving to processed storage.
- **Batch vs. Streaming:** **Batch processing** is selected because `scikit-learn` releases versioned documentation snapshots periodically (e.g., tag `1.9.1`) rather than as continuous real-time event streams.
- **Storage & Provenance Caveat:** Raw files are held in temporary Colab storage (`data/raw/`) during execution and excluded from Git commits to keep the repository lightweight; full reproducibility is preserved via pinned GitHub `1.9.1` URLs, `data/LICENSE-scikit-learn.txt`, `data/week3_manifest.json`, and `data/pipeline_v1/documents/pipeline_manifest.json`.

## 4. Stage Contracts & Failure Responses

| Stage | Contract / Evidence | Failure Response |
|---|---|---|
| **Ingest** | Selected source file exists in `data/raw/` via explicit `resolve_source()` / `select_source()`. | Raise `FileNotFoundError` and halt execution immediately. |
| **Inspect** | File decodes as valid UTF-8 and contains non-empty readable characters (`86,023` chars). | Raise `ValueError` / `AssertionError` if extracted text is empty. |
| **Prepare** | Whitespace normalization and paragraph-aware chunking (`500` chars) produce non-empty text segments. | Raise `ValueError` if chunk size `<= 0` or zero chunks are produced. |
| **Validate** | Output `prepared_text.jsonl` exists on disk, has byte size `> 0` (`133,432` bytes), and passes repeatability SHA-256 smoke checks (Cell 9). | Raise `AssertionError` / `FileNotFoundError` if output is missing, empty, or non-deterministic across reruns. |
| **Record Lineage** | `week3_manifest.json` and `pipeline_manifest.json` map exact source SHA-256 to output SHA-256 and processing metadata. | Raise `AssertionError` on checksum mismatch or missing lineage entries. |
