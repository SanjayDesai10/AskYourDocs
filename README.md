# AskYourDocs — CLI-Based RAG

A command-line RAG (Retrieval-Augmented Generation) tool that lets you ingest files or directories and ask questions about their content interactively.

## How It Works

```
┌──────────────┐
│  Load Docs   │
│ (files/dir)  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  Split into  │
│    Chunks    │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Embed &    │
│   Store in   │
│   ChromaDB   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  Query via   │
│   dynamic    │
│   prompt     │
└──────────────┘
```



1. **Ingestion** — Documents are loaded via `UnstructuredLoader` (supports PDF, TXT, and more), split into 1000-character chunks with 200-character overlap, and stored in a local ChromaDB vector store.
2. **Querying** — User queries are matched against stored chunks via similarity search. The top 4 results are injected as context into a system prompt using LangChain's `@dynamic_prompt` middleware, and the LLM generates an answer in a single pass.

## Tech Stack

- LLM: `google/gemma-4-26B-A4B-it` via HuggingFace
- Embeddings: `sentence-transformers/all-mpnet-base-v2`
- Vector Store: ChromaDB (persisted locally)
- Doc Loader: `UnstructuredLoader` (PDF, TXT, etc.)
- CLI Framework: Click
- Orchestration: LangChain `create_agent` + `dynamic_prompt`

## Setup

### Prerequisites

- Python >= 3.12
- uv (recommended) or pip

### Installation

```bash
# Clone the repo
git clone https://github.com/SanjayDesai10/AskYourDocs.git
cd AskYourDocs

# Install dependencies
uv sync
```

### Environment Variables

Create a `.env` file in the project root:

```env
HUGGINGFACEHUB_API_TOKEN=hf_your_token_here
```


## Usage

```bash
# Ingest the sample text file and start querying
uv run main.py hello.txt

# Ingest the sample PDF file
uv run main.py data/French_Revolution.pdf

# Ingest an entire directory of documents
uv run main.py ./data
```

### Example Terminal Session

```
$ uv run main.py data/French_Revolution.pdf
Setting up models and vector store...

Ingesting from: data/French_Revolution.pdf
Processing file: French_Revolution.pdf
Loaded 5 document(s)
Split documents into 5 sub-documents.
Stored 5 chunks in vector store

Ready! Ask questions about your documents (type 'exit' to quit)

You > when was french revolution started 
================================== Ai Message ==================================
The French Revolution began on July 14, 1789, with the storming of the Bastille.

You > exit
Goodbye!
```