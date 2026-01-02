# Grade Management System

> 성적 및 출결 관리 시스템 개발 문서

---

## Overview

| Component | Technology | Port |
|-----------|------------|------|
| **Backend** | FastAPI + SQLAlchemy | 8001 |
| **Frontend** | React 18 + Vite | Static |
| **Database** | PostgreSQL 16 | 5432 |
| **Auth** | JWT (python-jose) | - |

---

## Development Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                 Grade Management Development Flow                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Phase 1: Database Design                                           │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │  ER     │──▶│SQLAlchemy│──▶│Migration│──▶│PostgreSQL│            │
│  │ Diagram │   │ Models  │   │ Alembic │   │ Tables  │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 2: API Development                                           │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ Pydantic│──▶│ FastAPI │──▶│  CRUD   │──▶│ OpenAPI │            │
│  │ Schemas │   │ Routers │   │ Services│   │  Docs   │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 3: Frontend Development                                      │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │  React  │──▶│ Zustand │──▶│TanStack │──▶│  Vite   │            │
│  │  Pages  │   │  Store  │   │  Query  │   │  Build  │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  Phase 4: Integration & Deploy                                      │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐                          │
│  │  CORS   │──▶│ systemd │──▶│  Nginx  │                          │
│  │ Config  │   │ Service │   │  Proxy  │                          │
│  └─────────┘   └─────────┘   └─────────┘                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Database Schema

```
┌─────────────────┐       ┌─────────────────┐
│      User       │       │     Course      │
├─────────────────┤       ├─────────────────┤
│ id (UUID) PK    │       │ id (UUID) PK    │
│ email           │       │ name            │
│ full_name       │       │ code            │
│ password_hash   │       │ description     │
│ role            │       │ credits         │
│ is_active       │       └────────┬────────┘
└─────────────────┘                │
                                   │ 1:N
┌─────────────────┐       ┌────────▼────────┐
│     Student     │       │      Class      │
├─────────────────┤       ├─────────────────┤
│ id (UUID) PK    │       │ id (UUID) PK    │
│ student_id      │◄──────│ course_id FK    │
│ name            │  N:M  │ name            │
│ email           │       │ semester        │
│ phone           │       └────────┬────────┘
└────────┬────────┘                │
         │                         │
         │ 1:N                     │ 1:N
         │                         │
┌────────▼────────┐       ┌────────▼────────┐
│      Grade      │       │   Attendance    │
├─────────────────┤       ├─────────────────┤
│ id (UUID) PK    │       │ id (UUID) PK    │
│ student_id FK   │       │ student_id FK   │
│ evaluation_id FK│       │ class_id FK     │
│ score           │       │ date            │
│ created_at      │       │ status          │
└─────────────────┘       └─────────────────┘
```

---

## Backend Architecture

### Project Structure
```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI App Entry
│   ├── config.py            # Settings (pydantic-settings)
│   ├── database.py          # DB Connection & Session
│   │
│   ├── models/              # SQLAlchemy Models
│   │   ├── user.py
│   │   ├── student.py
│   │   ├── course.py
│   │   ├── grade.py
│   │   └── attendance.py
│   │
│   ├── schemas/             # Pydantic Schemas
│   │   ├── user.py
│   │   ├── student.py
│   │   └── ...
│   │
│   ├── routers/             # API Routers
│   │   ├── auth.py          # POST /auth/login
│   │   ├── students.py      # CRUD /students
│   │   ├── courses.py       # CRUD /courses
│   │   ├── grades.py        # CRUD /grades
│   │   ├── attendance.py    # CRUD /attendance
│   │   └── reports.py       # GET /reports/*
│   │
│   └── services/            # Business Logic
│       ├── auth.py
│       └── reports.py
│
├── venv/
├── requirements.txt
└── .env
```

