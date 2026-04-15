# RAG for Research Papers

A **Retrieval-Augmented Generation (RAG)** application that lets you chat with research papers. Upload PDFs (arXiv papers, journal articles, theses), ask questions in natural language, and get accurate, citation-backed answers grounded in the source material — instead of hallucinated summaries.

Built end-to-end with a **100% free, zero-cost tech stack**. No paid APIs, no cloud bills, no credit card required.

---

## Features

- **Multi-PDF ingestion** — upload one paper or an entire reading list
- **Smart chunking** — preserves section structure, tables, and equations
- **Semantic search** with dense embeddings + reranking for high-precision retrieval
- **Citation-grounded answers** — every response cites the page and section it came from
- **Conversational memory** — ask follow-up questions in context
- **arXiv integration** (planned) — pull papers directly by ID or URL
- **Local-first** — your papers and queries never leave your machine unless you want them to

---

## Tech Stack (All Free)

### Language & Core
| Component | Choice | Why |
|---|---|---|
| Language | **Python 3.11+** | Standard for ML/RAG ecosystems |
| Orchestration | **LangChain** / **LlamaIndex** | Rich RAG abstractions, free & open-source |

### LLM (Generation)
| Component | Choice | Why |
|---|---|---|
| Primary LLM | **Groq API** (Llama 3.3 70B / Llama 3.1 8B) | Generous free tier, blazing-fast inference |
| Backup LLM | **Google Gemini 1.5 Flash** (free tier) | 1500 free requests/day |
| Local fallback | **Ollama** (Llama 3.2, Phi-3, Qwen 2.5) | Runs offline on your laptop, $0 forever |

### Embeddings (Retrieval)
| Component | Choice | Why |
|---|---|---|
| Embedding model | **BAAI/bge-small-en-v1.5** (HuggingFace) | SOTA-quality, runs locally, free |
| Alternative | **sentence-transformers/all-MiniLM-L6-v2** | Lightweight, fast on CPU |

### Vector Database
| Component | Choice | Why |
|---|---|---|
| Primary | **ChromaDB** | Open-source, runs locally, persistent storage |
| Alternative | **Qdrant** (self-hosted via Docker) | Production-grade, free forever when self-hosted |
| Lightweight | **FAISS** | In-memory, blazing fast for prototyping |

### Document Processing
| Component | Choice | Why |
|---|---|---|
| PDF parsing | **PyMuPDF (fitz)** | Fast, accurate, handles complex layouts |
| Layout-aware | **unstructured** | Handles tables, figures, multi-column |
| Chunking | **LangChain RecursiveCharacterTextSplitter** | Section/semantic-aware splitting |

### Reranking (Precision Boost)
| Component | Choice | Why |
|---|---|---|
| Reranker | **BAAI/bge-reranker-base** (HuggingFace) | Local, free, dramatically improves retrieval quality |

### Frontend / UI
| Component | Choice | Why |
|---|---|---|
| UI Framework | **Streamlit** | Pure-Python, fast to build, beautiful out of the box |
| Alternative | **Gradio** | Great for ML demos, free hosting on HF Spaces |

### Backend (Optional API Layer)
| Component | Choice | Why |
|---|---|---|
| API Server | **FastAPI** | Modern, fast, free |

### Hosting & Deployment (All Free)
| Component | Choice | Why |
|---|---|---|
| App hosting | **Streamlit Community Cloud** | Free public deploy for Streamlit apps |
| Alternative | **HuggingFace Spaces** | Free GPU-backed hosting |
| Backend | **Render** / **Railway** free tier | Free server hosting |
| Code | **GitHub** | Free version control & CI |

### Dev Tools
- **uv** or **pip** — dependency management
- **python-dotenv** — env var management
- **Ruff** — linting & formatting
- **pytest** — testing

---

## Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│  PDF Upload  │────▶│  PyMuPDF /   │────▶│  Text Chunks     │
│  (Streamlit) │     │  unstructured│     │  (~500 tokens)   │
└──────────────┘     └──────────────┘     └────────┬─────────┘
                                                   │
                                                   ▼
                                          ┌──────────────────┐
                                          │  BGE Embeddings  │
                                          │  (local, free)   │
                                          └────────┬─────────┘
                                                   ▼
                                          ┌──────────────────┐
                                          │   ChromaDB       │
                                          │   (vector store) │
                                          └────────┬─────────┘
                                                   │
┌──────────────┐     ┌──────────────┐              │
│  User Query  │────▶│  Embed Query │──────────────┤
└──────────────┘     └──────────────┘              │
                                                   ▼
                                          ┌──────────────────┐
                                          │  Top-K Retrieval │
                                          └────────┬─────────┘
                                                   ▼
                                          ┌──────────────────┐
                                          │  BGE Reranker    │
                                          └────────┬─────────┘
                                                   ▼
                                          ┌──────────────────┐
                                          │  Groq LLM        │
                                          │  (Llama 3.3 70B) │
                                          └────────┬─────────┘
                                                   ▼
                                          ┌──────────────────┐
                                          │  Answer + Cites  │
                                          └──────────────────┘
```

---

## Getting Started

### Prerequisites
- Python 3.11+
- A free **Groq API key** → https://console.groq.com (no credit card)
- (Optional) **Ollama** installed for fully offline mode → https://ollama.com

### Installation

```bash
git clone https://github.com/Manasa-vamsi/RAG-for-Research-Papers.git
cd RAG-for-Research-Papers

python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate

pip install -r requirements.txt
```

### Configuration

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_free_groq_key_here
EMBEDDING_MODEL=BAAI/bge-small-en-v1.5
LLM_MODEL=llama-3.3-70b-versatile
VECTOR_DB_PATH=./chroma_db
```

### Run

```bash
streamlit run app.py
```

Open http://localhost:8501, upload a paper, and start asking questions.

---

## Project Structure (Planned)

```
RAG-for-Research-Papers/
├── app.py                      # Streamlit entry point
├── src/
│   ├── ingest/                 # PDF parsing & chunking
│   ├── embed/                  # Embedding model wrappers
│   ├── store/                  # Vector DB interface
│   ├── retrieve/               # Retrieval + reranking logic
│   ├── generate/               # LLM call & prompt templates
│   └── chat/                   # Conversational memory
├── data/                       # Uploaded PDFs (gitignored)
├── chroma_db/                  # Vector store (gitignored)
├── tests/
├── requirements.txt
├── .env.example
└── README.md
```

---

## Roadmap

- [x] Project scaffolding & README
- [ ] PDF ingestion pipeline
- [ ] Embedding + ChromaDB integration
- [ ] Basic retrieval + Groq LLM answering
- [ ] BGE reranker integration
- [ ] Streamlit UI with citation rendering
- [ ] Conversational memory
- [ ] arXiv ID / URL ingestion
- [ ] Multi-paper comparison mode
- [ ] Deploy to Streamlit Community Cloud

---

## Cost Breakdown

| Item | Cost |
|---|---|
| Groq API (free tier) | ₹0 |
| HuggingFace embeddings (local) | ₹0 |
| ChromaDB (local) | ₹0 |
| Streamlit Cloud hosting | ₹0 |
| GitHub | ₹0 |
| **Total** | **₹0** |

---

## License

MIT
