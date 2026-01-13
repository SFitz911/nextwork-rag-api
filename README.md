# NextWork RAG API

A Retrieval-Augmented Generation (RAG) API built with FastAPI, ChromaDB, and Ollama. This project provides an intelligent question-answering system that retrieves relevant context from a knowledge base and generates accurate answers using a local language model.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [How It Works](#how-it-works)
- [Docker Deployment](#docker-deployment)
- [Development](#development)

## 🎯 Overview

NextWork RAG API is a production-ready RAG (Retrieval-Augmented Generation) system that combines the power of vector databases with large language models to provide accurate, context-aware answers to user queries. The system uses semantic search to find relevant information from a knowledge base and then uses a language model to generate comprehensive answers based on that context.

### What is RAG?

RAG (Retrieval-Augmented Generation) is an AI technique that:
1. **Retrieves** relevant information from a knowledge base using semantic search
2. **Augments** the language model's prompt with this retrieved context
3. **Generates** accurate, contextually-aware responses

This approach allows the system to answer questions based on specific documents or knowledge bases, rather than relying solely on the model's training data.

## ✨ Features

- **Semantic Search**: Uses ChromaDB for efficient vector-based document retrieval
- **Local LLM Integration**: Powered by Ollama with TinyLlama model for fast, local inference
- **RESTful API**: Clean FastAPI interface for easy integration
- **Persistent Storage**: ChromaDB provides persistent vector storage
- **Docker Support**: Containerized deployment for easy distribution
- **Interactive Documentation**: Built-in Swagger UI and ReDoc for API exploration

## 🏗️ Architecture

The system follows a three-stage RAG pipeline:

```
User Query → Vector Search (ChromaDB) → Context Retrieval → LLM Generation (Ollama) → Response
```

1. **Query Processing**: User submits a question via the API
2. **Vector Search**: ChromaDB searches the knowledge base for semantically similar content
3. **Context Retrieval**: Most relevant document chunks are retrieved
4. **Answer Generation**: Ollama's TinyLlama model generates an answer using the retrieved context
5. **Response**: Formatted answer is returned to the user

## 🛠️ Technology Stack

- **FastAPI**: Modern, fast web framework for building APIs
- **ChromaDB**: Open-source vector database for embeddings storage and similarity search
- **Ollama**: Local LLM runtime for running language models without cloud dependencies
- **TinyLlama**: Lightweight language model optimized for fast inference
- **Uvicorn**: ASGI server for running FastAPI applications
- **Python 3.11**: Programming language and runtime

## 📁 Project Structure

```
nextwork-rag-api/
├── app.py              # FastAPI application and API endpoints
├── embed.py            # Script to embed documents into ChromaDB
├── k8s.txt             # Knowledge base document (Kubernetes documentation)
├── db/                 # ChromaDB persistent storage directory
├── Dockerfile          # Docker container configuration
├── .gitignore          # Git ignore rules
└── README.md           # Project documentation
```

## 📦 Prerequisites

Before you begin, ensure you have the following installed:

- **Python 3.11+**: [Download Python](https://www.python.org/downloads/)
- **Ollama**: [Install Ollama](https://ollama.ai/)
- **Git**: [Install Git](https://git-scm.com/downloads)

### Setting Up Ollama

1. Install Ollama from [ollama.ai](https://ollama.ai/)
2. Pull the TinyLlama model:
   ```bash
   ollama pull tinyllama
   ```

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/SFitz911/nextwork-rag-api.git
cd nextwork-rag-api
```

### 2. Create Virtual Environment

**Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**
```cmd
python -m venv venv
venv\Scripts\activate
```

**macOS/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install fastapi uvicorn chromadb ollama
```

### 4. Initialize the Knowledge Base

Run the embedding script to load documents into ChromaDB:

```bash
python embed.py
```

This will:
- Read the `k8s.txt` file
- Generate embeddings for the content
- Store them in the ChromaDB collection named "docs"

## 💻 Usage

### Starting the Services

The system requires two services to be running:

#### Terminal 1: Start Ollama Server
```bash
ollama serve
```

#### Terminal 2: Start the FastAPI Application
```bash
# Activate virtual environment (if not already active)
.\venv\Scripts\Activate.ps1  # Windows PowerShell
# or
      # Windows CMD
# or
source venv/bin/activate     # macOS/Linux

# Start the API server
uvicorn app:app --reload
```

The API will be available at `http://127.0.0.1:8000`

### Making Queries

#### Using PowerShell (Windows)
```powershell
Invoke-WebRequest -Uri "http://127.0.0.1:8000/query?q=What%20is%20Kubernetes%3F" -Method POST
```

#### Using curl
```bash
curl -X POST "http://127.0.0.1:8000/query?q=What%20is%20Kubernetes%3F"
```

#### Using Python
```python
import requests

response = requests.post(
    "http://127.0.0.1:8000/query",
    params={"q": "What is Kubernetes?"}
)
print(response.json())
```

## 🔌 API Endpoints

### `GET /`
Root endpoint that returns API information.

**Response:**
```json
{
  "message": "NextWork RAG API is running",
  "endpoints": {
    "POST /query": "Query the knowledge base with a question",
    "GET /docs": "Interactive API documentation (Swagger UI)",
    "GET /redoc": "Alternative API documentation (ReDoc)"
  }
}
```

### `POST /query`
Query the knowledge base with a question.

**Parameters:**
- `q` (query string, required): The question to ask

**Example Request:**
```
POST /query?q=What%20is%20Kubernetes%3F
```

**Example Response:**
```json
{
  "answer": "Kubernetes is a container orchestration platform used to manage containers at scale..."
}
```

### `GET /docs`
Interactive API documentation (Swagger UI) - available at `http://127.0.0.1:8000/docs`

### `GET /redoc`
Alternative API documentation (ReDoc) - available at `http://127.0.0.1:8000/redoc`

## 🔄 How It Works

### Step-by-Step Process

1. **Document Embedding** (`embed.py`):
   - Reads source documents (e.g., `k8s.txt`)
   - ChromaDB automatically generates vector embeddings
   - Stores embeddings in a persistent collection

2. **Query Processing** (`app.py`):
   - User submits a question via `/query` endpoint
   - ChromaDB performs semantic search to find the most relevant document chunk
   - Retrieves the top result (n_results=1)

3. **Answer Generation**:
   - Constructs a prompt with the retrieved context and user question
   - Sends prompt to Ollama's TinyLlama model
   - Model generates an answer based on the provided context
   - Returns the answer to the user

### Why This Approach?

- **Accuracy**: Answers are grounded in actual documents, reducing hallucinations
- **Updatability**: Knowledge base can be updated without retraining the model
- **Efficiency**: Only relevant context is sent to the LLM, reducing token usage
- **Local Processing**: All computation happens locally, ensuring data privacy

## 🐳 Docker Deployment

The project includes a Dockerfile for containerized deployment.

### Building the Docker Image

```bash
docker build -t nextwork-rag-api .
```

### Running the Container

**Note**: The Dockerfile assumes Ollama is running separately. For a complete Docker setup, you may need to use Docker Compose to orchestrate both services.

```bash
docker run -p 8000:8000 nextwork-rag-api
```

### Docker Compose (Recommended)

Create a `docker-compose.yml` to run both Ollama and the API:

```yaml
version: '3.8'
services:
  ollama:
    image: ollama/ollama
    volumes:
      - ollama_data:/root/.ollama
    ports:
      - "11434:11434"
  
  api:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - ollama
    environment:
      - OLLAMA_HOST=http://ollama:11434

volumes:
  ollama_data:
```

## 🧪 Development

### Adding New Documents

1. Add your document (e.g., `new-doc.txt`) to the project root
2. Modify `embed.py` to include the new document:
   ```python
   with open("new-doc.txt", "r") as f:
       text = f.read()
   collection.add(documents=[text], ids=["new-doc"])
   ```
3. Run `python embed.py` to update the knowledge base

### Changing the LLM Model

To use a different Ollama model:

1. Pull the model: `ollama pull <model-name>`
2. Update `app.py`:
   ```python
   answer = ollama.generate(
       model="<model-name>",  # Change here
       prompt=f"Context:\n{context}\n\nQuestion: {q}\n\nAnswer clearly and concisely:"
   )
   ```

### Adjusting Retrieval Parameters

Modify the `n_results` parameter in `app.py` to retrieve more or fewer context chunks:

```python
results = collection.query(query_texts=[q], n_results=3)  # Get top 3 results
```

## 📝 License

This project is open source and available for use and modification.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the issues page.

## 📧 Contact

For questions or support, please open anvenv\Scripts\activate   issue on the GitHub repository.

---

**Built with ❤️ using FastAPI, ChromaDB, and Ollama**