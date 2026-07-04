# Financial RAG Challenge - OfficeQA

## Project Overview

This project builds a Retrieval-Augmented Generation (RAG) system for answering financial questions using the Databricks OfficeQA U.S. Treasury records dataset.

The project compares two versions of the system:

1. **Baseline RAG**: A simple version using basic chunking and vector search.
2. **Engineered RAG**: An improved version using section-aware chunking and Year/Month metadata filtering.

The purpose of this project is to show how engineering choices such as chunking, metadata, and retrieval filtering affect RAG performance.

## Dataset

- **Data Source**: Databricks OfficeQA
- **Data Format Used**: TXT / Markdown Treasury Bulletin files
- **Recent Years Used**: 2022, 2023, 2024, 2025
- **Answer Key**: officeqa_full.csv
- **Evaluation Cutoff**: K = 5

## Technical Stack

| Component | Tool / Method |
|---|---|
| Vector Database | FAISS |
| Embedding Model | sentence-transformers/all-MiniLM-L6-v2 |
| Metadata | Year and Month tags |
| Baseline Chunking | 1200-character chunks with 100-character overlap |
| Engineered Chunking | 1800-character section-aware chunks with 250-character overlap |
| Evaluation | Hit Rate@5, MRR, Groundedness, Factual Accuracy, Hallucination Rate |

## Repository Structure

```text
financial-rag-officeqa/
├── data/
│   └── docs/
├── results/
│   ├── baseline_results.csv
│   ├── engineered_results.csv
│   └── scorecard.csv
├── financial_rag_officeqa_colab.ipynb
├── README.md
└── requirements.txt
```

## Architecture

```text
OfficeQA TXT / Markdown Files
        ↓
Load Treasury Bulletin Documents
        ↓
Create Baseline Chunks and Engineered Chunks
        ↓
Generate Embeddings with MiniLM
        ↓
Store and Search Vectors using FAISS
        ↓
Retrieve Top 5 Chunks
        ↓
Generate Answer from Retrieved Context
        ↓
Compare with officeqa_full.csv Answer Key
        ↓
Calculate Baseline vs Engineered Metrics
```

## Scorecard

| Metric | Baseline (Simple) | Engineered (Improved) |
|---|---:|---:|
| Hit Rate@5 | 33.3% | 22.2% |
| MRR | 0.19 | 0.06 |
| Groundedness | 100.0% | 100.0% |
| Factual Accuracy | 0.0% | 0.0% |
| Hallucination Rate | 0.0% | 0.0% |

## Engineering Reflection

### 1. The Bottleneck

The main bottleneck in the baseline system was retrieval. The baseline system was able to retrieve some relevant chunks, but the Hit Rate@5 was only 33.3% and the MRR was 0.19. This means the correct source was not always appearing in the top five results, and even when it did appear, it was often not ranked first.

The generator was highly grounded because the answers came directly from retrieved context, but factual accuracy was still 0.0%. This shows that being grounded is not the same as being correct. The system may stay inside the retrieved text, but if the retrieved text is not the exact answer context, the final answer will still not match the answer key.

### 2. The Metadata Fix

The engineered system added Year and Month metadata and used section-aware chunking. However, in this experiment, the engineered system did not improve the retrieval metrics. Hit Rate@5 decreased from 33.3% to 22.2%, and MRR decreased from 0.19 to 0.06.

This likely happened because the metadata filter narrowed the search space too much or because the year/month detection was not perfect for every question and document. If the filter removes useful chunks, then the retriever has fewer chances to find the correct evidence. This was an important finding because it shows that metadata is powerful, but it must be accurate and carefully tested.

### 3. Scaling Insight

If this project were scaled from four recent years to the full 1939–2025 archive, the first component that would likely become too slow is the retrieval and indexing pipeline. The number of chunks would grow significantly, which would increase embedding generation time, vector database size, and search cost.

To scale this system, I would use stronger metadata partitioning, batch embedding generation, persistent FAISS indexes, and possibly a reranking step. I would also improve document parsing and table-aware chunking so that financial tables are not split incorrectly.

## Key Learning

This project showed that a RAG system depends heavily on the retriever. A generator can only answer correctly if the retrieved context contains the correct evidence. The baseline is important because it gives a comparison point. In this run, the engineered system did not outperform the baseline, but that result is still useful because it shows that metadata filtering must be tested carefully instead of assuming it will always improve performance.

## Results Files

- `results/baseline_results.csv`
- `results/engineered_results.csv`
- `results/scorecard.csv`

## How to Run

Install dependencies:

```bash
pip install -r requirements.txt
```

Then open and run:

```text
financial_rag_officeqa_colab.ipynb
```

The notebook loads the OfficeQA data, builds baseline and engineered RAG systems, evaluates both systems, and saves the final results into the `results/` folder.
