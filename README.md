# VibeGuard

**Contract-driven AI development workflow for full-stack projects.**

VibeGuard is a set of Claude Code slash commands that enforce a disciplined, step-by-step development pipeline. Instead of trusting AI to "figure it out," VibeGuard constrains it to mechanical execution against explicit contracts — types, API specs, naming conventions, and mock data that are cross-validated at every step.

## The Problem

AI coding agents are powerful but unreliable at self-checking:
- They write links that point to non-existent routes
- They call APIs with mismatched field names
- They skip Loading/Empty/Error states
- They use vague type definitions (`any`, "format不限")
- They produce inconsistent naming across frontend and backend

VibeGuard compensates by replacing trust with contracts and verification.

## How It Works

```
Human: "I want a blog with articles, categories, and comments"
         │
         ▼
  /translate-requirement     ← Iterative questioning → structured spec
         │
         ├──► /frontend-standard   ← 15-step mechanical execution
         │         │
         │         ▼
         │    types.ts, api-contract.ts, mock-api-doc.md
         │    components, services, E2E tests
         │
         ├──► /backend-assess       ← Feasibility review before coding
         │         │
         │         ▼
         │    backend-feasibility.md (approve / reject)
         │
         └──► /backend-standard     ← 8-step backend implementation
                   │
                   ▼
              ORM models, Pydantic schemas, API routes,
              business logic, security audit, tests
```

Each command runs in its own conversation window to prevent context pollution.

## Commands

| Command | Role | Output |
|---------|------|--------|
| `/translate-requirement` | Requirement analyst — asks questions until the spec is precise | Structured requirement document |
| `/frontend-standard` | Frontend executor — 15 steps from types to E2E tests | `types.ts`, `api-contract.ts`, mock handlers, components, Playwright tests |
| `/backend-assess` | Technical reviewer — feasibility check before any backend code | `backend-feasibility.md` (or `REJECT_FEEDBACK.md` if blocked) |
| `/backend-standard` | Backend executor — 8 steps from ORM to security audit | Models, schemas, routes, services, pytest suite |

## Key Design Principles

**Contract-driven.** Every artifact (`types.ts`, `api-contract.ts`, `mock-api-doc.md`, `naming-convention.md`) is a contract that downstream steps must mechanically follow. No improvisation.

**Cross-validated.** Each step verifies its output against existing contracts. Inconsistencies halt execution immediately — the AI cannot choose which version to keep.

**Naming is centrally governed.** `naming-convention.md` is the single source of truth. Entity names, field names, ID prefixes, API endpoints, and database columns are all derived from it through mechanical transformation rules (camelCase ↔ snake_case, PascalCase → table names, prefix mapping).

**AI limitations are explicitly addressed.** The commands state upfront: "You have no global vision. You cannot be trusted to self-check." Every step forces the AI into rigid execution with mandatory verification.

## Project Structure (after running all commands)

```
your-project/
├── blog-frontend/
│   ├── src/
│   │   ├── types.ts              # All entity interfaces
│   │   ├── api-contract.ts       # API endpoint type contracts
│   │   ├── services/index.ts     # Typed API client functions
│   │   ├── mocks/
│   │   │   ├── handlers.ts       # MSW mock handlers (all endpoints)
│   │   │   └── browser.ts        # MSW worker setup
│   │   ├── components/           # React components
│   │   └── main.tsx              # Entry point (MSW conditional startup)
│   ├── e2e/                      # Playwright E2E tests
│   ├── mock-api-doc.md           # Global API contract (single source of truth for backend)
│   └── .gitignore
├── backend/
│   ├── app/
│   │   ├── models/               # SQLAlchemy ORM models
│   │   ├── schemas/              # Pydantic validation models
│   │   ├── routers/              # API route handlers
│   │   └── services/             # Business logic
│   ├── tests/                    # pytest test suite
│   ├── migrations/               # Alembic migration scripts
│   ├── backend-feasibility.md    # Pre-implementation assessment
│   ├── BACKEND_DELIVERY_LOG.md   # Implementation retrospective
│   └── .gitignore
└── naming-convention.md          # Project-wide naming rules (single source of truth)
```

## Getting Started

1. Copy the four command files into your project's `.claude/commands/` directory:
   ```
   your-project/.claude/commands/
   ├── translate-requirement.md
   ├── frontend-standard.md
   ├── backend-assess.md
   └── backend-standard.md
   ```

2. Copy `naming-convention.md` to your project root (or let `/frontend-standard` generate it on first run).

3. Start with `/translate-requirement` and describe what you want to build.

4. Follow the workflow: requirement → frontend → backend assessment → backend implementation.

## Tech Stack (default)

- **Frontend:** React 19 + TypeScript + Vite + Tailwind CSS + React Router
- **Backend:** FastAPI + SQLAlchemy + Pydantic + Alembic + SQLite/PostgreSQL
- **Mocking:** MSW (Mock Service Worker) — frontend runs standalone without backend
- **Testing:** Playwright (E2E) + pytest (API tests)

The commands are opinionated about the stack but the contract-driven approach is transferable.

## License

MIT
