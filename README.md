# Agentic RAG Assistant

[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://agentic-rag-assistant-fdy8im2emrsdgw7ttelwyu.streamlit.app/)

> 🚀 **Live Deployment:** **[https://agentic-rag-assistant-fdy8im2emrsdgw7ttelwyu.streamlit.app/](https://agentic-rag-assistant-fdy8im2emrsdgw7ttelwyu.streamlit.app/)**
> 
> Experience the live interactive Agentic RAG Assistant in your browser!

A multi-agent RAG (Retrieval-Augmented Generation) system that lets you upload documents and ask questions about them. It uses a three-agent pipeline — one for retrieval, one for answering, and one that verifies the answer isn't hallucinated.

Built with FastAPI, Streamlit, Google Gemini, and ChromaDB.

## What it does

- Upload PDFs, DOCX, TXT, or CSV files through a simple web interface
- Ask natural language questions about your documents
- Get detailed answers grounded in the actual content you uploaded
- A verification agent checks whether the answer is actually supported by the source material or if the LLM is making stuff up

The verification step is what makes this different from a basic RAG setup — it adds a layer of trust by flagging answers that might contain hallucinations.

## How the pipeline works

```
User Query
    │
    ▼
Retrieval Agent ──→ searches ChromaDB for relevant chunks
    │
    ▼
Reasoning Agent ──→ generates a detailed answer using Gemini
    │
    ▼
Verification Agent ──→ cross-checks the answer against source docs
    │
    ▼
Final Response (answer + verification status + sources)
```

The backend runs on FastAPI (port 8000) and the frontend is a Streamlit app (port 8501). They communicate over REST.

## Setup

**Prerequisites:** Python 3.12+ and a [Google Gemini API key](https://aistudio.google.com/apikey)

```bash
# clone and cd into the project
git clone https://github.com/Nishant052004/data-analyst-assessment.git
cd data-analyst-assessment

# set up virtual environment
python -m venv venv
venv\Scripts\activate        # on Windows
# source venv/bin/activate   # on Mac/Linux

# install dependencies
pip install -r requirements.txt

# set up your env file
cp .env.example .env
# open .env and paste your GEMINI_API_KEY
```

## Running

The easiest way is to use the launcher script — it starts both the backend and frontend:

```bash
python run.py
```

Then open http://127.0.0.1:8501 in your browser.

You can also run them separately if you prefer:

```bash
# backend
uvicorn app.main:app --host 127.0.0.1 --port 8000

# frontend (in another terminal)
streamlit run streamlit_app.py --server.port 8501
```

There's also a Dockerfile if you want to containerize it:

```bash
docker build -t agentic-rag .
docker run -p 8000:8000 --env-file .env agentic-rag
```

## Project structure

```
├── app/
│   ├── main.py              # FastAPI app
│   ├── api.py               # API routes (/chat, /upload)
│   ├── agent_pipeline.py    # the three-agent pipeline logic
│   ├── config.py            # settings (pydantic-settings)
│   ├── embeddings.py        # sentence-transformer setup
│   ├── ingestion.py         # document chunking and indexing
│   ├── vectorstore.py       # ChromaDB operations
│   └── utils.py             # file parsing helpers
├── streamlit_app.py          # frontend
├── run.py                    # launches both servers
├── requirements.txt
├── Dockerfile
└── .env.example
```

## Configuration

Everything is configured through environment variables. See `.env.example` for the full list, but the main ones are:

- `GEMINI_API_KEY` — your Google Gemini API key (required)
- `LLM_MODEL` — which Gemini model to use (defaults to `gemini-1.5-flash`)
- `EMBEDDING_MODEL` — sentence-transformer model (defaults to `all-MiniLM-L6-v2`)
- `CHUNK_SIZE` / `CHUNK_OVERLAP` — controls how documents get split up (defaults: 500 / 50)

You can also pass your API key directly through the sidebar in the UI if you don't want to use an env file.

## Tech stack

- **FastAPI** + Uvicorn for the backend API
- **Streamlit** for the frontend
- **Google Gemini** as the LLM (via OpenAI-compatible endpoint)
- **Sentence Transformers** for document embeddings
- **ChromaDB** for vector storage
- **LangChain** for orchestration
- **Pydantic Settings** for config management

## Contributing

If you want to contribute, feel free to fork the repo and open a PR. Any improvements to the agent pipeline or new document format support would be especially welcome.

## License

MIT
