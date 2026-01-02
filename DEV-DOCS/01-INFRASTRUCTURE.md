# Infrastructure & Deployment

> 서버 환경 구축 및 배포 인프라 문서

---

## Overview

| Component | Technology | Purpose |
|-----------|------------|--------|
| **Server** | Ubuntu 24.04 LTS | Host OS |
| **Web Server** | Nginx | Reverse Proxy |
| **SSL/CDN** | Cloudflare | HTTPS + Caching |
| **Process Manager** | systemd | Service Management |
| **Database** | PostgreSQL 16 | Relational DB |
| **Vector Store** | ChromaDB | Embedding Storage |

---

## Architecture Diagram

```
                        Internet
                            │
                            ▼
                ┌───────────────────────┐
                │      Cloudflare       │
                │    SSL Termination    │
                │      CDN Caching      │
                └───────────┬───────────┘
                            │ HTTPS (443)
                            ▼
                ┌───────────────────────┐
                │     Ubuntu 24.04      │
                │      psqlserver       │
                └───────────┬───────────┘
                            │
                ┌───────────▼───────────┐
                │    Nginx (:80)        │
                │    Reverse Proxy      │
                └───────────┬───────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│   Next.js     │   │   FastAPI     │   │    Django     │
│    :3000      │   │    :8001      │   │    :8002      │
│  thatshoon-   │   │grade-management│  │  chatbot-api  │
│  nextjs.svc   │   │  -api.svc     │   │    .svc       │
└───────────────┘   └───────┬───────┘   └───────┬───────┘
                            │                   │
                    ┌───────▼───────┐   ┌───────▼───────┐
                    │  PostgreSQL   │   │   ChromaDB    │
                    │    :5432      │   │   VectorDB    │
                    └───────────────┘   └───────────────┘
```

---

## Nginx Configuration

### Upstream Servers
```nginx
upstream nextjs_upstream {
    server 127.0.0.1:3000;
    keepalive 64;
}

upstream grades_api_upstream {
    server 127.0.0.1:8001;
    keepalive 64;
}

upstream chatbot_api_upstream {
    server 127.0.0.1:8002;
    keepalive 64;
}
```

### Location Blocks
```nginx
# Main Frontend (Next.js)
location / {
    proxy_pass http://nextjs_upstream;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}

# Grade Management API
location /grades-api/ {
    rewrite ^/grades-api/(.*) /$1 break;
    proxy_pass http://grades_api_upstream;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
}

# Grade Management Frontend (Static)
location /grades-app/ {
    alias /home/erif/thatshoon/mini-project-01/.../frontend/dist/;
    try_files $uri $uri/ /grades-app/index.html;
    expires 7d;
    add_header Cache-Control "public";
}

# Chatbot API
location /chatbot-api/ {
    rewrite ^/chatbot-api/(.*) /$1 break;
    proxy_pass http://chatbot_api_upstream;
    proxy_read_timeout 120s;  # AI API timeout
}
```

---

## systemd Services

### Service Files Structure
```
/etc/systemd/system/
├── thatshoon-nextjs.service      # Next.js Frontend
├── thatshoon-django.service      # Django Backend
├── grade-management-api.service  # FastAPI
└── chatbot-api.service           # Django Chatbot
```

### Example Service (FastAPI)
```ini
[Unit]
Description=Grade Management API (FastAPI)
After=network.target postgresql.service

[Service]
Type=simple
User=erif
WorkingDirectory=/home/erif/thatshoon/mini-project-01/grade_management_system/backend
Environment="PATH=/path/to/venv/bin"
ExecStart=/path/to/venv/bin/uvicorn app.main:app --host 0.0.0.0 --port 8001
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

### Service Commands
```bash
# Daemon reload (after editing service files)
sudo systemctl daemon-reload

# Enable auto-start on boot
sudo systemctl enable grade-management-api

# Start/Stop/Restart
sudo systemctl start grade-management-api
sudo systemctl stop grade-management-api
sudo systemctl restart grade-management-api

# Status & Logs
sudo systemctl status grade-management-api
sudo journalctl -u grade-management-api -f
```

---

## PostgreSQL Setup

### Installation
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

### Database Creation
```sql
-- Connect as postgres user
sudo -u postgres psql

-- Create database
CREATE DATABASE grade_management_db;

-- Create user
CREATE USER erif WITH PASSWORD 'password';

-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE grade_management_db TO erif;
\c grade_management_db
GRANT ALL ON SCHEMA public TO erif;
```

---

## Cloudflare Configuration

| Setting | Value | Description |
|---------|-------|-------------|
| **SSL Mode** | Full | HTTPS to Origin |
| **Caching** | Standard | Static assets cached |
| **Firewall** | Basic | DDoS protection |

### DNS Records
| Type | Name | Content |
|------|------|--------|
| A | thatshoon.com | Server IP |
| A | www | Server IP |

---

## Deployment Workflow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Code      │────▶│   Build     │────▶│   Deploy    │
│   Change    │     │   Assets    │     │   Service   │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │                   │
       │            ┌──────┴──────┐            │
       │            │             │            │
       │      ┌─────▼─────┐ ┌─────▼─────┐      │
       │      │ Next.js   │ │   Vite    │      │
       │      │ npm build │ │ npm build │      │
       │      └───────────┘ └───────────┘      │
       │                                       │
       └──────────────┬────────────────────────┘
                      │
                      ▼
               ┌─────────────┐
               │ th-restart  │
               │   Script    │
               └─────────────┘
```

### th-restart Script
```bash
#!/bin/bash
# Full rebuild & restart

# 1. Stop all services
sudo systemctl stop thatshoon-nextjs chatbot-api grade-management-api

# 2. Build Next.js
cd /home/erif/thatshoon/frontend
rm -rf .next && npm run build

# 3. Build Vite (Grades)
cd /home/erif/thatshoon/mini-project-01/.../frontend
rm -rf .next && npm run build

# 4. Restart all services
sudo systemctl start thatshoon-nextjs chatbot-api grade-management-api
sudo systemctl reload nginx

# 5. Health check
curl https://thatshoon.com/grades-api/api/v1/health
curl https://thatshoon.com/chatbot-api/api/v1/health
```

---

## Monitoring

### Service Status
```bash
# All services at once
systemctl list-units --type=service | grep -E "(nginx|grade|chatbot|nextjs)"

# Port check
netstat -tlnp | grep -E "(3000|8001|8002|80)"
```

### Log Analysis
```bash
# Nginx access log
sudo tail -f /var/log/nginx/access.log

# Nginx error log
sudo tail -f /var/log/nginx/error.log

# Service logs
sudo journalctl -u grade-management-api --since "1 hour ago"
```

---

## Troubleshooting

| Issue | Check | Solution |
|-------|-------|----------|
| 502 Bad Gateway | Backend running? | `systemctl status <service>` |
| 500 Static Files | File permissions | `chmod 755 dist/` |
| Connection Refused | Port listening? | `netstat -tlnp` |
| SSL Error | Cloudflare mode | Set to "Full" |
