<h1 align="center">
  <img src="https://readme-typing-svg.herokuapp.com?font=Fira+Code&weight=600&size=28&pause=1000&color=3B82F6&center=true&vCenter=true&random=false&width=435&lines=Hi+there!+I'm+ThatsHoon;Full-Stack+Developer;AI+%2F+ML+Engineer" alt="Typing SVG" />
</h1>

<p align="center">
  <a href="https://thatshoon.com"><img src="https://img.shields.io/badge/Portfolio-thatshoon.com-3B82F6?style=for-the-badge&logo=google-chrome&logoColor=white" alt="Website"/></a>
  <a href="https://github.com/ThatsHoon"><img src="https://img.shields.io/badge/GitHub-ThatsHoon-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/></a>
</p>

---

## Live Services

<table>
  <tr>
    <td align="center" width="50%">
      <a href="https://thatshoon.com">
        <img src="https://img.shields.io/badge/Main_Portfolio-thatshoon.com-3B82F6?style=for-the-badge&logo=vercel&logoColor=white" alt="Main"/>
      </a>
      <br/><br/>
      <b>ThatsHoon Portfolio</b>
      <br/>
      <sub>Next.js 16 + React 19 + Tailwind CSS v4</sub>
    </td>
    <td align="center" width="50%">
      <a href="https://thatshoon.com/chatbot">
        <img src="https://img.shields.io/badge/AI_Chatbot-RAG_Service-10B981?style=for-the-badge&logo=openai&logoColor=white" alt="Chatbot"/>
      </a>
      <br/><br/>
      <b>AI Chatbot (RAG)</b>
      <br/>
      <sub>GPT-4o / Claude + ChromaDB VectorDB</sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <a href="https://thatshoon.com/grades">
        <img src="https://img.shields.io/badge/Grade_System-Management-F59E0B?style=for-the-badge&logo=postgresql&logoColor=white" alt="Grades"/>
      </a>
      <br/><br/>
      <b>Grade Management</b>
      <br/>
      <sub>FastAPI + PostgreSQL + React</sub>
    </td>
    <td align="center">
      <a href="https://thatshoon.com/chatbot">
        <img src="https://img.shields.io/badge/News_Analyzer-Sentiment-EC4899?style=for-the-badge&logo=anthropic&logoColor=white" alt="News"/>
      </a>
      <br/><br/>
      <b>News Sentiment Analysis</b>
      <br/>
      <sub>Summary + Keywords + Sentiment</sub>
    </td>
  </tr>
</table>

---

## Tech Stack

<p align="center">
  <img src="https://skillicons.dev/icons?i=nextjs,react,ts,tailwind,python,fastapi,django,postgres,docker,nginx,linux,git&theme=dark" alt="Tech Stack"/>
</p>

<details>
<summary><b>More Details</b></summary>
<br/>

| Category | Technologies |
|----------|-------------|
| **Frontend** | Next.js 16, React 19, TypeScript, Tailwind CSS v4, Vite |
| **Backend** | FastAPI, Django 5.0, Django REST Framework |
| **Database** | PostgreSQL 16, ChromaDB, SQLite |
| **AI / ML** | OpenAI GPT-4o, Anthropic Claude, Sentence Transformers |
| **Infra** | Ubuntu 24.04, Nginx, Cloudflare, systemd |

</details>

---

## System Architecture

```
                    ┌──────────────────────┐
                    │  thatshoon.com       │
                    │  Cloudflare SSL/CDN  │
                    └──────────┬───────────┘
                               │
                    ┌──────────▼───────────┐
                    │   Nginx Reverse Proxy │
                    └──────────┬───────────┘
           ┌───────────────────┼───────────────────┐
           │                   │                   │
    ┌──────▼──────┐     ┌──────▼──────┐     ┌──────▼──────┐
    │  Next.js    │     │  FastAPI    │     │   Django    │
    │  Frontend   │     │  Grades API │     │ Chatbot API │
    │   :3000     │     │   :8001     │     │   :8002     │
    └─────────────┘     └──────┬──────┘     └──────┬──────┘
                               │                   │
                        ┌──────▼──────┐     ┌──────▼──────┐
                        │ PostgreSQL  │     │  ChromaDB   │
                        │   :5432     │     │  VectorDB   │
                        └─────────────┘     └─────────────┘
```

---

## RAG VectorDB Collections

| Database | Docs | Language | Domain |
|:--------:|:----:|:--------:|:------:|
| Wikipedia | 2,490 | Korean | General |
| Medical Q&A | 3,000 | English | Healthcare |
| Korean MRC | 3,000 | Korean | Wikipedia |
| Finance | 2,000 | English | Sentiment |
| BioMedical | 2,000 | English | BioASQ |
| Korean RAG | 300 | Korean | 5 Domains |

---

## Quick Links

| Service | URL | API |
|---------|-----|-----|
| Main | [thatshoon.com](https://thatshoon.com) | - |
| AI Chatbot | [/chatbot](https://thatshoon.com/chatbot) | `/chatbot-api/` |
| Grades | [/grades](https://thatshoon.com/grades) | `/grades-api/` |

---

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=ThatsHoon&show_icons=true&theme=tokyonight&hide_border=true" alt="GitHub Stats" height="165"/>
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=ThatsHoon&layout=compact&theme=tokyonight&hide_border=true" alt="Top Languages" height="165"/>
</p>

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=ThatsHoon&color=3B82F6&style=flat-square&label=Profile+Views" alt="Profile Views"/>
</p>
