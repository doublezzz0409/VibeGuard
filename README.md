# VibeGuard

**Contract-driven AI development workflow for full-stack projects.**

English | [中文](./README_CN.md)

## Why does this exist?

**Q: Before VibeGuard, how were you using AI to build projects?**

Letting AI generate code, then repeatedly fixing it. Tried many "magic" skills — prompt templates, workflow plugins, self-improving agents. None of them worked reliably. The root cause: AI is a probabilistic model. A skill might trigger correctly this time and fail next time. You can't control probability.

**Q: So what did you change?**

Stopped relying on AI's "understanding" and started encoding intent as mechanical steps. A skill says "you should do this" — the AI interprets it probabilistically. VibeGuard says "execute this step, input is X, output goes to Y, then verify against Z" — the AI follows instructions mechanically. Skills assume the AI has judgment. VibeGuard assumes it doesn't.

**Q: What low-level mistakes does VibeGuard actually prevent?**

The kind every project accumulates:
- Field names inconsistent between frontend and backend → `naming-convention.md` enforces mechanical conversion
- Types use `any` or "format不限" → step 1 requires precise, falsifiable type definitions
- Mock data drifts from API contract → step 14.5 regenerates mocks from the final document
- Links point to non-existent routes → steps 9-10 scan all links against the route table
- Frontend works but backend doesn't exist yet → MSW mocks let frontend run standalone
- Database column names don't match API fields → mock-api-doc.md's 5-column table removes ambiguity

These are deterministic errors. With the right constraints, they're 100% eliminable.

**Q: Does this scale as features accumulate?**

VibeGuard maintains **interface-layer order**, not implementation-layer order. As the project grows from 28 to 100 endpoints, naming stays consistent, API contracts stay synchronized, and mocks stay aligned. Business logic bloat and architecture decay still happen — those are inherent to software engineering, not AI-specific. Even the best human teams can't fully prevent them.

What VibeGuard does guarantee: when you need to refactor, the cost is lower. Contracts document every interface boundary. Naming is searchable and replaceable. Tests cover every path. You have a map of the territory, even if the territory gets messy.

**Q: Is this more useful for new projects or existing ones?**

Existing projects. Onboarding VibeGuard onto a legacy codebase automatically audits the current state — extracting types, documenting APIs, exposing naming inconsistencies. The cross-validation steps catch real inconsistencies between existing code and the new contract. For new projects, the constraints prevent mistakes; for old projects, they also **find existing mistakes**.

**Q: Can this prevent all bugs?**

No. VibeGuard raises the floor, not the ceiling. It eliminates low-level errors (naming, types, contracts, routing) that humans also struggle with — "随便吧，能跑就行" is how most technical debt starts. For security, concurrency, and financial transactions, human oversight is still required. The framework is designed to be extended: add constraint items to the security checklist as project needs grow.

**Q: What's the realistic positioning?**

VibeGuard is a **discipline framework**, not a silver bullet. It does the same thing for AI that code review, PR checks, and CI pipelines do for human teams — not eliminating errors, but making them detectable and recoverable. The philosophy: accept that errors will happen, but guarantee that they're caught fast and fixed cheaply.

---

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
