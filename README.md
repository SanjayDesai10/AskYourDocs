# ⚡ NotebookLM CLI — Local RAG Agent

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.12+-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/LangChain-⚡-1C3C3A?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Hugging%20Face-🤗-FFD21E?style=for-the-badge" />
  <img src="https://img.shields.io/badge/ChromaDB-VectorStore-3b82f6?style=for-the-badge" />
</p>

A command-line tool modeled after **Google NotebookLM**. Ingest files or directories locally and have an interactive, context-grounded conversation with them.

---

## 🚀 Key Features

* **Grounded QA:** Answers are constructed strictly using information retrieved from your documents, avoiding LLM hallucinations.
* **Local Document Parsing:** Local extraction of PDFs and plaintext files using the `Unstructured` library.
* **Persistent Vector Memory:** Embeds text chunks using `all-mpnet-base-v2` and persists them locally inside a Chroma DB directory.
* **Dynamic Context Middleware:** Automatically fetches the top 4 most relevant text segments and injects them into the prompt history before generating answers.

---

## 🛠️ Architecture

```
                  ┌───────────────────────────────┐
                  │    Ingest: PDF / Text Files   │
                  └───────────────┬───────────────┘
                                  ▼
                  ┌───────────────────────────────┐
                  │   Recursive Chunking (1000)   │
                  └───────────────┬───────────────┘
                                  ▼
                  ┌───────────────────────────────┐
                  │  Vector Store: Local Chroma   │
                  └───────────────┬───────────────┘
                                  ▼
     ┌─────────────┐      ┌───────────────┐      ┌─────────────┐
     │ User Query  ├─────▶│  Similarity   ├─────▶│ LLM Response│
     └─────────────┘      │  Search (K=4) │      └─────────────┘
                          └───────────────┘
```

---

## ⚙️ Setup Instructions

### 1. Prerequisites
Ensure you have **Python 3.12+** and **[uv](https://docs.astral.sh/uv/)** installed on your machine.

### 2. Install Dependencies
Run the sync command to automatically set up your virtual environment and install all packages (including `unstructured`, `pdfminer`, and local embedding tools):
```bash
uv sync
```

### 3. Add Environment Key
Create a `.env` file in the root of the project:
```env
HUGGINGFACEHUB_API_TOKEN=hf_your_token_here
```
> 💡 *Note: You do not need any Unstructured API key. Parsing is completed entirely locally on your device.*

---

## 💻 How to Use

Simply run the script and point it to a file or a folder of documents:

```bash
# Chat with the sample text document
uv run main.py hello.txt

# Chat with the sample PDF document
uv run main.py data/French_Revolution.pdf

# Chat with an entire directory of documents
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