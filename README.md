# 📚 Retrieval-Augmented Generation (RAG) Pipeline

A complete **Retrieval-Augmented Generation (RAG)** pipeline that processes PDF documents, converts their content into vector embeddings, stores them in a persistent ChromaDB vector store, retrieves the most relevant document chunks for a user query, and uses an LLM to generate an answer based on the retrieved context.

The project demonstrates the complete RAG workflow from **document ingestion to LLM-generated responses** using Python, LangChain, Sentence Transformers, ChromaDB, OpenAI, and Groq.

---

## 🚀 Project Overview

Traditional LLMs generate answers based on the information available in their training data. A RAG system improves this process by retrieving relevant information from an external knowledge base before generating an answer.

This project implements the following pipeline:

```text
PDF Documents
      ↓
Document Loading
      ↓
Text Chunking
      ↓
Text Embeddings
      ↓
ChromaDB Vector Store
      ↓
Semantic Retrieval
      ↓
Relevant Context
      ↓
LLM
      ↓
Generated Answer
```

The system can therefore answer questions using information retrieved from the provided PDF documents.

---

# ✨ Features

* Load multiple PDF documents from a directory
* Convert PDF pages into LangChain `Document` objects
* Split documents into smaller chunks
* Generate semantic embeddings using Sentence Transformers
* Store embeddings in a persistent ChromaDB vector database
* Perform semantic similarity search
* Retrieve the top relevant document chunks
* Apply a similarity score threshold
* Generate context-aware answers using an LLM
* Support multiple LLM providers:

  * OpenAI
  * Groq
* Inspect and test individual stages of the RAG pipeline

---

# 🧠 How RAG Works in This Project

## 1. Document Ingestion

PDF files are placed inside:

```text
data/pdfs/
```

The project uses LangChain's `PyPDFLoader` to load the PDF files.

Each PDF page is converted into a LangChain `Document` containing:

* Page content
* Metadata
* Source information

Multiple PDFs can be loaded automatically from the `data/pdfs` directory.

---

## 2. Document Chunking

Large documents are divided into smaller pieces using:

```text
RecursiveCharacterTextSplitter
```

The current implementation uses:

```text
Chunk size:     500 characters
Chunk overlap:   50 characters
```

The overlap helps preserve contextual information between neighboring chunks.

```text
Original Document
        ↓
 ┌───────────────┐
 │   Chunk 1     │
 └───────────────┘
        ↓
 ┌───────────────┐
 │   Chunk 2     │
 └───────────────┘
        ↓
 ┌───────────────┐
 │   Chunk 3     │
 └───────────────┘
```

---

## 3. Embedding Generation

Each document chunk is converted into a numerical vector using the Sentence Transformers model:

```text
all-MiniLM-L6-v2
```

The `EmbeddingManager` class is responsible for loading the model and generating embeddings.

Conceptually:

```text
Text Chunk
    ↓
Sentence Transformer
    ↓
Embedding Vector
```

These vectors represent the semantic meaning of the text and allow the system to search for documents based on meaning rather than exact keyword matches.

---

# 🗄️ 4. Vector Store

The generated embeddings are stored using:

```text
ChromaDB
```

The project uses a persistent ChromaDB client with the following configuration:

```text
Persistence Directory:
data/vector_store

Collection:
pdf_documents
```

The vector store contains:

* Document IDs
* Document text
* Embeddings
* Metadata

A unique ID is generated for each stored document chunk.

---

# 🔎 5. Retrieval Pipeline

When a user asks a question, the query goes through the same embedding model used for the documents.

```text
User Query
    ↓
Sentence Transformer
    ↓
Query Embedding
    ↓
ChromaDB Similarity Search
    ↓
Top-K Relevant Chunks
```

The project retrieves the most relevant chunks using semantic similarity.

The retriever supports:

```python
top_k=5
```

and a configurable:

```python
score_threshold
```

The retrieved results contain information such as:

* Document ID
* Document content
* Metadata
* Distance
* Similarity score
* Retrieval rank

