# ResumeJe - AI-Powered Resume Builder

Build, optimize, and tailor your resume with AI. Beat ATS filters, rewrite sections, and generate cover letters – all in one place.

---

## 📂 Structure

```
resumeje/
├── backend/          # Express 5 Backend (with semantic-release)
├── frontend/         # Next.js 16 Frontend (with semantic-release)
└── centralized-resume/  # Root orchestration (with semantic-release)
```

---

## 🚀 Quick Start

```bash
cd centralized-resume
bun install
bun dev
```

**Frontend**: http://localhost:3000  
**Backend**: http://localhost:3001

---

## 📖 Full Documentation

See [centralized-resume/README.md](./centralized-resume/README.md)

---

**MIT License**


```
Browser --> Next.js API Route (/api/*) --> Express Backend (/api/v1/*)
                                                |
                                    AI Provider Chain:
                                    1. HuggingFace Router (primary)
                                    2. Groq SDK (fallback)
```

```
              +-------------+     +-------------+     +-----------+
              |  Frontend   |     |  Backend    |     | PostgreSQL|
              |  Next.js 16 | --> |  Express 5  | --> | Prisma    |
              |  React 19   |     |  REST API   |     | Messages  |
              |  Port 3000  |     |  Port 3001  |     |           |
              +-------------+     +-------------+     +-----------+
```

---

## Tech Stack

| Layer      | Technology                                                      |
| ---------- | --------------------------------------------------------------- |
| Frontend   | Next.js 16 (App Router), React 19, TypeScript                   |
| Styling    | Tailwind CSS, Framer Motion, Radix UI, shadcn/ui, Lucide React |
| State      | Redux Toolkit                                                   |
| Auth       | Google OAuth, JWT                                               |
| Backend    | Express 5, TypeScript                                           |
| AI         | HuggingFace Router (primary), Groq SDK (fallback)               |
| Database   | PostgreSQL 16, Prisma ORM                                       |
| Analytics  | Vercel Analytics                                                |
| CI/CD      | GitHub Actions                                                  |

---

## Quick Start

### Prerequisites

| Tool    | Version | Required |
| ------- | ------- | -------- |
| Bun     | 1.0+    | Yes      |
| Node.js | 18+     | Yes      |

### Setup

```bash
# 1. Clone
git clone <repo-url> && cd resumeje

# 2. Install dependencies
bun install

# 3. Set up environment
cp backend/.env.example backend/.env.development
cp frontend/.env.example frontend/.env.local
# Edit both with your API keys

# 4. Start Backend + Frontend
bun dev

# Frontend: http://localhost:3000
# Backend:  http://localhost:3001
# Health:   http://localhost:3001/health
```

That's it. un dev is all you need.

---

## Project Structure

```
resumeje/
+-- backend/
|   +-- src/
|       +-- config/env.config.ts        # Environment loading + validation
|       +-- controllers/
|       |   +-- rewrite.controller.ts   # Resume upload + AI enhance
|       |   +-- section.controller.ts   # Section/ATS/cover letter
|       |   +-- parse-text.controller.ts
|       +-- middleware/security.middleware.ts
|       +-- prompts/                    # AI prompt templates
|       +-- routes/resume.routes.ts
|       +-- services/
|       |   +-- ai.provider.ts          # HuggingFace -> Groq chain
|       |   +-- ai.service.ts           # Resume enhancement
|       |   +-- section.service.ts      # Section/ATS/cover letter AI
|       |   +-- file.parser.ts          # PDF/DOCX/TXT extraction
|       +-- server.ts                   # Express entry point
+-- frontend/
|   +-- src/app/
|       +-- (routes)/                   # Pages: resume, upload, ats, cover-letter, feedback
|       +-- api/                        # Next.js API route proxies
|       +-- components/                 # UI components
|       +-- services/                   # apiClient, aiService, authService
|       +-- store/                      # Redux Toolkit
|   +-- prisma/                         # Database schema + migrations
+-- .github/workflows/ci-cd.yml
+-- CODEBASE_REFERENCE.md
```

