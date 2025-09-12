# 💹 Live Fintech RAG --- Gemini + Pathway

![Frontend Screenshot](Screenshot%202025-09-12%20235254.png)

## 📌 Problem Statement

In financial research and decision-making, it's crucial to:\
- Retrieve **live financial data** from the web.\
- Parse **annual reports, filings, and PDFs**.\
- Consolidate information from multiple heterogeneous sources.

Traditional RAG (Retrieval-Augmented Generation) setups either:\
- Fail to combine **real-time search with document analysis**, or\
- Struggle with **multi-source consistency and consolidation**.

------------------------------------------------------------------------

## 🚀 Solution Overview

We built a **Pathway-powered Hybrid RAG Assistant** that:\
- Accepts **queries + uploaded financial documents** via a Streamlit
frontend.\
- Uses **Pathway multi-agent pipelines** to retrieve, reason, and
consolidate.\
- Combines:\
- **Gemini/OpenAI/Groq models** for reasoning\
- **Pathway retrievers** for uploaded files\
- **Tavily & Google Search tools** for live financial data\
- **Iterative Pathway consolidation** for conflict resolution

The result: **a unified, reliable, and interactive AI assistant** for
financial queries.

------------------------------------------------------------------------

## 🛠️ Features

-   **📂 Document Uploads** -- PDFs, TXTs, DOCX (live-indexed by
    Pathway).\
-   **🌍 Real-Time Web Search** -- Tavily + Google Finance via SerpAPI.\
-   **🔎 Sub-Question Decomposition** -- Pathway generates targeted
    sub-queries.\
-   **⚖️ Knowledge Consolidation** -- Pathway merges & clusters
    consistent info.\
-   **💬 Conversational Frontend** -- Query via Streamlit interface.\
-   **☁️ Public Access** -- Exposed with Cloudflare Tunnel (no ngrok).

------------------------------------------------------------------------

## 📂 System Architecture

1.  **User Input (Frontend)**
    -   Query entered in Streamlit.\
    -   File(s) uploaded for context.
2.  **Pathway Decider Agent**
    -   Routes simple queries directly.\
    -   Breaks down complex ones into sub-questions.
3.  **Multi-Agent Pathway Workflow**
    -   **Internal LLM Agent** (Gemini/OpenAI reasoning).\
    -   **Search Agent** (Tavily/Google for fresh data).\
    -   **Retriever Agent** (PDF embeddings via FAISS).\
    -   **Financial Agent** (abbreviation → full forms).
4.  **Pathway Consolidation Agent**
    -   Clusters consistent answers.\
    -   Separates conflicting info.\
    -   Produces unified response.
5.  **Final Agent**
    -   Formats and returns results to frontend.

------------------------------------------------------------------------

## ⚙️ Tech Stack

-   **Pathway** -- multi-agent orchestration & consolidation.\
-   **LangChain / LangGraph** -- tool integrations.\
-   **LLMs** -- Gemini 1.5 Pro, OpenAI GPT, Groq Mixtral.\
-   **Vector Search** -- FAISS + OpenAI embeddings.\
-   **Frontend** -- Streamlit.\
-   **Deployment** -- Cloudflare Tunnel (Colab → Public).

------------------------------------------------------------------------

## 🚀 Quickstart (Colab + Cloudflare)

1.  **Install requirements**\

``` bash
!pip install streamlit langchain langgraph langchain_community langchain_openai langchain_groq langchain_google_genai openai pypdf pdfplumber faiss-cpu google-search-results
```

2.  **Run Streamlit app**\

``` bash
!nohup streamlit run streamlit_app.py --server.port 8501 --server.headless true > streamlit.log 2>&1 &
```

3.  **Expose with Cloudflare Tunnel**\

``` bash
!wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
!dpkg -i cloudflared-linux-amd64.deb || true
!cloudflared tunnel --url http://localhost:8501 --no-autoupdate
```

4.  Copy the generated `https://xxxxx.trycloudflare.com` → open in
    browser.

------------------------------------------------------------------------

## 📌 Use Cases

-   Financial research (company filings, annual surveys).\
-   Fintech RAG demos with **Pathway + Gemini**.\
-   Multi-source fact consolidation.\
-   Academic/business reports using AI retrieval.

------------------------------------------------------------------------

## 🔮 Future Scope

-   Multi-user session handling via Pathway.\
-   Persistent document stores.\
-   Richer visualization of consolidated financial metrics.

------------------------------------------------------------------------

## 🤝 Contributions

-   Built as a **hackathon submission** leveraging **Pathway** for
    multi-agent orchestration.\
-   Contributions welcome: Fork, improve agents, extend retrievers.

------------------------------------------------------------------------

🔥 **In short:** This repo demonstrates how **Pathway enables a robust
hybrid RAG system** that merges **LLM reasoning, financial document
analysis, and live search**, all exposed via a clean Streamlit UI and
Cloudflare tunnel.
