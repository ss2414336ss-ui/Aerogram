# AEROGRAM

**Your business. Running intelligently.**

Aerogram is a multi-tenant AI business operations platform. This repository is a runnable MVP foundation: premium dark dashboard, demo inbox and AI activity, a FastAPI backend, policy-gated action simulation, a provider-neutral email adapter interface, PostgreSQL schema with tenant RLS, and a reviewable historical-email learning pipeline.

> **Honest implementation status:** The included application runs in demo mode. The Gmail/Outlook adapters are interfaces/stubs, the AI provider is a deterministic demo adapter, and no real email is sent. Production OAuth, model-provider calls, provider webhooks, payment/accounting integrations, legal review, security assessment and deployment credentials must be configured and tested before live use.

## Repository layout

```text
aerogram/
├── apps/web/                 # Next.js dashboard
├── apps/api/                 # FastAPI backend
├── database/schema.sql       # PostgreSQL schema and RLS policies
├── docs/                     # Architecture and security notes
├── docker-compose.yml
├── .env.example
└── README.md
```

## Requirements

- Node.js 20.9+ and npm
- Python 3.11+
- Docker Desktop (optional, for PostgreSQL)

## 1. Configure environment

Copy `.env.example` to `.env` and change values as needed. The demo works without AI or email provider credentials. Never commit `.env` or production secrets.

## 2. Start PostgreSQL (optional)

```bash
docker compose up -d db
```

Apply the schema using your preferred PostgreSQL client:

```bash
psql "$DATABASE_URL" -f database/schema.sql
```

The SQL uses an `app.current_organization_id` session setting as a demonstration RLS context. In production, replace this with a verified identity-to-tenant mechanism (for example, Supabase JWT claims and policies). Do not allow clients to set arbitrary tenant context.

## 3. Run the API

```bash
cd apps/api
python -m venv .venv
# macOS/Linux: source .venv/bin/activate
# Windows: .venv\\Scripts\\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

API docs: http://localhost:8000/docs

## 4. Run the web app

In a second terminal:

```bash
cd apps/web
npm install
npm run dev
```

Open http://localhost:3000. The current dashboard uses clearly labelled demo fixtures; the API is a separate working service with demo endpoints. The web-to-API data wiring is a next integration task.

## Included routes

- `GET /health`
- `GET /api/v1/dashboard/overview`
- `GET /api/v1/emails`
- `GET /api/v1/emails/{id}`
- `POST /api/v1/emails/{id}/draft`
- `POST /api/v1/emails/{id}/send` — simulated only; policy-gated and never sends a real message
- `GET /api/v1/agents`
- `GET /api/v1/learning/profile`
- `POST /api/v1/learning/analyze` — analyzes the bundled sample corpus only
- `GET /api/v1/activity`

## Safety model

- Default mode is `shadow`; no real outbound email is sent.
- Email body text is untrusted input and cannot change system policy.
- Historical replies are examples, not authoritative policies.
- High-impact actions require human approval.
- The demo send endpoint returns a simulated result.
- Real provider integration must add OAuth, encrypted token storage, webhook verification, sync cursors, provider-specific scopes, rate limits, idempotency and audit persistence.

## Next implementation steps

1. Add real authentication and organization membership.
2. Connect a test Gmail OAuth project with minimum scopes; complete provider verification before public launch.
3. Replace the demo email adapter with Gmail API and Microsoft Graph adapters.
4. Add managed secret storage and encrypted OAuth refresh tokens.
5. Replace the demo AI adapter with a model-provider abstraction and structured-output validation.
6. Add persistent job queue/workflow orchestration and an approval service.
7. Run tenant-isolation, prompt-injection, duplicate-send, privacy and AI-evaluation test suites.
8. Complete privacy/legal/security review before processing customer mail.

## Product blueprint

See the accompanying `Aerogram_Complete_Product_Blueprint.pdf` supplied separately. This codebase implements the foundation and demonstration workflows, not every future module described in the blueprint.