---

## API Endpoints

| Method | Endpoint                   | Purpose                         |
| ------ | -------------------------- | ------------------------------- |
| GET    | /health                  | Health probe                    |
| POST   | /api/v1/rewriteResume    | Upload + AI enhance full resume |
| POST   | /api/v1/parse-text       | Extract text from file          |
| POST   | /api/v1/enhance-section  | AI enhance single section       |
| POST   | /api/v1/generate-summary | Generate professional summary   |
| POST   | /api/v1/ats-score        | ATS score analysis              |
| POST   | /api/v1/cover-letter     | Generate cover letter           |

---

## Environment Variables

### Backend

| Variable              | Default                  | Required | Notes                    |
| --------------------- | ------------------------ | -------- | ------------------------ |
| NODE_ENV            | development            | Yes      | development / production |
| PORT                | 3001                   | No       |                          |
| GROQ_API_KEY        | �                        | Yes      | Groq fallback AI         |
| HUGGINGFACE_API_KEY | �                        | Recommended | Primary AI provider   |
| JWT_SECRET          | dev fallback             | Prod     | Must change in production|
| CORS_ORIGIN         | http://localhost:3000  | Yes      | Comma-separated origins  |

### Frontend

| Variable                       | Default | Notes           |
| ------------------------------ | ------- | --------------- |
| NEXT_PUBLIC_API_BASE_URL     | �       | Backend API URL |
| NEXT_PUBLIC_APP_ENV          | �       | Environment     |
| NEXT_PUBLIC_GOOGLE_CLIENT_ID | �       | OAuth           |

---

## Common Commands

```bash
# Development
bun dev                         # Backend + Frontend

# Verification
bun verify                      # ESLint + TypeScript (all)
bun verify:frontend             # Frontend only
bun verify:backend              # Backend only
bun lint:fix                    # Auto-fix linting

# Build
bun build:all                   # Build both frontend + backend
```

---

## Security

| Feature            | Status | Implementation                    |
| ------------------ | ------ | --------------------------------- |
| Helmet.js          | Yes    | CSP, HSTS, X-Frame-Options       |
| CORS               | Yes    | Configurable whitelist            |
| Rate Limiting      | Yes    | 20 req/15min, 5 uploads/15min    |
| Input Sanitization | Yes    | DOMPurify recursive               |
| File Validation    | Yes    | MIME, size (10MB), name sanitize  |
| SQL Injection      | Yes    | Prisma parameterized queries      |
| JWT Auth           | Yes    | Google OAuth + JWT validation     |
| Auto-cleanup       | Yes    | Uploads deleted after 7 days      |

---

## Git Flow

```
main ----------- production releases
  |
  +-- develop -- integration branch
        +-- feature/name
        +-- hotfix/name
```

### Commit Convention

| Prefix            | Bump  | Example                           |
| ----------------- | ----- | --------------------------------- |
| eat(scope):    | MINOR | eat(resume): add AI summary    |
| ix(scope):     | PATCH | ix(parser): handle invalid PDF |
| BREAKING CHANGE | MAJOR | BREAKING CHANGE: redesign API   |

Scopes: pi, 
esume, letter, pdf, ui, uth, db, deps, parser

---

## Production Checklist

- [ ] Set strong JWT_SECRET
- [ ] Set CORS_ORIGIN to production domain
- [ ] Set GROQ_API_KEY + HUGGINGFACE_API_KEY
- [ ] Set LOG_LEVEL=warn
- [ ] Run 
px prisma migrate deploy
- [ ] Configure SSL/TLS
- [ ] Security audit (OWASP Top 10)

---

## References

- [CODEBASE_REFERENCE.md](./CODEBASE_REFERENCE.md) � Full technical reference
- [Backend README](./backend/README.md) � Backend-specific docs
- [Frontend README](./frontend/README.md) � Frontend-specific docs

---

**MIT License**
