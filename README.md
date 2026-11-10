# Vision-Based RAG for Documents

This repository contains a document question-answering project focused on visually rich pages, where answers often depend on charts, tables, figures, diagrams, and layout rather than plain text alone. The work compares OCR-based retrieval with page-image retrieval and tests ways to improve the vision pipeline.

## Goal

The main objective is to study how a vision-language retrieval pipeline performs against a conventional text-based retrieval pipeline on document QA tasks. Rather than treating each page only as OCR text, the project evaluates whether page images preserve information that is lost in OCR pipelines.

The repo implements and compares systems such as:

- `BM25-OCR`
- `BGE-OCR`
- `TextRAG-top1`
- `TextRAG-top3`
- `VisRet`
- `VisRAG-top1`
- `VisRAG-top3`
- `NoRetrieval`
- `Oracle-text`
- `Oracle-image`

## Why this project matters

Most document QA systems rely on OCR to convert each page into text before retrieval. That approach is useful for text-heavy pages, but it can fail when the relevant information is structured visually. For example:

- tables with multi-row alignment
- charts and graphs with plotted values
- figures and diagrams with semantics in the layout
- documents with dense visual hierarchy and small text

The project tests whether a page-image retriever can recover that information more effectively than OCR-based retrieval.

## Experimental setup

The project uses document QA datasets from the VisRAG retrieval benchmark, including:

- MP-DocVQA
- InfoVQA

The base workflow is consistent across systems:

1. Load the corpus, query set, and qrels.
2. Sample a fixed subset of evaluation questions.
3. Run OCR-based retrieval and page-image retrieval.
4. Pass the top retrieved pages to a multimodal generator.
5. Measure retrieval metrics and answer accuracy.
6. Compare against oracle and no-retrieval baselines.

## Notebooks in this repository

### Root-level notebooks

- `TextRAG.ipynb` — OCR baseline experiments and standard text retrieval pipeline.
- `VisionRAG.ipynb` — page-image retrieval pipeline with VisRAG and oracle bounds.

### Dataset-specific folders

- `MP-DocVQA/MP-DocVQA TextRAG.ipynb` — OCR-based text pipeline for MP-DocVQA.
- `MP-DocVQA/MP-DocVQA VisRAG.ipynb` — vision-based pipeline for MP-DocVQA.
- `MP-DocVQA/MP-DocVQA improvements.ipynb` — improved retrieval/generation strategies.
- `MPDocVQA_hybrid_abstain.ipynb` — hybrid retrieval and abstention experiments.
- `InfoVQA/InfoVQA TextRAG.ipynb` — OCR baseline for InfoVQA.
- `InfoVQA/InfoVQA VisRAG.ipynb` — vision-based pipeline for InfoVQA.
- `InfoVQA/InfoVQA improvements.ipynb` — improved document QA methods for InfoVQA.
- `InfoVQA_hybrid_abstain.ipynb` — hybrid and abstention analysis for InfoVQA.

## Improvement work

The repo includes several improvement directions beyond the basic baseline:

### IMP-zoom

The improvement notebook explores a tile-based zoom strategy for the retrieved page. The idea is to avoid the default downscaling that blurs small text, then crop the page into native-resolution tiles, select the most relevant tile with the vision retriever, and send that tile to the generator with the full page as context.

The notebook defines experiments such as:

- `IMP-zoom-top1`
- `IMP-zoomonly-top1`
- `IMP-zoom-oracle`

### Hybrid and abstention behavior

The hybrid-abstain notebooks explore whether the retriever score can indicate when the page is wrong or when the model should say "I do not know" instead of guessing. This is a practical extension for real applications where incorrect answers are more harmful than abstention.

## Evaluation metrics

The project measures both retrieval quality and answer correctness.

### Retrieval metrics

- Recall@1
- Recall@5
- MRR@10
- nDCG@5

### Accuracy metrics

The answer is considered correct with VQA-style normalization, and chart-based numeric answers are compared with a tolerance rule. The notebooks compare systems on the same question subsets and report the resulting system scores.

## Repository outputs

The project includes generated outputs and packaged artifacts:

- `report.pdf` — final report output
- `visionRAG.zip` — packaged project archive

## Summary

This repository is a full end-to-end exploration of multimodal document retrieval and generation. It compares OCR-first RAG with image-first RAG, studies the retrieval/generation gap with bounds, and tests improvements designed for visually complex documents. The code reflects a research-and-experiment workflow rather than a single final script, with separate notebook pipelines for each dataset and improvement direction.