---

# 🤖 6. LLM Integration

After retrieving relevant document chunks, the retrieved text is combined into a context that is provided to an LLM.

The basic flow is:

```text
User Question
      ↓
Retrieve Relevant Chunks
      ↓
Combine Retrieved Context
      ↓
Context + Question
      ↓
LLM
      ↓
Final Answer
```

The project demonstrates integration with multiple LLM providers.

---

## OpenAI

The notebook demonstrates integration with OpenAI through LangChain:

```text
langchain-openai
```

The configured model in the notebook is:

```text
gpt-5.4
```

The LLM is configured with a low temperature to make the generated responses more focused.

---

## Groq

The project also demonstrates integration with Groq using:

```text
langchain-groq
```

The configured model is:

```text
qwen/qwen3-32b
```

This allows the same retrieval pipeline to be used with a different LLM provider.

---

# 🔄 Complete RAG Pipeline

The complete implementation can be summarized as:

```text
                    OFFLINE / INGESTION
                    -------------------

              PDF Documents
                    │
                    ▼
             PyPDFLoader
                    │
                    ▼
             LangChain Docs
                    │
                    ▼
       RecursiveCharacterTextSplitter
                    │
                    ▼
              Text Chunks
                    │
                    ▼
        all-MiniLM-L6-v2
                    │
                    ▼
             Embeddings
                    │
                    ▼
                ChromaDB
                    │
                    ▼
           Persistent Vector Store


                    QUERY TIME
                    ----------

              User Question
                    │
                    ▼
        all-MiniLM-L6-v2
                    │
                    ▼
            Query Embedding
                    │
                    ▼
          ChromaDB Retrieval
                    │
                    ▼
          Top-K Relevant Chunks
                    │
                    ▼
          Retrieved Context
                    │
                    ▼
                  LLM
          ┌─────────┴─────────┐
          ▼                   ▼
       OpenAI                Groq
          │                   │
          └─────────┬─────────┘
                    ▼
              Final Answer
```

---

# 🛠️ Tech Stack

| Category                | Technology                     |
| ----------------------- | ------------------------------ |
| Language                | Python                         |
| RAG Framework           | LangChain                      |
| Document Processing     | PyPDFLoader                    |
| Text Splitting          | RecursiveCharacterTextSplitter |
| Embeddings              | Sentence Transformers          |
| Embedding Model         | `all-MiniLM-L6-v2`             |
| Vector Database         | ChromaDB                       |
| LLM Provider            | OpenAI                         |
| LLM Provider            | Groq                           |
| OpenAI Integration      | `langchain-openai`             |
| Groq Integration        | `langchain-groq`               |
| Development Environment | Jupyter Notebook               |

---

# 📁 Project Structure

Based on the notebooks, the expected project structure is:

```text
RAG_Project/
│
├── data/
│   ├── pdfs/
│   │   ├── document1.pdf
│   │   ├── document2.pdf
│   │   └── ...
│   │
│   └── vector_store/
│       └── ChromaDB persistent storage
│
├── RAG_pipeline.ipynb
├── 1-RAG_pipeline.ipynb
│
└── README.md
```

### Files

**`RAG_pipeline.ipynb`**

Contains the core RAG implementation:

* Document loading
* PDF ingestion
* Chunking
* Embedding generation
* ChromaDB vector storage
* Semantic retrieval

**`1-RAG_pipeline.ipynb`**

Extends the core pipeline with:

* Retrieval
* OpenAI integration
* Groq integration
* Context-based answer generation

---

# 📦 Installation

Install the required libraries:

```bash
pip install langchain
pip install langchain-core
pip install langchain-community
pip install langchain-text-splitters
pip install pypdf
pip install pymupdf
pip install sentence-transformers
pip install chromadb
```

For OpenAI integration:

```bash
pip install langchain-openai
```

For Groq integration:

```bash
pip install langchain-groq
```

---

# ▶️ Running the Project

