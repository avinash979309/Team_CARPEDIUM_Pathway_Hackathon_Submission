                        
                                       Multi-Agent RAG using Pathway — Financial Extraction Expert

A detailed README for the Multiagented RAG project — a multi-agent Retrieval-Augmented Generation (RAG) pipeline designed as an expert system for financial information extraction.
This file explains architecture, components, setup, usage, security considerations, testing and deployment notes, and includes flow diagrams (Mermaid + ASCII fallback) and example queries.

1. Project summary

This project implements a multi-agent system that combines web/search tools, document downloaders/parsers (PDFs, text), Pathway vector stores/embeddings, and several specialized agents to extract and consolidate financial information. It is built with LangChain/the community extensions and includes:

a search agent that chooses between finance-aware search tools and general web tools,

document ingestion tools (PDF/text loaders, downloader),

specialized financial helper tools (abbreviation expansion, Yahoo finance news extractor, Tavily helper),

vector stores & embeddings for retrieval,

an iterative consolidation / astute agent that synthesizes multi-source data into a single reliable answer.

Intended use: answer financial questions, extract figures/metrics from reports, compile multi-source summaries, and produce structured deliverables using documents and web sources.

2. High-level architecture & flow
2.1 Narrative flow

User query is submitted (financial or general).

Orchestrator (react-style search agent) decides which tool(s) to call:

Finance-specific queries → use Google Finance / YahooFinanceNews / google_search tool.

General or document search → use Tavily search tool (or web downloader).

Tools fetch content:

online search results, PDFs, HTML pages, or third-party tool results.

the downloader tool can save PDFs locally.

Document loaders & splitters (PDF/Text loaders + text splitters) create chunks.

Embeddings & Vector Store (OpenAI / HuggingFace embeddings + FAISS/InMemory) store chunks and allow similarity search.

Iterative consolidation agent uses retrieved chunks to produce sub-answers.

Astute Knowledge Consolidator (final agent) synthesizes the sub-answers into the final deliverable output.

Output returned to user (direct answer, structured summary, or instruction to call sub-tools if needed).

2.2 Flowchart (Mermaid)
flowchart TD
  U[User Query] --> O[Orchestrator / Search Agent]
  O -->|Finance Query| FTOOLS[Finance Tools<br/>(GoogleFinance, Yahoo)]
  O -->|General Query| GTOOLS[Tavily / Web Search]
  GTOOLS --> D[Downloader Tool / PDF Fetcher]
  D --> L[Document Loaders (PDF/Text)]
  L --> S[Text Splitter (chunks)]
  S --> V[Embeddings + VectorStore (FAISS / InMemory)]
  FTOOLS --> V
  V --> R[Retriever (similarity_search)]
  R --> C[Iterative Consolidation Agent]
  C --> A[Astute Knowledge Consolidator]
  A --> OUT[Final Structured Answer]
  style U fill:#f9f,stroke:#333,stroke-width:1px
  style OUT fill:#bff,stroke:#333

2.3 ASCII fallback diagram
User -> Orchestrator Agent
    -> (finance) -> Finance Tools -> Vector Store -> Consolidator -> Final Answer
    -> (general)  -> Tavily/Downloader -> Loaders -> Splitter ->Pathway Vector Store -> Consolidator -> Final Answer

3. Code components (detailed explanation)

Below is a breakdown of major code components found in the notebook and what each does.

3.1 Environment & dependencies

Uses langchain, langchain_community, langchain_openai, langchain_core, langchain_text_splitters, langchain_community.document_loaders and tooling for agents, tools, and vector stores.

Embedding models used: OpenAIEmbeddings and HuggingFaceEmbeddings (“sentence-transformers/all-MiniLM-L6-v2”).

Vectorstores used: FAISS and InMemoryVectorStore (custom/in-memory).

Utilities: pdfplumber, PyPDFLoader, googlesearch (for scraper), requests, beautifulsoup4 (if present), SERPAPI wrappers (if configured).

Notebook installs multiple packages in early cells (pip install lines). See Installation for exact commands.

3.2 Tools (what to expect)

PDFTextExtractorTool: Loads PDFs, splits text into chunks and performs similarity search. Useful for extracting sections/figures from financial reports.

downloader_tool (tool): Uses googlesearch to find filetype:pdf results, downloads PDFs to a folder (e.g., /content/sample_data/pdfs).

