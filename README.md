# Vision-Based Retrieval-Augmented Generation for Document QA

This project builds and compares document question-answering systems for visually rich pages. The focus is on pages where charts, tables, figures, and dense layouts matter as much as the raw text. The core question is whether a page-image retrieval model can outperform a conventional OCR-based retrieval pipeline when the answer depends on visual structure rather than a clean text transcript.

## Overview

Many document question-answering systems rely on OCR to convert each page into text before retrieval. This works well for straightforward text-heavy documents, but it loses information that is encoded visually: layout, diagram structure, chart values, table relationships, and the placement of relevant content on the page.

This project evaluates two retrieval strategies on the same document question sets:

- a text-based pipeline built from OCR text and traditional retrieval
- a vision-based pipeline that retrieves from page images directly and passes the retrieved images to a vision-language model

The work also includes lower and upper bound runs to separate retrieval failures from generator failures and to better understand where the performance gaps come from.

## System Design

### 1. TextRAG baseline

The text pipeline follows the standard OCR-first workflow:

- OCR is extracted for each document page using EasyOCR
- retrieval is performed over OCR text with BM25 and a dense embedding model
- the stronger text retriever is selected for evaluation
- the top 1 and top 3 pages are passed to the generator

The notebook implements the following system names:

- `BM25-OCR`
- `BGE-OCR`
- `TextRAG-top1`
- `TextRAG-top3`

### 2. Vision-based RAG

The vision pipeline avoids OCR during retrieval and instead uses a page-image retriever based on a ColPali-style vision-language model. The retriever reads page images directly and ranks pages by similarity to the question.

The generated answer is then based on the top 1 or top 3 retrieved page images.

The system names in this pipeline are:

- `VisRet`
- `VisRAG-top1`
- `VisRAG-top3`

### 3. Lower and upper bounds

To isolate the source of error, the project includes reference systems that show the best possible performance under ideal conditions:

- `NoRetrieval`: answer the question without document context
- `Oracle-text`: provide the generator with the OCR text of the correct page
- `Oracle-image`: provide the generator with the correct page image

These bound conditions help to distinguish errors caused by weak retrieval from errors caused by the generation model itself.

## Datasets and Evaluation

The project uses document QA datasets designed for visually rich content, including:

- MP-DocVQA
- InfoVQA
- optional additional sets such as SlideVQA and ChartQA for broader evaluation

Each dataset includes page-level images, questions, gold answers, and relevant-page annotations for retrieval.

### Retrieval metrics

The project reports:

- Recall@1
- Recall@5
- MRR@10
- nDCG@5

### Answer quality

Answer correctness is measured with VQA-style normalization, which strips casing, punctuation, and articles from the response before comparison. For chart-related questions, numeric answers are also judged with a tolerance-based rule.

## Repository Contents

The repository contains the main experimental notebooks used for the work:

- `TextRAG.ipynb` — OCR-based baseline pipeline, retrieval comparison, and text-only RAG experiments
- `VisionRAG.ipynb` — vision-based retrieval pipeline, bounded runs, and multimodal evaluation

## Core Research Questions

This project is designed to answer several practical questions:

- When does OCR-based retrieval remain preferable to vision-based retrieval?
- How much of the observed error comes from retrieval versus generation?
- Which document types benefit most from page-level visual understanding?
- Can multimodal retrieval and reranking improve the results over a purely text-based baseline?

## Summary

This project sits at the intersection of multimodal retrieval, document understanding, and retrieval-augmented generation. It evaluates whether image-native retrieval can better capture the information encoded in visually structured documents, and it places that comparison against a strong OCR-driven baseline to understand when each approach is most effective.
