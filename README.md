<div align="center">
  <img src="https://img.shields.io/badge/Research-USP%20ICMC-F7DF1E?style=for-the-badge&logo=school" alt="USP Research">
  <h1>Legal RAG Assistant (LRA)</h1>
  <p>
    <b>Context-Aware Legal Analysis using Retrieval-Augmented Generation (RAG) and LLMs.</b>
  </p>

  <p>
    <a href="#-problem-statement">The Problem</a> •
    <a href="#-architecture">Architecture</a> •
    <a href="#-hallucination-guardrails">Anti-Hallucination</a> •
    <a href="#-tech-stack">Tech Stack</a> •
    <a href="#-demo">Demo</a>
  </p>

  <img src="https://img.shields.io/badge/LangChain-🦜🔗-black?style=for-the-badge" alt="LangChain">
  <img src="https://img.shields.io/badge/LLM-OpenAI%20%2F%20Llama3-blue?style=for-the-badge" alt="LLM">
  <img src="https://img.shields.io/badge/Vector_DB-Chroma-orange?style=for-the-badge" alt="ChromaDB">
  <img src="https://img.shields.io/badge/Focus-NLP%20%26%20Law-green?style=for-the-badge" alt="LegalTech">
</div>

## 📂 Estrutura do Repositório
Para um projeto de GenAI, a estrutura deve mostrar que você sabe organizar prompts e dados.
```
/legal-rag-assistant
│
├── /data
│   ├── /raw_contracts       # PDFs de exemplo (Anonimizados)
│   └── /vector_store        # Banco de dados vetorial persistido (ChromaDB)
│
├── /src
│   ├── ingest.py            # Script de Chunking e Embedding
│   ├── rag_engine.py        # Lógica de Retrieval e Chain
│   └── prompts.py           # Templates de Prompt (Engenharia de Prompt)
│
├── /app
│   └── streamlit_app.py     # Interface de Chat (Frontend)
│
├── .env.example             # Exemplo de config (API Keys)
├── requirements.txt
└── README.md
```
---

## ⚖️ Problem Statement

Large Language Models (LLMs) like GPT-4 are powerful but suffer from two critical flaws in the legal domain:
1.  **Hallucination:** Inventing laws or clauses that do not exist.
2.  **Context Window Limits:** Inability to process thousands of pages of case files at once.

**The Solution:**
The **Legal RAG Assistant** is a prototype developed as part of my Master's research at **USP (University of São Paulo)**. It uses a **Retrieval-Augmented Generation (RAG)** architecture to strictly ground the AI's answers in the provided documents, citing specific pages for every claim.

---

## 🏗️ Architecture

The system follows a modern "Naïve RAG" to "Advanced RAG" pipeline:

1.  **Ingestion:** Legal PDFs are parsed and split into semantic chunks (using recursive character splitting to preserve context).
2.  **Embedding:** Text chunks are converted into vector representations using `text-embedding-3-small` (OpenAI) or `HuggingFace InstructEmbeddings`.
3.  **Retrieval:** When a user asks a question, the system performs a **Cosine Similarity Search** in the Vector Database (ChromaDB) to find the top-k most relevant contract clauses.
4.  **Generation:** The LLM receives the question + the retrieved context and generates an answer with strict instructions to **cite sources**.

```mermaid
graph LR
    A[User Question] --> B(Embedding Model)
    B --> C{Vector Store Search}
    D[Legal Documents] --> E[Chunking] --> F[Embeddings] --> C
    C -->|Top-3 Contexts| G[LLM Context Window]
    A --> G
    G --> H[Final Answer with Citations]
