# TAMU HPRC RAG Assistant

An AI-powered Retrieval-Augmented Generation (RAG) assistant developed for Texas A&M University's High Performance Research Computing (HPRC) to help over 3,0000+ student and staff researchers quickly get information to questions about HPC resources and documentation.

The source code for this project is not publicly available due to Texas A&M HPRC policies. This repository serves as a portfolio overview of the project's architecture, technologies, and development.

## Overview

The HPRC RAG Assistant uses semantic search and large language models (LLMs) to provide context-aware answers to technical questions about high-performance computing. The system scrapes relevant information from existing HPRC documentation available online publically and uses that information to generate helpful responses.

The goal is to help researchers independently resolve common HPC questions, reducing the need to submit support tickets to HPRC staff.

The LLM is hosted locally on ACES, Texas A&M's high-performance computing cluster, using Intel Data Center GPU Max (PVC) nodes. This allows the assistant to run on university-managed supercomputing infrastructure rather than relying on external LLM hosting services.

## Features

- **Retrieval-Augmented Generation:** Combines document retrieval with LLM-generated responses to provide context-aware answers.
- **Semantic Search:** Uses BGE embeddings to identify relevant documentation based on the meaning of a researcher's question.
- **Vector Search:** Uses FAISS to efficiently index and retrieve relevant document embeddings.
- **Context-Aware Responses:** Supplies retrieved documentation to the LLM to generate responses grounded in relevant HPC resources.
- **Linux/HPC Integration:** Runs within the Linux-based HPC environment at Texas A&M.

## Technologies Used

| Category | Technologies |
|---|---|
| Programming Language | Python, Bash |
| AI/ML Frameworks | LangChain |
| Embedding Models | BGE (BAAI General Embedding) |
| Vector Database | FAISS |
| LLMs | Meta Llama |
| Infrastructure | Linux, HPC |
| GPU Computing | Intel Data Center GPU Max |

## System Architecture

The RAG pipeline consists of document processing, embedding generation, vector indexing, information retrieval, and response generation.

### Workflow

1. **Document Processing:** HPRC documentation is collected, cleaned, and divided into smaller text chunks.
2. **Embedding Generation:** BGE embedding models convert document chunks into numerical vector representations.
3. **Vector Indexing:** FAISS stores and indexes the embeddings to enable efficient similarity searches.
4. **Query Processing:** A researcher submits a question, which is converted into an embedding using the same embedding model.
5. **Information Retrieval:** FAISS identifies the most relevant document chunks based on vector similarity.
6. **Response Generation:** LangChain passes the retrieved context and original question to the LLM, which generates a relevant response.

## Performance & Evaluation

The assistant was developed and evaluated to improve the speed and usefulness of AI-powered support for HPC researchers.

- Indexed information from HPRC documentation to support questions from 3,000+ researchers.
- Used BGE embeddings and FAISS vector search to retrieve relevant information.
- Evaluated multiple LLM configurations for response quality and inference performance.
- Explored deployment and optimization on Intel Data Center GPU Max resources.
