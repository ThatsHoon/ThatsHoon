# Main Frontend (Next.js)

> thatshoon.com 메인 프론트엔드 개발 문서

---

## Overview

| Item | Value |
|------|-------|
| **Framework** | Next.js 16.1.1 |
| **React** | 19.2.3 |
| **Language** | TypeScript |
| **Styling** | Tailwind CSS v4 |
| **Port** | 3000 |
| **URL** | https://thatshoon.com |

---

## Development Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Next.js Development Flow                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Project Setup                                                   │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐                          │
│  │ create- │──▶│Configure│──▶│ Install │                          │
│  │next-app │   │tsconfig │   │  deps   │                          │
│  └─────────┘   └─────────┘   └─────────┘                          │
│                                                                     │
│  2. UI Development                                                  │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │ Design  │──▶│Tailwind │──▶│ Create  │──▶│  Test   │            │
│  │ System  │   │  Theme  │   │  Pages  │   │   UI    │            │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘            │
│                                                                     │
│  3. API Integration                                                 │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐                          │
│  │ Create  │──▶│ Connect │──▶│  Handle │                          │
│  │API Route│   │ Backend │   │Response │                          │
│  └─────────┘   └─────────┘   └─────────┘                          │
│                                                                     │
│  4. Build & Deploy                                                  │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐                          │
│  │npm build│──▶│ Start   │──▶│  Nginx  │                          │
│  │         │   │ Server  │   │  Proxy  │                          │
│  └─────────┘   └─────────┘   └─────────┘                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Project Structure

```
frontend/
├── src/
│   └── app/                    # App Router (Next.js 13+)
│       ├── layout.tsx          # Root Layout (Header/Footer)
│       ├── page.tsx            # Home Page
│       ├── globals.css         # Global Styles + Tailwind
│       │
│       ├── chatbot/
│       │   └── page.tsx        # AI Chatbot Page
│       │
│       ├── grades/
│       │   └── page.tsx        # Grade Management (iframe)
│       │
│       └── api/
│           └── chatbot/
│               ├── send/
│               │   └── route.ts    # Chat API Proxy
│               └── databases/
│                   └── route.ts    # DB List API
│
├── public/
│   ├── icon.png
│   └── apple-icon.png
│
├── package.json
├── next.config.ts
├── tsconfig.json
└── postcss.config.mjs
```

---

## Page Architecture

### Home Page (`/`)
```
┌─────────────────────────────────────────────────────────────────┐
│  Header (Navigation)                                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    Hero Section                          │   │
│  │              "ThatsHoon Portfolio"                        │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────┐   ┌─────────────────────┐            │
│  │   Grade Management  │   │     AI Chatbot      │            │
│  │   Service Card      │   │   Service Card      │            │
│  │   → /grades         │   │   → /chatbot        │            │
│  └─────────────────────┘   └─────────────────────┘            │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  Footer                                                         │
└─────────────────────────────────────────────────────────────────┘
```

### Chatbot Page (`/chatbot`)
```
┌─────────────────────────────────────────────────────────────────┐
│  Header                                                          │
├──────────────────┬──────────────────────────────────────────────┤
│                  │                                              │
│  Settings Panel  │              Chat Area                       │
│  ┌────────────┐  │  ┌────────────────────────────────────┐     │
│  │Search Mode │  │  │                                    │     │
│  │ ○ RAG      │  │  │         Message History            │     │
│  │ ○ News     │  │  │                                    │     │
│  │ ○ Finetune │  │  │                                    │     │
│  ├────────────┤  │  │                                    │     │
│  │AI Provider │  │  │                                    │     │
│  │ OpenAI     │  │  └────────────────────────────────────┘     │
│  │ Claude     │  │  ┌────────────────────────────────────┐     │
│  ├────────────┤  │  │ Input Area                    [Send]│     │
│  │Model Select│  │  └────────────────────────────────────┘     │
│  ├────────────┤  │                                              │
│  │API Key     │  │                                              │
│  ├────────────┤  │                                              │
│  │VectorDB    │  │                                              │
│  │ ○ Wikipedia│  │                                              │
│  │ ○ Medical  │  │                                              │
│  │ ○ Finance  │  │                                              │
│  │ ...        │  │                                              │
│  └────────────┘  │                                              │
│                  │                                              │
└──────────────────┴──────────────────────────────────────────────┘
```

---

## Design System

### Color Palette
```css
:root {
  /* Brand Colors */
  --primary: #4B39EF;      /* Purple */
  --secondary: #39D2C0;    /* Teal */
  --accent: #EE8B60;       /* Orange */

  /* Background & Surface */
  --background: #1D2428;   /* Dark */
  --surface: #14181B;      /* Darker */
  --border: #2A3137;       /* Border */

  /* Text Colors */
  --foreground: #F2F2F2;   /* Primary Text */
  --muted-foreground: #A3A3A3; /* Secondary Text */
}
```

### Tailwind v4 Theme
```css
@theme inline {
  --color-primary: var(--primary);
  --color-secondary: var(--secondary);
  --color-accent: var(--accent);
  --color-background: var(--background);
  --color-surface: var(--surface);
  --font-sans: 'Inter Tight', sans-serif;
}
```

---

## API Routes

### Chat API Proxy
```typescript
// src/app/api/chatbot/send/route.ts
export async function POST(request: NextRequest) {
  const body = await request.json();
  
  const response = await fetch(
    `${CHATBOT_API_URL}/api/v1/chat/send/`,
    {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(body),
    }
  );
  
  return NextResponse.json(await response.json());
}
```

### Database List API
```typescript
// src/app/api/chatbot/databases/route.ts
export async function GET() {
  // Reads VectorDB metadata from filesystem
  const vectorDbPath = "/path/to/vectordb";
  const databases = readdirSync(vectorDbPath)
    .filter(isDirectory)
    .map(getMetadata);
  
  return NextResponse.json({ databases });
}
```

---

## Component Patterns

### State Management (useState)
```typescript
const [messages, setMessages] = useState<Message[]>([]);
const [searchMode, setSearchMode] = useState<SearchMode>("rag");
const [provider, setProvider] = useState<AIProvider>("openai");
const [model, setModel] = useState("gpt-4.1-mini");
const [apiKey, setApiKey] = useState("");
const [vectorDb, setVectorDb] = useState("default");
```

### Effect Hooks
```typescript
// Auto-scroll to latest message
useEffect(() => {
  messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
}, [messages]);

// Fetch VectorDB list on mount
useEffect(() => {
  fetchVectorDbs();
}, []);

// Update model when provider changes
useEffect(() => {
  setModel(provider === "openai" ? "gpt-4.1-mini" : "claude-sonnet-4");
}, [provider]);
```

---

## Build & Deploy

### Development
```bash
npm install
npm run dev    # localhost:3000
```

### Production Build
```bash
npm run build
npm run start  # Production server
```

### Build Output
```
Route (app)
┌ ○ /                         # Static
├ ○ /chatbot                  # Static
├ ○ /grades                   # Static
├ ƒ /api/chatbot/send         # Dynamic (API)
└ ƒ /api/chatbot/databases    # Dynamic (API)

○ (Static)   prerendered as static content
ƒ (Dynamic)  server-rendered on demand
```

---

## Dependencies

```json
{
  "dependencies": {
    "next": "16.1.1",
    "react": "19.2.3",
    "react-dom": "19.2.3",
    "axios": "^1.13.2",
    "lucide-react": "^0.562.0"
  },
  "devDependencies": {
    "@tailwindcss/postcss": "^4",
    "tailwindcss": "^4",
    "typescript": "^5"
  }
}
```
