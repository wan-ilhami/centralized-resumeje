# ResumeJe Codebase Reference

**Updated**: April 20, 2026 | **Stack**: Full-Stack TypeScript | **Status**: Production-Ready

---

## Rules

- Every file must pass `bun verify` before commit
- Pre-commit hook blocks broken code
- AI Agent: read this file first, check Issues, run verify after changes

---

## Quick Facts

| Property | Value                                                    |
| -------- | -------------------------------------------------------- |
| Frontend | Next.js 16, React 19, TypeScript, Tailwind CSS v4, Redux |
| Backend  | Express 5, TypeScript, Multer                            |
| AI       | HuggingFace Router (primary) ? Groq SDK (fallback)       |
| DB       | PostgreSQL 16, Prisma ORM (feedback only)                |
| Ports    | Frontend: 3000, Backend: 3001                            |
| Env      | Consolidated: `.env` (root) with backend & frontend vars |

---

## Architecture

```
Browser ? Next.js API Routes (/api/*) ? Express (/api/v1/*)
                                              �
                                          Direct AI
                                      Return result + data

AI Chain: HuggingFace (primary) ? Groq (fallback on error/rate-limit)
Data: PostgreSQL (messages) + Filesystem (uploads, auto-cleanup 7d)
```

---

## File Structure

### Backend (`backend/src/`)

```
config/env.config.ts       # Env loading, validation
controllers/
  rewrite.controller.ts    # File upload ? parse ? AI enhance
  section.controller.ts    # Section/ATS/cover letter
  parse-text.controller.ts # Text extraction
middleware/security.middleware.ts  # Helmet, CORS, rate-limit, sanitize
prompts/                   # AI prompt templates
routes/resume.routes.ts    # Route registry
services/
  ai.provider.ts           # HuggingFace ? Groq chain
  ai.service.ts            # Resume enhancement logic
  section.service.ts       # Section/ATS/cover letter AI
  file.parser.ts           # PDF/DOCX/TXT extraction
  resume.parser.ts         # Regex fallback parser
types/ai.types.ts
utils/                     # logger, errorHandler, fileCleanup
server.ts                  # Express entry
```

### Frontend (`frontend/src/app/`)

```
(routes)/                  # resume, upload, ats-checker, cover-letter, feedback
api/                       # Next.js API proxies to backend
components/                # layout/, forms/, ui/, template-resume/
hooks/                     # Custom React hooks
lib/prisma.ts              # Prisma singleton
services/
  apiClient.ts             # HTTP client (with auth header)
  aiService.ts             # Frontend ? backend AI calls
  authService.ts           # JWT storage
  messageService.ts        # CRUD for messages
store/                     # Redux Toolkit
types/                     # TypeScript interfaces
```

---

## API Endpoints

| Method | Endpoint                   | Purpose                  |
| ------ | -------------------------- | ------------------------ |
| GET    | `/health`                  | Health probe             |
| POST   | `/api/v1/rewriteResume`    | File upload ? AI enhance |
| POST   | `/api/v1/parse-text`       | Extract text from file   |
| POST   | `/api/v1/enhance-section`  | AI enhance section       |
| POST   | `/api/v1/generate-summary` | Professional summary     |
| POST   | `/api/v1/ats-score`        | ATS match analysis       |
| POST   | `/api/v1/cover-letter`     | Generate cover letter    |

---

## AI Provider Chain

```
callAI(opts)
  +- HF key missing? ? Groq directly
  +- Try HuggingFace Router (meta-llama/Llama-3.1-8B-Instruct)
  �   +- Fail/rate-limit ? catch
  +- Fallback: Groq SDK (llama-3.3-70b-versatile)
      +- Both fail ? AppError(429) or throw
```

| Feature         | Temp | Tokens |
| --------------- | ---- | ------ |
| Resume Rewrite  | 0.4  | 4096   |
| Section Enhance | 0.4  | 1024   |
| ATS Score       | 0.2  | 1536   |
| Cover Letter    | 0.6  | 900    |
| Summary         | 0.5  | 512    |

---

## Security

- Helmet.js (CSP, HSTS, X-Frame-Options)
- CORS whitelist per environment
- Rate limiting: 20/15min general, 5/15min uploads (disabled in dev)
- DOMPurify recursive sanitization on body/query
- File validation: MIME whitelist, 10MB max, filename sanitize
- Prisma parameterized queries (SQL injection safe)
- JWT auth, auto-cleanup uploads (7 days)
- Trust proxy for reverse proxies in production

---

## Environment Variables

### Backend

| Key                       | Default                 | Notes                  |
| ------------------------- | ----------------------- | ---------------------- |
| `NODE_ENV`                | `development`           | development/production |
| `PORT`                    | `3001`                  |                        |
| `GROQ_API_KEY`            | �                       | Required for Groq AI   |
| `HUGGINGFACE_API_KEY`     | �                       | Primary AI provider    |
| `JWT_SECRET`              | dev fallback            | Must change in prod    |
| `CORS_ORIGIN`             | `http://localhost:3000` | Comma-separated        |
| `UPLOAD_MAX_MB`           | `10`                    |                        |
| `RATE_LIMIT_MAX_REQUESTS` | `20`                    |                        |
| `RATE_LIMIT_MAX_UPLOAD`   | `5`                     |                        |

### Frontend

| Key                            | Notes             |
| ------------------------------ | ----------------- |
| `NEXT_PUBLIC_API_BASE_URL`     | Backend API URL   |
| `NEXT_PUBLIC_APP_ENV`          | Environment label |
| `NEXT_PUBLIC_GOOGLE_CLIENT_ID` | Google OAuth      |

---

## Performance

- React.memo + useCallback: minimized re-renders
- CSS modules: scoped styling
- MutationObserver: instant theme switch
- next/image lazy loading
- Vercel Analytics: usage tracking

---

## Version History

| Ver   | Date  | Changes                                        |
| ----- | ----- | ---------------------------------------------- |
| 1.1.0 | Apr19 | Simplified: removed Worker, Redis, K8s, Docker |
| 1.0.0 | Mar31 | Initial production build                       |

---

## Commands

```bash
bun dev                  # Frontend + backend
bun verify               # ESLint + TypeScript
bun lint:fix             # Auto-fix linting
bun build:all            # Production build
bun dev                  # All you need to run locally
```

---

## Commit Convention

```
feat(scope):    ? MINOR    fix(scope):     ? PATCH
BREAKING CHANGE ? MAJOR    docs/chore/test ? no bump
```

Scopes: `api`, `resume`, `letter`, `pdf`, `ui`, `auth`, `db`, `deps`, `parser`

---

## Deployment

### Local Development

1. `bun install`
2. Create `.env.development` with API keys
3. `bun dev`
4. Verify http://localhost:3000 + http://localhost:3001/health

### Production (Vercel)

1. Push to GitHub and connect to Vercel
2. Set environment variables: `GROQ_API_KEY`, `HUGGINGFACE_API_KEY`, `JWT_SECRET`, `CORS_ORIGIN`
3. Vercel auto-deploys on push to main
4. Backend: Deploy separately (Docker/Railway/Heroku/VPS)

---

## Contributing

1. Branch: `feature/name` or `hotfix/name` from `develop`
2. Implement + `bun verify`
3. Commit (conventional) + PR ? develop
4. Auto-version on merge to main
