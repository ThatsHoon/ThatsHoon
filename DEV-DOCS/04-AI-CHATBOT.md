# AI Chatbot Service

> RAG 기반 AI 챗봇 서비스 개발 문서

---

## Overview

| Component | Technology | Port |
|-----------|------------|------|
| **Backend** | Django 5.0 + DRF | 8002 |
| **Vector Store** | ChromaDB | - |
| **Embedding** | Sentence Transformers | - |
| **AI Providers** | OpenAI, Anthropic | - |

---

## Development Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                   AI Chatbot Development Flow                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Phase 1: Backend Setup                                             │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ Django  │──▶│   DRF   │──▶│  CORS   │──▶│  Apps   │            │
│  │ Project │   │ Install │   │ Config  │   │ Create  │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 2: RAG Engine                                                │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ChromaDB │──▶│Sentence │──▶│Embedding│──▶│ Vector  │            │
│  │ Setup   │   │Transform│   │ Index   │   │ Search  │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 3: AI Integration                                            │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ OpenAI  │──▶│Anthropic│──▶│ Prompt  │──▶│Response │            │
│  │   API   │   │   API   │   │ Builder │   │ Handler │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 4: Features                                                  │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐                          │
│  │  News   │──▶│Sentiment│──▶│Multi-DB │                          │
│  │ Summary │   │Analysis │   │ Select  │                          │
│  └─────────┘   └─────────┘   └─────────┘                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        AI Chatbot Service                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────┐                                                   │
│  │   Next.js   │                                                   │
│  │  Frontend   │                                                   │
│  └──────┬──────┘                                                   │
│         │                                                           │
│         │ POST /api/chatbot/send                                   │
│         ▼                                                           │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐          │
│  │   Django    │────▶│   RAG       │────▶│  ChromaDB   │          │
│  │   Backend   │     │   Service   │     │  VectorDB   │          │
│  └──────┬──────┘     └─────────────┘     └─────────────┘          │
│         │                                                           │
│         │ use_rag: true                                            │
│         ▼                                                           │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │                    Context Builder                           │  │
│  │  ┌─────────┐   ┌─────────┐   ┌─────────┐                    │  │
│  │  │  Query  │──▶│Embedding│──▶│ Search  │                    │  │
│  │  │         │   │ (MiniLM)│   │Top-K Docs│                   │  │
│  │  └─────────┘   └─────────┘   └─────────┘                    │  │
│  └──────────────────────┬──────────────────────────────────────┘  │
│                         │                                          │
│                         ▼                                          │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │                    LLM Provider                              │  │
│  │  ┌──────────────┐              ┌──────────────┐             │  │
│  │  │   OpenAI     │     OR      │  Anthropic   │             │  │
│  │  │  GPT-4o      │              │   Claude     │             │  │
│  │  └──────────────┘              └──────────────┘             │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## RAG Pipeline

```
┌─────────┐     ┌──────────────┐     ┌──────────────┐     ┌─────────┐
│  User   │────▶│   Embedding  │────▶│   ChromaDB   │────▶│ Context │
│  Query  │     │   (MiniLM)   │     │    Search    │     │  Docs   │
└─────────┘     └──────────────┘     └──────────────┘     └────┬────┘
                                                               │
                     ┌─────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         Prompt Template                              │
├─────────────────────────────────────────────────────────────────────┤
│  다음 문서를 참고하여 질문에 답변하세요.                               │
│                                                                     │
│  [참고 문서]                                                         │
│  {context_documents}                                                │
│                                                                     │
│  [질문]                                                              │
│  {user_query}                                                       │
│                                                                     │
│  [답변]                                                              │
└─────────────────────────────────────────────────────────────────────┘
                     │
                     ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   OpenAI     │ OR  │  Anthropic   │────▶│   Response   │
│   GPT-4o     │     │   Claude     │     │   to User    │
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Project Structure

```
chatbot-backend/
├── apps/
│   ├── chat/                    # Chat App
│   │   ├── models.py           # Chat History
│   │   ├── views.py            # API Views
│   │   ├── serializers.py      # DRF Serializers
│   │   └── urls.py             # URL Routing
│   │
│   ├── rag/                     # RAG App
│   │   ├── services.py         # RAG Service Logic
│   │   ├── vectordb.py         # ChromaDB Client
│   │   └── prompts.py          # Prompt Templates
│   │
│   └── users/                   # Users App
│
├── config/
│   ├── settings/
│   │   ├── base.py             # Base Settings
│   │   ├── development.py
│   │   └── production.py
│   ├── urls.py                 # URL Config
│   └── wsgi.py
│
├── data/
│   └── vectordb/               # ChromaDB Data
│
├── venv/
├── manage.py
└── .env
```

---

## API Endpoints

### Chat
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/chat/send/` | Send message |
| GET | `/api/v1/chat/history/` | Get chat history |

### Databases
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/databases/` | List VectorDBs |

### Request Body (Chat)
```json
{
  "message": "사용자 질문",
  "api_key": "sk-...",
  "provider": "openai",
  "model": "gpt-4o-mini",
  "use_rag": true,
  "use_news": false,
  "db_name": "default"
}
```

### Response
```json
{
  "response": "AI 응답 내용",
  "sources": [
    {
      "content": "참조 문서 내용",
      "metadata": {...}
    }
  ]
}
```

---

## Feature Modes

### 1. RAG Mode (자료검색)
- VectorDB에서 관련 문서 검색
- Context + Query를 LLM에 전달
- 출처 정보와 함께 응답

### 2. News Mode (뉴스 요약)
- 뉴스 기사 입력
- 요약 + 키워드 + 감정 분석
- JSON 형식 응답

```json
{
  "summary": "기사 요약 내용",
  "keywords": ["키워드1", "키워드2", ...],
  "sentiment": "긍정/부정/중립"
}
```

### 3. Fine-tuning Mode (준비중)
- 사용자 정의 모델 학습
- 도메인 특화 응답

---

## AI Providers

### OpenAI Models
| Model | Use Case |
|-------|----------|
| gpt-4.1-mini | Fast, cost-effective |
| gpt-4.1 | Balanced |
| gpt-4o-mini | Latest mini |
| gpt-4o | Most capable |

### Anthropic Models
| Model | Use Case |
|-------|----------|
| claude-sonnet-4-20250514 | Latest |
| claude-3-5-sonnet-latest | Balanced |
| claude-3-5-haiku-latest | Fast |

---

## Dependencies

```txt
# Django
Django>=5.0
djangorestframework>=3.14.0
django-cors-headers>=4.3.0

# RAG
sentence-transformers>=2.2.0
chromadb>=0.4.0

# AI APIs
openai>=1.0.0
anthropic>=0.8.0

# Utilities
python-dotenv>=1.0.0
```

---

## Deployment

```bash
# Start service
sudo systemctl start chatbot-api

# View logs
sudo journalctl -u chatbot-api -f

# Health check
curl https://thatshoon.com/chatbot-api/api/v1/health
```
