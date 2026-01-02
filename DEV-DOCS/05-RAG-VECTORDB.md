# RAG VectorDB

> VectorDB 구축 및 RAG 워크플로우 문서

---

## Overview

| Component | Technology |
|-----------|------------|
| **Vector Store** | ChromaDB |
| **Embedding Model** | all-MiniLM-L6-v2 |
| **Data Source** | HuggingFace Datasets |
| **Total Documents** | 12,790 |

---

## VectorDB Build Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VectorDB Build Pipeline                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Dataset Download                                                │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐          │
│  │ HuggingFace │────▶│  Download   │────▶│  Raw JSON   │          │
│  │  Datasets   │     │   Script    │     │   Files     │          │
│  └─────────────┘     └─────────────┘     └─────────────┘          │
│                                                                     │
│  2. Text Processing                                                 │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐          │
│  │  Load JSON  │────▶│   Extract   │────▶│  Chunk/     │          │
│  │   Data      │     │   Fields    │     │  Clean      │          │
│  └─────────────┘     └─────────────┘     └─────────────┘          │
│                                                                     │
│  3. Embedding Generation                                            │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐          │
│  │  Sentence   │────▶│   Batch     │────▶│   Vector    │          │
│  │ Transformer │     │  Encoding   │     │  (384-dim)  │          │
│  └─────────────┘     └─────────────┘     └─────────────┘          │
│                                                                     │
│  4. ChromaDB Storage                                                │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐          │
│  │  Create     │────▶│   Add       │────▶│   Persist   │          │
│  │ Collection  │     │  Documents  │     │   to Disk   │          │
│  └─────────────┘     └─────────────┘     └─────────────┘          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Dataset Collections

| Database | Source | Documents | Language | Domain |
|----------|--------|-----------|----------|--------|
| **default** | Wikipedia Korean | 2,490 | Korean | General |
| **medical** | MedMCQA | 3,000 | English | Healthcare |
| **korean-general** | KorQuAD v1 | 3,000 | Korean | Wikipedia MRC |
| **finance** | FinGPT Sentiment | 2,000 | English | Financial |
| **bio** | BioASQ | 2,000 | English | Biomedical |
| **korean-rag** | Allganize RAG | 300 | Korean | 5 Domains |

### Total: 12,790 Documents

---

## Dataset Configuration

```yaml
# dataset_config.yaml
datasets:
  default:
    name: "Wikipedia"
    name_ko: "기본DB(Wikipedia)"
    hf_id: "wikipedia"
    subset: "20231101.ko"
    language: "ko"
    max_samples: 3000
    fields:
      content: "text"
      metadata: ["title"]

  medical:
    name: "Medical Q&A"
    name_ko: "의료"
    hf_id: "openlifescienceai/medmcqa"
    language: "en"
    max_samples: 3000
    fields:
      content: "question"
      metadata: ["subject_name", "topic_name"]

  korean-general:
    name: "Korean General"
    name_ko: "한국어 일반"
    hf_id: "KorQuAD/squad_kor_v1"
    language: "ko"
    max_samples: 3000
    fields:
      content: "context"
      metadata: ["title"]

  finance:
    name: "Finance"
    name_ko: "금융"
    hf_id: "FinGPT/fingpt-sentiment-train"
    language: "en"
    max_samples: 2000
    fields:
      content: "input"
      metadata: ["output"]

  bio:
    name: "BioMedical"
    name_ko: "생물의학"
    hf_id: "rag-datasets/rag-mini-bioasq"
    config_name: "question-answer-passages"
    language: "en"
    max_samples: 2000

  korean-rag:
    name: "Korean RAG Eval"
    name_ko: "한국어 RAG"
    hf_id: "allganize/RAG-Evaluation-Dataset-KO"
    language: "ko"
    max_samples: 500
```

---

## Build Script

```python
# build_vectordb.py
from sentence_transformers import SentenceTransformer
import chromadb
import json

# Initialize
embedder = SentenceTransformer('sentence-transformers/all-MiniLM-L6-v2')
client = chromadb.PersistentClient(path="./data/vectordb")

def build_collection(dataset_id: str, documents: list):
    """Build VectorDB collection from documents."""
    
    # Create collection with cosine similarity
    collection = client.get_or_create_collection(
        name=dataset_id,
        metadata={"hnsw:space": "cosine"}
    )
    
    # Batch processing
    batch_size = 100
    for i in range(0, len(documents), batch_size):
        batch = documents[i:i+batch_size]
        
        # Extract texts and generate embeddings
        texts = [doc["content"] for doc in batch]
        embeddings = embedder.encode(texts).tolist()
        
        # Add to collection
        collection.add(
            ids=[f"{dataset_id}_{i+j}" for j in range(len(batch))],
            documents=texts,
            embeddings=embeddings,
            metadatas=[doc.get("metadata", {}) for doc in batch]
        )
    
    return collection.count()
```