### API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/login` | JWT Login |
| GET | `/api/v1/students` | List Students |
| POST | `/api/v1/students` | Create Student |
| GET | `/api/v1/students/{id}` | Get Student |
| PUT | `/api/v1/students/{id}` | Update Student |
| DELETE | `/api/v1/students/{id}` | Delete Student |
| GET | `/api/v1/courses` | List Courses |
| POST | `/api/v1/grades` | Input Grade |
| POST | `/api/v1/grades/bulk` | Bulk Input |
| POST | `/api/v1/attendance` | Input Attendance |
| GET | `/api/v1/reports/grades/excel` | Download Excel |
| GET | `/api/v1/reports/grades/pdf` | Download PDF |

---

## Frontend Architecture

### Project Structure
```
frontend/
├── src/
│   ├── main.tsx             # App Entry
│   ├── App.tsx              # Router Setup
│   ├── index.css            # Tailwind
│   │
│   ├── pages/               # Page Components
│   │   ├── LoginPage.tsx
│   │   ├── DashboardPage.tsx
│   │   ├── StudentsPage.tsx
│   │   ├── CoursesPage.tsx
│   │   ├── GradesPage.tsx
│   │   ├── AttendancePage.tsx
│   │   └── ReportsPage.tsx
│   │
│   ├── components/          # Reusable Components
│   │   ├── common/
│   │   ├── forms/
│   │   ├── tables/
│   │   └── modals/
│   │
│   ├── services/
│   │   └── api.ts           # Axios Instance
│   │
│   ├── store/
│   │   └── authStore.ts     # Zustand Auth Store
│   │
│   └── types/
│       └── index.ts         # TypeScript Types
│
├── dist/                    # Build Output
├── vite.config.ts
└── package.json
```

### State Management Flow
```
┌─────────────────────────────────────────────────────────────────┐
│                        React App                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐        │
│  │   Zustand   │    │  TanStack   │    │    Axios    │        │
│  │   Store     │    │   Query     │    │   Client    │        │
│  │             │    │             │    │             │        │
│  │ • user      │    │ • useQuery  │    │ • baseURL   │        │
│  │ • token     │    │ • useMutation│   │ • interceptor│       │
│  │ • isAuth    │    │ • cache     │    │ • JWT token │        │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘        │
│         │                  │                  │                │
│         └──────────────────┼──────────────────┘                │
│                            │                                    │
│                            ▼                                    │
│                   ┌─────────────┐                              │
│                   │   FastAPI   │                              │
│                   │   Backend   │                              │
│                   └─────────────┘                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Authentication Flow

```
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│  User   │────▶│  Login  │────▶│ FastAPI │────▶│   JWT   │
│         │     │  Form   │     │  /auth  │     │  Token  │
└─────────┘     └─────────┘     └─────────┘     └────┬────┘
                                                     │
                ┌────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────────────────────────┐
│                      Zustand Store                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │    user     │  │    token    │  │  isAuth     │         │
│  │   Object    │  │access_token │  │   true      │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────────────────────────┐
│                    Axios Interceptor                         │
│  Authorization: Bearer {access_token}                        │
└─────────────────────────────────────────────────────────────┘
```

---

## Deployment

### Backend (FastAPI)
```bash
# Start service
sudo systemctl start grade-management-api

# View logs
sudo journalctl -u grade-management-api -f
```

### Frontend (Vite)
```bash
# Build
cd frontend && npm run build

# Output to dist/
# Served by Nginx at /grades-app/
```

### URL Mapping
| URL | Target |
|-----|--------|
| `/grades` | Next.js (iframe to /grades-app/) |
| `/grades-app/*` | Nginx Static (dist/) |
| `/grades-api/*` | FastAPI (:8001) |

---

## Key Dependencies

### Backend
```txt
fastapi>=0.109.0
uvicorn[standard]>=0.27.0
sqlalchemy>=2.0.25
psycopg2-binary>=2.9.9
python-jose[cryptography]>=3.3.0
passlib[bcrypt]>=1.7.4
reportlab>=4.0.0
openpyxl>=3.1.0
```

### Frontend
```json
{
  "react": "^18.2.0",
  "react-router-dom": "^6.22.0",
  "@tanstack/react-query": "^5.0.0",
  "zustand": "^4.5.0",
  "axios": "^1.6.0",
  "tailwindcss": "^3.4.0",
  "vite": "^5.0.0"
}
```
