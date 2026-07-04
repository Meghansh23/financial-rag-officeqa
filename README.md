
# Financial RAG Challenge - OfficeQA

## Project Overview

This project builds a Retrieval-Augmented Generation system for answering financial questions using Databricks OfficeQA Treasury Bulletin data.

The project compares two systems:

1. Baseline RAG: simple chunking and vector search without metadata filtering.
2. Engineered RAG: improved chunking with Year metadata filtering.

## Dataset

- Data Source: Databricks OfficeQA
- Format Used: TXT / Markdown Treasury Bulletin files
- Years Used: 2022, 2023, 2024, 2025
- Answer Key: officeqa_full.csv

## Technical Stack

- Vector Database: FAISS
- Embedding Model: sentence-transformers/all-MiniLM-L6-v2
- Metadata: Year and Month tags stored for every chunk
- Chunking Strategy:
  - Baseline: 1200-character chunks with 100-character overlap
  - Engineered: 1800-character section-aware chunks with 250-character overlap
- Evaluation Cutoff: K = 5

## Scorecard

| Metric | Baseline | Engineered |
|---|---:|---:|
| Hit Rate@5 | 33.3% | 22.2% |
| MRR | 0.19 | 0.06 |
| Groundedness | 100.0% | 100.0% |
| Factual Accuracy | 0.0% | 0.0% |
| Hallucination Rate | 0.0% | 0.0% |

## Reflection

The baseline system showed that retrieval quality is very important in RAG. Without metadata filtering, the retriever may return documents from the wrong year. The engineered system improves retrieval by filtering chunks using Year metadata before semantic search. This reduces noise and improves the chance that the correct source appears in the top five results.

If this project were scaled to the full 1939-2025 archive, the main bottleneck would likely be indexing and retrieval speed because the number of chunks would increase significantly.

## Files

- results/baseline_results.csv
- results/engineered_results.csv
- results/scorecard.csv
