# AlgerPay

A professional money transfer platform for Algeria — receive money via Baridimob and transfer globally to Wise, PayPal, Paysera, and Western Union.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm --filter @workspace/algerpay-web run dev` — run the web frontend
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL`, `SESSION_SECRET`

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + TailwindCSS + shadcn/ui + Wouter + react-query
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Auth: express-session + connect-pg-simple + bcrypt
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)
- i18n: i18next + react-i18next (AR, FR, EN)

## Where things live

- `artifacts/algerpay-web/` — React frontend (served at `/`)
- `artifacts/api-server/` — Express backend (served at `/api`)
- `lib/db/` — Drizzle schema + migrations
- `lib/api-spec/` — OpenAPI spec (`openapi.yaml`)
- `lib/api-client-react/` — Generated React Query hooks + Zod schemas

## Architecture decisions

- Contract-first API: OpenAPI spec → Orval codegen → typed hooks and schemas.
- Session-based auth (no JWT) — sessions stored in PostgreSQL via connect-pg-simple.
- Exchange rate hardcoded at 1€ = 280 DZD; fees defined in a tiered table.
- Baridimob account `00799999002808537624` is display-only with a copy button.
- Status history is stored as JSONB in the transfers table for simplicity.

## Product

- **Users** register, log in, create transfer requests (EUR amount, destination wallet, recipient info).
- **Fee calculator** shows fee + DZD amount in real time during transfer creation.
- **Receipt upload** — users upload Baridimob payment proof; admin reviews it.
- **Status timeline** — transfers move through: pending_payment → receipt_received → funds_received → transfer_in_progress → completed.
- **Admin panel** — overview stats, filterable transfers table, status updates, archive/delete, PDF export.
- **Multilingual** — Arabic (RTL), French, English; toggle in navbar.
- **Dark mode** — full theme toggle.

## User preferences

- Admin credentials: `admin@algerpay.dz` / `Admin@1234` (seeded on server start)
- Exchange rate: 1€ = 280 DZD

## Gotchas

- The frontend uses `import.meta.env.BASE_URL` from Vite — don't hardcode `/`.
- Receipts are stored as base64 data URLs in the DB. For production, use object storage.
- Always run `pnpm --filter @workspace/api-spec run codegen` after changing `openapi.yaml`.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