---

## RAG Search Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                         RAG Search Flow                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Input: User Query                                                  │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ "당뇨병의 증상과 치료법에 대해 알려주세요"                         │   │
│  └───────────────────────────┬─────────────────────────────────┘   │
│                              │                                      │
│                              ▼                                      │
│  Step 1: Query Embedding                                           │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ SentenceTransformer.encode(query)                            │   │
│  │ → Vector [0.123, -0.456, 0.789, ...] (384 dimensions)       │   │
│  └───────────────────────────┬─────────────────────────────────┘   │
│                              │                                      │
│                              ▼                                      │
│  Step 2: Vector Search (ChromaDB)                                  │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ collection.query(                                            │   │
│  │   query_embeddings=[query_vector],                          │   │
│  │   n_results=5  # Top-K                                      │   │
│  │ )                                                            │   │
│  └───────────────────────────┬─────────────────────────────────┘   │
│                              │                                      │
│                              ▼                                      │
│  Step 3: Context Documents                                         │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ Doc 1: "당뇨병은 인슐린 분비 장애로..." (similarity: 0.89)     │   │
│  │ Doc 2: "2형 당뇨병의 주요 증상은..." (similarity: 0.85)       │   │
│  │ Doc 3: "당뇨 치료에는 생활습관 개선..." (similarity: 0.82)    │   │
│  └───────────────────────────┬─────────────────────────────────┘   │
│                              │                                      │
│                              ▼                                      │
│  Step 4: Prompt Building                                           │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ System: 다음 문서를 참고하여 답변하세요.                         │   │
│  │ Context: [Doc 1, Doc 2, Doc 3]                              │   │
│  │ Question: 당뇨병의 증상과 치료법에 대해 알려주세요              │   │
│  └───────────────────────────┬─────────────────────────────────┘   │
│                              │                                      │
│                              ▼                                      │
│  Step 5: LLM Response                                              │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ GPT-4o / Claude generates answer using context               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Directory Structure

```
chatbot-engine/
├── scripts/
│   ├── dataset_config.yaml    # Dataset configurations
│   ├── download_datasets.py   # HuggingFace downloader
│   └── build_vectordb.py      # VectorDB builder
│
└── data/
    └── vectordb/
        ├── databases.json     # Metadata
        ├── default/           # Wikipedia collection
        │   └── chroma.sqlite3
        ├── medical/           # Medical collection
        ├── finance/           # Finance collection
        ├── bio/               # BioMedical collection
        ├── korean-general/    # Korean MRC collection
        └── korean-rag/        # Korean RAG collection
```

---

## Metadata Format

```json
// databases.json
{
  "default": {
    "id": "default",
    "name": "Wikipedia",
    "name_ko": "기본DB(Wikipedia)",
    "description": "한국어 위키피디아 문서",
    "language": "ko",
    "document_count": 2490,
    "embedding_model": "sentence-transformers/all-MiniLM-L6-v2"
  },
  "medical": {
    "id": "medical",
    "name": "Medical Q&A",
    "name_ko": "의료",
    "description": "Medical entrance exam questions",
    "language": "en",
    "document_count": 3000
  }
  // ... other collections
}
```

---

## Performance Notes

| Metric | Value |
|--------|-------|
| Embedding Dimension | 384 |
| Similarity Metric | Cosine |
| Search Latency | ~50ms |
| Top-K Results | 5 (default) |

### Embedding Model
- **Model**: `all-MiniLM-L6-v2`
- **Size**: 22M parameters
- **Speed**: ~2000 sentences/sec (GPU)
- **Quality**: Good for semantic search

---

## Commands

```bash
# Build all VectorDBs
cd /home/erif/thatshoon/mini-project-02/data-preprocessing-for-input-AI/chatbot-engine
source ../venv/bin/activate
python scripts/build_vectordb.py

# Build specific collection
python scripts/build_vectordb.py --dataset medical

# Check collection stats
python -c "
import chromadb
client = chromadb.PersistentClient('./data/vectordb')
for col in client.list_collections():
    print(f'{col.name}: {col.count()} docs')
"
```
