# Financial RAG Challenge

## Project Overview

This project builds and evaluates a Retrieval-Augmented Generation (RAG) system using the Databricks OfficeQA dataset and transformed U.S. Treasury Bulletin text files.

The goal was to compare a simple baseline RAG system with an engineered RAG system for financial question answering.

## Recent Years Used

I used Treasury Bulletin files from:

2020, 2021, 2022, 2023, 2024, and 2025

After filtering OfficeQA questions to these source years, the final evaluation set contained 9 questions.

## Dataset

Dataset: `databricks/officeqa`

The notebook downloads the required Treasury Bulletin `.txt` files from Hugging Face after authentication.

## RAG Systems Compared

### Baseline System

The baseline system used:
- Simple fixed-size text chunks
- No overlap
- SentenceTransformer embeddings
- ChromaDB vector search
- Top-5 retrieval

### Engineered System

The engineered system used:
- RecursiveCharacterTextSplitter
- Larger chunks with overlap
- Metadata tagging using source file, year, and month
- Year-based metadata filtering
- Special handling for year-end / calendar-year questions
- Neighbor/source expansion for generator context when tables were split across chunks

## Evaluation Metrics

Retriever metrics:
- Hit Rate@5
- Mean Reciprocal Rank (MRR)

Generator metrics:
- Groundedness
- Factual Accuracy
- Hallucination Rate

Because I did not use an OpenAI API key, I evaluated the generator manually by checking whether the retrieved context contained enough evidence to support the gold answer.

## Final Scorecard

| Metric | Baseline (Simple) | Engineered (Improved) |
|---|---:|---:|
| Hit Rate (K=5) | 11.11% | 66.67% |
| MRR | 0.06 | 0.43 |
| Groundedness | 0.00% | 22.22% |
| Factual Accuracy | 0.00% | 22.22% |
| Hallucination Rate | 100.00% | 77.78% |

## Results Summary

The engineered RAG system improved retrieval performance significantly compared with the baseline. Hit Rate (k=5) increased from 11.11% to 66.67%, and MRR increased from 0.06 to 0.43.

Generation performance was still challenging because many questions required values spread across multiple tables, adjacent chunks, or multiple source files. After adding metadata filtering and neighbor/source expansion, the engineered system achieved 22.22% groundedness and factual accuracy, while reducing hallucination rate from 100.00% to 77.78%.

## Files Included

- `notebooks/financial_rag_challenge.ipynb`: Main notebook
- `results/final_scorecard.csv`: Final scorecard
- `results/baseline_retriever_details.csv`: Baseline retriever details
- `results/engineered_retriever_details.csv`: Engineered retriever details
- `results/manual_generator_evaluation_completed.csv`: Manual generator evaluation
- `docs/reflection.md`: Engineering reflection
