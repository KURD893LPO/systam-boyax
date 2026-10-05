# بۆیاغی ئەکتیڤ

A Sorani Kurdish, right-to-left shop manager for tracking paint products, customers, staff, sales, and customer debt in USD and IQD.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/active-paint-shop/src/` — Sorani RTL web interface and shop pages
- `lib/api-spec/openapi.yaml` — source of truth for the shared API contract
- `artifacts/api-server/src/routes/shop.ts` — shop endpoints and the starter product catalog
- `lib/db/src/schema/shop.ts` — PostgreSQL tables for products, customers, staff, sales, sale items, and debt payments
- `lib/api-client-react/src/generated/` and `lib/api-zod/src/generated/` — generated API hooks and validation schemas

## Architecture decisions

- Prices and balances remain in the product's original currency; USD and IQD are never combined or converted.
- Sale lines retain a product name and price snapshot so later catalog edits do not rewrite purchase history.
- Removing a product, customer, staff member, sale, or payment deactivates it instead of deleting its record, preserving shop history.
- Debt payments are customer-level balances and are tracked separately by currency.
- The cash ledger for USD/IQD is separate from sales and debt; its balance comes only from logged deposits, withdrawals, and exchanges.
- Shop expenses are categorized separately but still reduce the matching USD/IQD cash balance and remain visible in the currency ledger.
- Initial setup seeds the requested 27 products idempotently; it does not create sample customers or staff.

## Product

The dashboard summarizes products, sales, and debt. The product catalog supports search and editing; sales record the customer, optional staff member, items taken, and amounts paid; customer ledgers show purchase and payment history; debt, customer, and staff pages support day-to-day shop management.

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Keep `dom.iterable` enabled for `lib/api-client-react`; the generated client uses `Headers.entries()`.
- Archived sales and payments stay visible in customer history but do not affect current sales totals or debt balances.
- Restoring an archived payment is allowed only when the customer's current debt can cover it.
- The public USD/IQD reference feed updates daily and requires a visible ExchangeRate-API attribution; it is not a local Iraqi market quote.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
