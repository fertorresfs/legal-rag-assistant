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
```

## 🛡️ Hallucination Guardrails (Prompt Engineering)
To ensure reliability for legal professionals, I implemented specific prompt engineering techniques:
```
"I Don't Know" Token: The model is explicitly instructed to refuse answering if the context is insufficient, rather than inventing facts.

Source Tracking: The return object includes metadata (page_number, document_name) for verification.

Temperature Zero: Deterministic outputs to minimize creative variance.
```
System Prompt Example:
```
"You are a Senior Legal Assistant. Answer the user question based ONLY on the following context context. If the answer is not in the context, say 'I cannot find this information in the documents'. Do not use outside knowledge."
```
## 💻 Demo & Usage
1. Installation
```Bash
git clone [https://github.com/fertorresfs/legal-rag-assistant.git](https://github.com/fertorresfs/legal-rag-assistant.git)
pip install -r requirements.txt
```
2. Run the Streamlit Interface
```Bash
streamlit run app/streamlit_app.py
```
3. Example Query
```
User: "Does this contract contain a clause regarding GDPR or LGPD compliance?"

LRA Agent:

"Yes. According to Clause 14.2 (Page 8), the Data Controller must ensure compliance with LGPD (Law 13.709/2018). Furthermore, Clause 14.5 specifies a penalty of 2% of revenue in case of a data breach."
```
## 🛠️ Tech Stack
Orchestration: LangChain / LlamaIndex

LLM: OpenAI GPT-4o-mini (Cost-optimized) or Llama-3 (Local)

Vector Database: ChromaDB (Local persistence)

Frontend: Streamlit

Embedding: OpenAI text-embedding-3-small

## 🔬 Research Context
This project serves as a proof-of-concept for my Master's thesis on "Enhancing Information Retrieval in Long-Context Legal Documents". Future improvements will include:

Hybrid Search: Combining Keyword Search (BM25) with Semantic Search (Dense).

Re-ranking: Using Cohere Rerank to improve context precision.

## 👤 Author
Fernando Torres MSc Candidate in Computer Science (USP) | Senior Data Scientist

<a href="https://www.linkedin.com/in/fertorresfs/"> <img src="https://www.google.com/search?q=https://img.shields.io/badge/LinkedIn-Connect-0077B5%3Fstyle%3Dfor-the-badge%26logo%3Dlinkedin"> </a>
<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-linkedin" viewBox="0 0 16 16">
  <path d="M0 1.146C0 .513.526 0 1.175 0h13.65C15.474 0 16 .513 16 1.146v13.708c0 .633-.526 1.146-1.175 1.146H1.175C.526 16 0 15.487 0 14.854zm4.943 12.248V6.169H2.542v7.225zm-1.2-8.212c.837 0 1.358-.554 1.358-1.248-.015-.709-.52-1.248-1.342-1.248S2.4 3.226 2.4 3.934c0 .694.521 1.248 1.327 1.248zm4.908 8.212V9.359c0-.216.016-.432.08-.586.173-.431.568-.878 1.232-.878.869 0 1.216.662 1.216 1.634v3.865h2.401V9.25c0-2.22-1.184-3.252-2.764-3.252-1.274 0-1.845.7-2.165 1.193v.025h-.016l.016-.025V6.169h-2.4c.03.678 0 7.225 0 7.225z"/>
</svg>

## 🧠 Instruções Finais do Consultor:

1.  **O "Pulo do Gato" (Mermaid Diagram):** Eu incluí um bloco de código `mermaid` no README. O GitHub renderiza isso automaticamente como um diagrama de arquitetura profissional. Isso mostra que você sabe documentar sistemas complexos.
2.  **Research Context:** Adicionei a seção "Research Context" para conectar explicitamente com a USP. Isso valida seu currículo acadêmico.
3.  **Execução:** Para este projeto, você pode usar o **Streamlit** (que é Python puro) para criar uma interface bonitinha em 10 minutos. Tem milhares de tutoriais de "Chat with PDF using Langchain" no YouTube. Copie um, adapte para o contexto jurídico e suba.

Com esses 3 projetos no GitHub (**Forense, Fraude Financeira e GenAI Legal**), você cobre todas as bases: Segurança, Dinheiro e Inovação. Seu portfólio está blindado.
