# ThatsHoon

[![Website](https://img.shields.io/badge/Website-thatshoon.com-blue?style=flat-square&logo=google-chrome)](https://thatshoon.com)
[![GitHub](https://img.shields.io/badge/GitHub-ThatsHoon-181717?style=flat-square&logo=github)](https://github.com/ThatsHoon)

풀스택 개발자 포트폴리오 - AI/ML, 웹 개발, 인프라 구축

---

## thatshoon.com

> MVP 기능을 갖춘 여러 서비스들을 데모로 전시하는 개발 포트폴리오 프로젝트

### System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Cloudflare (SSL/CDN)                         │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       Nginx Reverse Proxy (:80)                      │
└─────────────────────────────────────────────────────────────────────┘
          │                    │                    │
          ▼                    ▼                    ▼
    ┌──────────┐         ┌──────────┐         ┌──────────┐
    │ Next.js  │         │ FastAPI  │         │  Django  │
    │  :3000   │         │  :8001   │         │  :8002   │
    │ Frontend │         │ Grades   │         │ Chatbot  │
    └──────────┘         └──────────┘         └──────────┘
                              │                    │
                              ▼                    ▼
                        ┌──────────┐         ┌──────────┐
                        │PostgreSQL│         │ ChromaDB │
                        │   :5432  │         │ VectorDB │
                        └──────────┘         └──────────┘
```

---

## Tech Stack

### Frontend
![Next.js](https://img.shields.io/badge/Next.js_16-000000?style=flat-square&logo=next.js&logoColor=white)
![React](https://img.shields.io/badge/React_19-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS_v4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=flat-square&logo=vite&logoColor=white)

### Backend
![Python](https://img.shields.io/badge/Python_3.11-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Django](https://img.shields.io/badge/Django_5.0-092E20?style=flat-square&logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/DRF-A30000?style=flat-square&logo=django&logoColor=white)

### Database
![PostgreSQL](https://img.shields.io/badge/PostgreSQL_16-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![ChromaDB](https://img.shields.io/badge/ChromaDB-FF6B6B?style=flat-square&logo=databricks&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat-square&logo=sqlite&logoColor=white)

### AI / ML
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=flat-square&logo=openai&logoColor=white)
![Anthropic](https://img.shields.io/badge/Claude-CC785C?style=flat-square&logo=anthropic&logoColor=white)
![HuggingFace](https://img.shields.io/badge/HuggingFace-FFD21E?style=flat-square&logo=huggingface&logoColor=black)
![Sentence Transformers](https://img.shields.io/badge/Sentence_Transformers-FF6F00?style=flat-square&logo=pytorch&logoColor=white)

### Infrastructure
![Ubuntu](https://img.shields.io/badge/Ubuntu_24.04-E95420?style=flat-square&logo=ubuntu&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=flat-square&logo=nginx&logoColor=white)
![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=flat-square&logo=cloudflare&logoColor=white)
![systemd](https://img.shields.io/badge/systemd-2C3E50?style=flat-square&logo=linux&logoColor=white)

---

## Services

### 1. AI Chatbot (RAG)
> 다중 VectorDB를 활용한 RAG 기반 AI 채팅 서비스

| Feature | Description |
|---------|-------------|
| **RAG Search** | ChromaDB + Sentence Transformers 기반 시맨틱 검색 |
| **Multi-Provider** | OpenAI (GPT-4o) / Anthropic (Claude) 선택 지원 |
| **News Analysis** | 뉴스 기사 요약, 키워드 추출, 감정 분석 |
| **Themed VectorDB** | 의료, 금융, 생물의학, 한국어 등 6개 도메인 |

#### RAG Workflow
```
┌─────────┐     ┌──────────────┐     ┌──────────┐     ┌─────────┐
│  Query  │────▶│  Embedding   │────▶│ ChromaDB │────▶│ Context │
└─────────┘     │ (MiniLM-L6)  │     │  Search  │     └────┬────┘
                └──────────────┘     └──────────┘          │
                                                           ▼
┌─────────┐     ┌──────────────┐     ┌──────────┐     ┌─────────┐
│Response │◀────│   LLM API    │◀────│  Prompt  │◀────│ + Query │
└─────────┘     │(GPT/Claude)  │     │  Build   │     └─────────┘
                └──────────────┘     └──────────┘
```

#### VectorDB Collections
| Database | Documents | Language | Domain |
|----------|-----------|----------|--------|
| Wikipedia | 2,490 | Korean | General Knowledge |
| Medical Q&A | 3,000 | English | Healthcare (21 subjects) |
| Korean General | 3,000 | Korean | Wikipedia MRC |
| Finance | 2,000 | English | Financial Sentiment |
| BioMedical | 2,000 | English | BioASQ Challenge |
| Korean RAG | 300 | Korean | 5 Domains Evaluation |

---

### 2. Grade Management System
> 성적 관리 시스템 - CRUD with PostgreSQL

| Feature | Description |
|---------|-------------|
| **FastAPI Backend** | Python 고성능 비동기 API |
| **PostgreSQL** | 관계형 데이터베이스 |
| **React Frontend** | Vite + TypeScript SPA |
| **RESTful API** | OpenAPI 3.0 문서화 |

#### API Endpoints
```
GET    /api/v1/students        # 학생 목록
POST   /api/v1/students        # 학생 등록
GET    /api/v1/students/{id}   # 학생 상세
PUT    /api/v1/students/{id}   # 학생 수정
DELETE /api/v1/students/{id}   # 학생 삭제
GET    /api/v1/health          # Health Check
```

---

## Development Workflow

### Project Structure
```
thatshoon/
├── frontend/                    # Next.js 16 Main Frontend
│   └── src/app/
│       ├── page.tsx            # Landing Page
│       ├── chatbot/            # AI Chatbot Page
│       └── grades/             # Grade Management Page
│
├── mini-project-01/            # Grade Management System
│   └── grade_management_system/
│       ├── backend/            # FastAPI + PostgreSQL
│       └── frontend/           # React + Vite
│
├── mini-project-02/            # AI Chatbot Engine
│   └── data-preprocessing-for-input-AI/
│       ├── chatbot-backend/    # Django + DRF
│       └── chatbot-engine/     # RAG + VectorDB
│
└── dev-docs/                   # Development Documentation
```

### Service Management
```bash
# Full Rebuild & Restart
th-restart

# Service Control
sudo systemctl [start|stop|restart] thatshoon-nextjs
sudo systemctl [start|stop|restart] grade-management-api
sudo systemctl [start|stop|restart] chatbot-api

# Health Check
curl https://thatshoon.com/grades-api/api/v1/health
curl https://thatshoon.com/chatbot-api/api/v1/health
```

### URL Routing
| Path | Service | Description |
|------|---------|-------------|
| `/` | Next.js :3000 | Main Landing |
| `/chatbot` | Next.js :3000 | AI Chatbot UI |
| `/grades` | Next.js :3000 | Grade System UI |
| `/grades-api/*` | FastAPI :8001 | Grade REST API |
| `/grades-app/*` | Static | Grade Frontend SPA |
| `/chatbot-api/*` | Django :8002 | Chatbot REST API |

---

## Deployment

### CI/CD Pipeline
```
┌─────────┐     ┌──────────┐     ┌──────────┐     ┌─────────┐
│  Code   │────▶│  Build   │────▶│  Deploy  │────▶│  Live   │
│  Push   │     │ Frontend │     │ systemd  │     │ Server  │
└─────────┘     └──────────┘     └──────────┘     └─────────┘
                     │
               ┌─────┴─────┐
               │           │
          ┌────▼───┐  ┌────▼───┐
          │Next.js │  │  Vite  │
          │ Build  │  │ Build  │
          └────────┘  └────────┘
```

### Infrastructure Overview
- **Server**: Ubuntu 24.04 LTS
- **Reverse Proxy**: Nginx (upstream keepalive)
- **SSL/CDN**: Cloudflare Full Mode
- **Process Manager**: systemd services
- **Database**: PostgreSQL 16.11

---

## Contact

- **Website**: [thatshoon.com](https://thatshoon.com)
- **GitHub**: [github.com/ThatsHoon](https://github.com/ThatsHoon)
