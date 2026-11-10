# HPRC RAG Assistant

A locally deployed Retrieval-Augmented Generation (RAG) chatbot developed for **Texas A&M University's High Performance Research Computing (HPRC)** to provide researchers with accurate, system-specific answers to HPC questions using existing documentation and large language models (LLMs).

## Overview

Generic LLMs often lack institution-specific knowledge about HPC systems, which can lead to inaccurate recommendations, hallucinated information, and advice that conflicts with system administrators' guidelines.

The HPRC RAG Assistant addresses this problem by retrieving relevant information from HPRC documentation and providing it as context to an LLM before generating a response.

Designed to support **3,000+ researchers** at Texas A&M, the assistant helps users independently resolve common HPC questions, reducing reliance on HPRC staff and the need to submit support tickets.

The system is hosted locally on **ACES, Texas A&M's high-performance computing cluster**, using Intel Data Center GPU Max (PVC) nodes. Researchers can access the chatbot through an interactive Open OnDemand application within the HPC environment.

## Features

- **Retrieval-Augmented Generation:** Combines document retrieval with LLM-generated responses to provide accurate, context-aware answers.
- **Semantic Search:** Uses BGE embeddings to identify relevant documentation based on the meaning of a researcher's question.
- **Vector Search:** Uses FAISS to index document embeddings and efficiently retrieve relevant information.
- **Document Reranking:** Applies a BGE reranker to prioritize the most relevant documents before passing context to the LLM.
- **Locally Hosted LLMs:** Runs language models on Texas A&M's ACES supercomputing infrastructure using Intel PVC GPUs.
- **HPC-Specific Support:** Provides guidance based on existing HPRC documentation, including system-specific configurations, software, and workflows.
- **Reduced Support Requests:** Helps researchers find answers independently rather than submitting tickets for common HPC questions.

## Technologies Used

| Category | Technologies |
|---|---|
| Programming Languages | Python, Bash |
| AI/ML Frameworks | LangChain, PyTorch |
| Embedding Models | BAAI/bge-large-en-v1.5 |
| Reranking | BAAI/bge-reranker-large |
| Vector Search | FAISS |
| LLMs | Meta Llama 3, Llama 3.1, Llama 3.2, Llama 3.3 |
| LLM Inference | vLLM |
| GPU Computing | Intel Data Center GPU Max (PVC), Intel XPU |
| Development Environment | Linux, Intel oneAPI 2025.0 |
| User Interface | Open OnDemand |

## System Architecture

The RAG pipeline consists of document processing, embedding generation, vector indexing, information retrieval, reranking, and LLM response generation.

### 1. Document Processing

- Collected relevant pages from the HPRC website and knowledge base.
- Used BeautifulSoup to scrape and extract documentation from HTML pages.
- Cleaned and converted extracted content into Markdown.
- Split documents into **500-token chunks with 200-token overlap** to preserve context between sections.

### 2. Embedding Generation and Vector Indexing

- Used the `BAAI/bge-large-en-v1.5` embedding model to convert document chunks into numerical vector representations.
- Indexed embeddings using **FAISS (Facebook AI Similarity Search)** for efficient similarity-based retrieval.
- Converted incoming user queries into embeddings using the same model.

### 3. Retrieval and Reranking

- Retrieved the **top 4 most semantically similar document chunks** using FAISS.
- Applied `BAAI/bge-reranker-large` to score and reorder retrieved documents based on query relevance.
- Selected the **top 3 most relevant documents** to provide context to the LLM.

### 4. LLM Response Generation

- Passed the original user query and retrieved documentation to the LLM.
- Generated responses grounded in HPRC-specific information.
- Hosted local Llama models on Intel PVC GPUs using vLLM for inference.

## LLM Deployment on Intel GPUs

The chatbot is deployed on **Intel Data Center GPU Max 1100 (PVC) nodes** within Texas A&M's ACES HPC system.

To support LLM inference on Intel hardware:

- Built vLLM from source with Intel XPU dependencies.
- Configured the Intel oneAPI 2025.0 toolkit.
- Deployed smaller 3B and 8B models using two PVC GPUs on a single node.
- Configured the larger 70B model to use four GPUs with tensor parallelism.
- Evaluated quantized inference using Llama 3.1 8B AWQ INT4.

This deployment explored an alternative to NVIDIA-based LLM inference and leveraged the availability of Intel accelerators within the ACES environment.

## Performance & Evaluation

Evaluated four local Llama model configurations using a consistent set of 20 HPC-related benchmark questions. Each question was executed three times per model to measure inference performance.

### Model Performance

Measured time per output token (TPOT) to compare inference latency across models.

| Model | Mean TPOT |
|---|---:|
| Llama 3.2 3B Instruct | 37.73 ms |
| Llama 3.1 8B AWQ INT4 | 42.30 ms |
| Llama 3 8B Instruct | 55.81 ms |
| Llama 3.3 70B Instruct | 141.18 ms |

**Key Findings:**

- Retrieval and reranking added minimal latency, averaging approximately 0.02–0.03 seconds and 0.03–0.04 seconds, respectively.
- Llama 3.1 8B AWQ INT4 provided the strongest balance of inference speed and response quality among the local models.
- The RAG-based models generated system-specific responses across all 10 benchmark questions using retrieved HPRC documentation.
- Generic LLMs sometimes generated incorrect HPC configurations or declined to answer system-specific questions without access to HPRC documentation.

## Limitations & Future Improvements

The evaluation identified several areas for improvement:

- **Documentation Extraction:** Some HPRC pages use tabbed interfaces to display system-specific information. The current scraper only retrieved content from the default tab, resulting in incomplete documentation and occasional inaccurate responses.
- **Intel GPU Optimization:** Some quantization optimizations were originally designed for NVIDIA CUDA hardware and were not fully optimized for Intel PVC GPUs.
- **Model Performance:** Larger models provided competitive response quality but introduced significantly higher inference latency.

Future work includes improving documentation extraction, exploring Intel-specific quantization with Intel Neural Compressor, and evaluating additional model configurations.

## Code Availability

The source code for this project is not publicly available due to intellectual property restrictions. This repository provides an overview of the system architecture, implementation approach, technologies, and research findings.