TavilySearchResults: A language-model search wrapper (not standard LangChain); configured for general knowledge retrieval (used for non-finance queries).

YahooFinanceNewsTool: A finance-specific news extractor for time-sensitive financial news.

FinancialAbbreviationTool: A helper to expand/explain financial abbreviations and terms (e.g., EBITDA, EPS).

NoOpTool: a placeholder used in some agent setups to constrain agents.

3.3 Agents and prompts

create_react_agent(...): Used multiple times to create tool-using reactive agents:

search_agent (uses tavily and google_search)

online_documents_agent (for fetching web documents)

internal_agent (uses internal knowledge only; no tools)

astute_agent (final consolidation agent)

Prompts:

make_searcher_prompt(suffix): instructs the search agent to choose the correct tool based on query type.

make_astute_prompt(suffix, information_from_multiple_sources): instructs final consolidator to rely on provided sources only and to output in a strict format (e.g., DIRECT RESPONSE: or call generate_subquestions for complex queries).

make_internal_prompt(...), make_internal_agent, make_searcher_prompt — specialized behavior controls.

3.4 Retrieval & consolidation pipeline

Downloader → Loader → Splitter → VectorStore chain converts large documents into searchable chunks.

Iterative consolidation: retrieved chunks are processed and passed through a chain where sub-answers are generated and consolidated iteratively (to reduce hallucination and merge multi-source signals).

Final stage: astute_agent composes the final answer using only the consolidated information (explicit instruction in prompt).

3.5 Execution flow (notebook cell end)

The notebook defines functions to run the full flow and then prompts user_query = input("Enter query please:") and if user_query: run_function() — a simple REPL-style entry.

4. Installation & setup

Important: This notebook contains placeholders for API keys. Do not copy any secret values from the notebook into a public repo. Use environment variables.

4.1 Recommended Python version

Python 3.10+ recommended.

4.2 Create virtual environment
python -m venv .venv
source .venv/bin/activate    # macOS / Linux
# or
.venv\Scripts\activate       # Windows PowerShell
pip install --upgrade pip

4.3 Install required packages (derived from the notebook)
pip install langchain langchain_community langchain_openai langchain_core \
            langchain_text_splitters langchain-embeddings langchain_anthropic \
            pdfplumber PyPDF2 googlesearch-python requests beautifulsoup4 \
            sentence-transformers faiss-cpu


If a specific package name in the notebook differs, adapt accordingly. The notebook uses multiple community packages; if you see import errors, check exact package names and versions.

4.4 Environment variables (set these before running)

Set the following environment variables — replace YOUR_KEY placeholders with your own keys:

export OPENAI_API_KEY="YOUR_OPENAI_KEY"
export SERPAPI_API_KEY="YOUR_SERPAPI_KEY"
export TAVILY_API_KEY="YOUR_TAVILY_KEY"         # if you use Tavily
# Optional:
export LANGCHAIN_TRACING_V2="true|false"


Notebook includes direct environment assignment at times — replace those with secure env var usage.

4.5 Folder structure (recommended)
project-root/
│
├─ pathway_hackathon.ipynb
├─ README.md
│--Tools.ipynb
│--Requirements and Installations.ipynb
|--Prompts_For_Agents.ipynb



Consider refactoring the notebook into src/ Python modules for maintainability.

5. How to run (examples)
5.1 In notebook (quick)

Open pathway_hackathon.ipynb in Jupyter/Colab.


Run all cells.

Provide a query in the input cell prompt at the end (e.g., Enter query please:).

5.2 From script (recommended)

Refactor the main flow into src/main.py with a run(query) function. Example pseudo-command:

python src/main.py --query "Nykaa Q4 2024 revenue and EPS"


Outputs: structured consolidated result printed to console or saved as JSON.

6. Example queries & expected responses
Examples

Single figure extraction:
"What was Nykaa's revenue in Q2 2024?"
Expected: precise numeric value with source citations and context (quarter, year, currency).

Multi-source reconciliation:
"Summarize the last 3 analyst notes on Tata Motors and list recent news events that could impact Q4 guidance."
Expected: consolidated list of events, analyst quotes, and citations.

Document-based extraction:
"Extract 'Total revenue' and 'Net income' from the PDF 'company_annual_report_2024.pdf'."
Expected: values for those labels and the PDF page numbers or chunk IDs used.

