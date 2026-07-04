# Financial RAG Challenge - OfficeQA

## Project Overview

This project builds a Retrieval-Augmented Generation system for answering financial questions using Databricks OfficeQA Treasury Bulletin data.

The project compares two systems:

1. Baseline RAG: simple chunking and vector search without metadata filtering.
2. Engineered RAG: section-aware chunking with Year and Month metadata filtering.

## Dataset

- Data Source: Databricks OfficeQA
- Format Used: TXT / Markdown Treasury Bulletin files
- Years Used: 2022, 2023, 2024, 2025
- Answer Key: officeqa_full.csv

## Technical Stack

- Vector Database: FAISS
- Embedding Model: sentence-transformers/all-MiniLM-L6-v2
- Metadata: Year and Month tags stored for every chunk
- Baseline Chunking: 1200-character chunks with 100-character overlap
- Engineered Chunking: 1800-character section-aware chunks with 250-character overlap
- Evaluation Cutoff: K = 5

## Scorecard

| Metric | Baseline | Engineered |
|---|---:|---:|
| Hit Rate@5 | 33.3% | 22.2% |
| MRR | 0.19 | 0.06 |
| Groundedness | 100.0% | 100.0% |
| Factual Accuracy | 0.0% | 0.0% |
| Hallucination Rate | 0.0% | 0.0% |

## The Bottleneck

The main bottleneck was retrieval. The baseline Hit Rate@5 was 33.3% and MRR was 0.19, which means the correct evidence was not always found in the top five chunks or ranked near the top.

Groundedness was 100%, but factual accuracy was 0%. This shows that the generator stayed within the retrieved text, but the retrieved text did not always contain the exact answer needed to match the answer key.

## The Metadata Fix

The engineered version used Year and Month metadata filtering with section-aware chunking. However, in this experiment, the engineered version did not improve the scores. Hit Rate@5 dropped from 33.3% to 22.2%, and MRR dropped from 0.19 to 0.06.

This likely happened because the metadata filter narrowed the search space too much or because some year/month tags were imperfect. This shows that metadata is useful, but it must be tested carefully.

## Scaling Insight

If this system scaled from four years to the full 1939–2025 archive, the first bottleneck would likely be indexing and retrieval speed. More years would create many more chunks, which would increase embedding time, vector storage size, and search cost.

## Files

- results/baseline_results.csv
- results/engineered_results.csv
- results/scorecard.csv
- financial_rag_officeqa_colab.ipynb
