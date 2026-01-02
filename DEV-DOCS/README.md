# Development Documentation

> thatshoon.com 프로젝트 개발 문서 및 워크플로우 가이드

---

## Development Timeline

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Development Workflow                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Phase 1: Infrastructure Setup                                      │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ Ubuntu  │──▶│PostgreSQL│──▶│  Nginx  │──▶│Cloudflare│            │
│  │  24.04  │   │   16    │   │ Reverse │   │  SSL    │            │
│  │         │   │         │   │  Proxy  │   │         │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 2: Main Frontend                                             │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐                          │
│  │ Next.js │──▶│  React  │──▶│Tailwind │                          │
│  │   16    │   │   19    │   │  CSS v4 │                          │
│  └─────────┘   └─────────┘   └─────────┘                          │
│                                                                     │
│  Phase 3: Grade Management System                                   │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ FastAPI │──▶│SQLAlchemy│──▶│  React  │──▶│  Vite   │            │
│  │ Backend │   │   ORM   │   │   18    │   │  Build  │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 4: AI Chatbot Service                                        │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ Django  │──▶│   DRF   │──▶│ OpenAI  │──▶│ Claude  │            │
│  │   5.0   │   │   API   │   │   API   │   │   API   │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 5: RAG VectorDB                                              │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │HuggingFace│──▶│Sentence │──▶│ChromaDB │──▶│  RAG    │            │
│  │ Datasets │   │Transformers│  │VectorDB│   │ Search  │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Documentation Index

| # | Document | Description |
|---|----------|-------------|
| 1 | [Infrastructure](./01-INFRASTRUCTURE.md) | 서버 환경, Nginx, systemd 설정 |
| 2 | [Main Frontend](./02-MAIN-FRONTEND.md) | Next.js 16 메인 프론트엔드 |
| 3 | [Grade Management](./03-GRADE-MANAGEMENT.md) | 성적관리 시스템 (FastAPI + React) |
| 4 | [AI Chatbot](./04-AI-CHATBOT.md) | AI 챗봇 서비스 (Django + RAG) |
| 5 | [RAG VectorDB](./05-RAG-VECTORDB.md) | VectorDB 구축 및 RAG 워크플로우 |

---

## Project Structure

```
thatshoon/
├── frontend/                    # Next.js 16 Main Frontend
│   └── src/app/
│       ├── page.tsx            # Landing Page
│       ├── chatbot/            # AI Chatbot Page
│       ├── grades/             # Grade Management Page
│       └── api/chatbot/        # API Routes (Proxy)
│
├── mini-project-01/            # Grade Management System
│   └── grade_management_system/
│       ├── backend/            # FastAPI + PostgreSQL
│       │   ├── app/
│       │   │   ├── main.py
│       │   │   ├── models/
│       │   │   ├── routers/
│       │   │   └── services/
│       │   └── venv/
│       └── frontend/           # React + Vite
│           ├── src/
│           └── dist/
│
├── mini-project-02/            # AI Chatbot Engine
│   └── data-preprocessing-for-input-AI/
│       ├── chatbot-backend/    # Django + DRF
│       │   ├── apps/
│       │   │   ├── chat/
│       │   │   └── rag/
│       │   └── config/
│       └── chatbot-engine/     # RAG + VectorDB
│           ├── scripts/
│           └── data/vectordb/
│
├── dev-docs/                   # Local Documentation
├── *.service                   # systemd Service Files
└── nginx-thatshoon.conf        # Nginx Configuration
```

---

## Quick Commands

### Service Management
```bash
# Full rebuild & restart
th-restart

# Individual services
sudo systemctl [start|stop|restart] thatshoon-nextjs
sudo systemctl [start|stop|restart] grade-management-api
sudo systemctl [start|stop|restart] chatbot-api
sudo systemctl reload nginx
```

### Health Checks
```bash
curl https://thatshoon.com/grades-api/api/v1/health
curl https://thatshoon.com/chatbot-api/api/v1/health
```

### Logs
```bash
sudo journalctl -u thatshoon-nextjs -f
sudo journalctl -u grade-management-api -f
sudo journalctl -u chatbot-api -f
sudo tail -f /var/log/nginx/error.log
```

---

## Tech Stack Summary

| Layer | Technology | Version |
|-------|------------|--------|
| **Frontend** | Next.js | 16.1.1 |
| | React | 19.2.3 |
| | TypeScript | 5.x |
| | Tailwind CSS | 4.x |
| | Vite | 5.x |
| **Backend** | FastAPI | 0.109+ |
| | Django | 5.0 |
| | DRF | 3.14+ |
| | SQLAlchemy | 2.0 |
| **Database** | PostgreSQL | 16.11 |
| | ChromaDB | 0.4+ |
| **AI/ML** | OpenAI API | GPT-4o |
| | Anthropic API | Claude |
| | Sentence Transformers | MiniLM-L6-v2 |
| **Infra** | Ubuntu | 24.04 LTS |
| | Nginx | Latest |
| | Cloudflare | SSL/CDN |