## 1. Clone the Repository

```bash
git clone <your-repository-url>
cd <your-repository-name>
```

## 2. Install Dependencies

```bash
pip install -r requirements.txt
```

## 3. Add PDF Documents

Place your PDF files inside:

```text
data/pdfs/
```

For example:

```text
data/
└── pdfs/
    ├── research.pdf
    ├── machine_learning.pdf
    └── transformers.pdf
```

## 4. Open the Notebook

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Then open:

```text
RAG_pipeline.ipynb
```

or:

```text
1-RAG_pipeline.ipynb
```

Run the cells sequentially.

---

# 🔑 API Keys

The second notebook demonstrates LLM integration using API keys.

For OpenAI:

```python
API_KEY_OPENAI = "paste-your-api-key-here"
```

For Groq:

```python
API_Key_GROQ = "paste-your-api-key-here"
```

### ⚠️ Security

Do **not** commit real API keys to GitHub.

For a production project, use environment variables or a `.env` file instead.

Example:

```python
import os

API_KEY_OPENAI = os.getenv("OPENAI_API_KEY")
API_Key_GROQ = os.getenv("GROQ_API_KEY")
```

Add `.env` to `.gitignore`:

```text
.env
```

---

# 🧪 Example Query

After the documents have been processed and stored in ChromaDB, a query can be passed to the retriever:

```python
rag_retriever.retrieve(
    "What is encoder decoder?"
)
```

The retriever returns the most relevant document chunks.

The retrieved context is then passed to the LLM:

```python
answer = generate_output(
    "what is encoder-decoder?",
    rag_retriever,
    llm
)
```

The LLM generates an answer using the retrieved document context.

---

# 📊 RAG Components

| Component                        | Purpose                      |
| -------------------------------- | ---------------------------- |
| `PyPDFLoader`                    | Loads PDF documents          |
| `RecursiveCharacterTextSplitter` | Splits documents into chunks |
| `SentenceTransformer`            | Generates embeddings         |
| `EmbeddingManager`               | Manages embedding generation |
| `ChromaDB`                       | Stores document embeddings   |
| `VectorStoreManager`             | Manages the vector database  |
| `RAGRetriever`                   | Performs semantic retrieval  |
| OpenAI / Groq LLM                | Generates the final response |

---

# 💡 Key Concepts Demonstrated

This project provides hands-on implementation of several important concepts in modern NLP and Generative AI:

* Retrieval-Augmented Generation (RAG)
* Document ingestion
* Text chunking
* Semantic embeddings
* Vector databases
* Similarity search
* Semantic retrieval
* Context augmentation
* LLM integration
* LangChain
* Sentence Transformers
* ChromaDB
* OpenAI API
* Groq API

---

# 🔮 Future Improvements

Possible improvements to the current implementation include:

* [ ] Add a web interface for querying documents
* [ ] Add support for TXT, DOCX, and other document formats
* [ ] Improve chunking strategies
* [ ] Add metadata-based filtering
* [ ] Add configurable `top_k` retrieval
* [ ] Add reranking of retrieved documents
* [ ] Improve prompt templates
* [ ] Add source citations to generated answers
* [ ] Add evaluation metrics for retrieval quality
* [ ] Add RAG evaluation using relevant benchmark datasets
* [ ] Move API keys to environment variables
* [ ] Add a requirements file
* [ ] Deploy the RAG application

---

# 🎯 Learning Objective

The main objective of this project is to understand how a **Retrieval-Augmented Generation system is built from scratch**, rather than simply using a pre-built RAG framework.

The project covers the complete journey:

```text
Raw Documents
      ↓
Document Processing
      ↓
Chunking
      ↓
Embeddings
      ↓
Vector Database
      ↓
Semantic Retrieval
      ↓
Context Construction
      ↓
LLM
      ↓
Generated Response
```

This makes the project a practical demonstration of how **document retrieval and Large Language Models can be combined to build knowledge-grounded AI systems**.

---
/github.com/GurbachanS

