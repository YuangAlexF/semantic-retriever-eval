# Semantic Retriever Evaluation and Benchmarking

This repository contains an evaluation pipeline for benchmarking **semantic retrievers** on the [BEIR-FiQA](https://public.ukp.informatik.tu-darmstadt.de/thakur/BEIR/datasets/fiqa.zip) dataset.  
It was developed as part of a UChicago Applied Data Science capstone project on **Evaluating and Benchmarking Semantic Retrievers for Conversational AI**.

The notebook implements reproducible comparisons between dense retrievers (DPR, E5, MPNet) and integrates an **LLM-as-a-Judge** layer for qualitative assessment.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Architecture](#architecture)
3. [Models Evaluated](#models-evaluated)
4. [Metrics](#metrics)
5. [Example Output](#example-output)

---

## Project Overview

Semantic retrievers play a central role in conversational and retrieval-augmented systems.  
This project provides a controlled evaluation environment to compare multiple retrievers across:

- **Quantitative metrics:** nDCG@K, Recall@K, Precision@K, and query latency  
- **Qualitative metrics:** LLM-based relevance judgments (1–5 scale)

The framework is modular and can easily extend to new datasets or retrieval models.

---

## Architecture

| Stage | Description |
|--------|--------------|
| **Dataset Loader** | Loads BEIR-FiQA via `GenericDataLoader`; subsets 200 queries for now |
| **Retriever Registry** | Configurable list of dense retrievers (e.g., DPR, E5, MPNet) |
| **Evaluator** | Computes nDCG, Recall, Precision, and average latency |
| **LLM-Judge** | Uses an Ollama-hosted model (e.g., `llama3.1:8b-instruct-q8_0`) to score retrieval quality |
| **Report Generator** | Outputs per-model metrics and ranked recommendations to `/reports/` |

---

## Models Evaluated

| Model | Type | HF ID |
|--------|------|-------|
| DPR | Dense | `facebook-dpr-question_encoder-single-nq-base` |
| E5 | Dense | `intfloat/e5-base-v2` |
| MPNet | Dense | `sentence-transformers/multi-qa-mpnet-base-dot-v1` |

LLM-Judge model used: **`llama3.1:8b-instruct-q8_0`** via [Ollama](https://ollama.com/).

Use:
**`ollama run llama3.1:8b-instruct-q8_0`**


---

## Metrics

| Metric | Description |
|---------|--------------|
| **nDCG@K** | Normalized Discounted Cumulative Gain – measures ranking quality |
| **Recall@K** | Fraction of relevant docs retrieved |
| **Precision@K** | Fraction of retrieved docs that are relevant |
| **Avg Latency (ms)** | Mean per-query inference time |
| **JudgeOverall@topK** | Average LLM-judge score across queries (1–5 scale) |

All metrics are computed using BEIR’s built-in evaluator.

Metrics and recommendations will be saved to:
**`./reports/fiqa_report.json`**

---

## Example Output

##### Recommendation (BEIR): MPNet > E5 > DPR
##### Judge ranking: MPNet > E5 > DPR

| Model | nDCG@1 | nDCG@3 | nDCG@10 | Recall@10 | P@10 | Avg Latency (ms) |
| ----- | ------ | ------ | ------- | --------- | ---- | ---------------- |
| E5    | 1.0000 | 1.0000 | 1.0000  | 1.0       | 0.1  | 28.91            |
| MPNet | 1.0000 | 1.0000 | 1.0000  | 1.0       | 0.1  | 28.08            |
| DPR   | 0.5714 | 0.7517 | 0.7947  | 1.0       | 0.1  | 31.92            |