7. Prompts & behaviour rules (important)

The notebook uses structured prompts to control agent behavior. Key guidelines:

Finance queries must be routed to finance tools (Google/Yahoo). Non-finance queries go to Tavily or generic search.

Astute agent: must use only provided consolidated info; no external speculation.

Generation format: The final agent often expects a prefix like DIRECT RESPONSE: for simple answers.

Complex queries: Agent can call generate_subquestions tool to break questions into smaller items. When used, the pipeline halts to let subqueries run.

Keep prompts in a dedicated module so they can be tuned without changing code.

8. Storage & retrieval details
8.1 Text splitting

Uses RecursiveCharacterTextSplitter / CharacterTextSplitter with chunk sizes (e.g., 512/1024) and overlap to preserve context across chunks.

8.2 Embeddings / Vector Store

Embeddings: OpenAIEmbeddings OR HuggingFace MiniLM. Choose OpenAI for best accuracy (token & cost tradeoff), MiniLM for local/no-cost embeddings.

Vector stores: FAISS recommended for production; InMemory useful for quick tests.

8.3 Retrieval

similarity_search(query) returns top chunks; those get fed to the consolidation agent.

Add metadata (source filename, page number, chunk index) to each document to enable precise citations.

9. Security & privacy

Do not hardcode API keys. Notebook sometimes shows example keys — replace these with secure env variables.

PDFs may contain PII/sensitive financial data — handle with care.

Rate limits and cost: Using OpenAI embeddings & LLMs incurs cost. Monitor usage.

Web scraping legality: Confirm you have right to download and store PDFs from the web (site terms).

10. Testing & validation

Unit tests:

test downloader returns expected number of PDFs for given query (mock external requests).

test PDF loader extracts text and the split yields expected chunk count.

test vectorstore add & similarity_search return known top result for a given sample query.

test final agent responds with DIRECT RESPONSE: when appropriate by mocking LLM outputs.

Integration tests:

end-to-end run with a small, known PDF corpus and a sample query; assert final answer contains expected numbers and sources.

11. Limitations & known issues

Hallucination risk if prompts are not constraining the agent; the project uses consolidation to reduce hallucination but it is not perfect.

Dependency fragility: uses many community packages that may change API; pin versions.

Real-time finance: Google Finance / Yahoo tools rely on external APIs/HTML structure — they can break if providers change pages.

Tool selection errors: the search agent's heuristics may misclassify a query; test and tune make_searcher_prompt.

12. Suggestions for improvement (next steps)

Move prompts and tools into separate modules for better management and testing.

Add robust metadata tracking (source URL, timestamp, page number) for stronger traceability.

Introduce rate-limiting and caching for web searches.

Add an evaluation harness to compare extracted numbers against ground truth.

Implement a small web UI to submit queries and display results with source citations and highlights.

Add user authentication & logging for audit trails (if used in production).

13. Example README snippet (short) — copy into repo root

This is a condensed copy you can paste into README.md quickly:

# Multi-Agent RAG: Financial Extraction Expert
A multi-agent retrieval-augmented generation system specializing in financial extraction. Built on LangChain and community tools. It fetches PDFs/web content, vectorizes, retrieves, and consolidates multi-source financial answers.

## Quickstart
1. Create virtual environment and install dependencies.
2. Set environment variables: `OPENAI_API_KEY`, `SERPAPI_API_KEY`, `TAVILY_API_KEY`.
3. Run the notebook or refactored `src/main.py`.
4. Enter a query (e.g., "Nykaa Q4 2024 revenue").

## Architecture
(See full README for flowchart, components, tests, and configuration)

14. Troubleshooting FAQ

Q: I get authentication or API Key errors.
A: Confirm env vars are set in the shell session running Jupyter and restart the kernel. Do not use hardcoded keys.

Q: Downloads fail using googlesearch.
A: googlesearch sometimes returns partial results. Use SERPAPI if you have the key set, or increase num_results. Also check network access and robots.txt.

Q: PDF loader returns empty text for some PDFs.
A: Try pdfplumber or PyPDF2 alternatives, or use OCR for scanned PDFs (Tesseract).

Q: VectorStore similarity_search returns irrelevant chunks.
A: Re-tune chunk_size and chunk_overlap, or use a better embedding model.
