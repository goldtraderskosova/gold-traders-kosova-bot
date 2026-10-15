# Gold Traders Kosova

A Telegram-ready XAU/USD practice terminal with Clerk accounts and persistent virtual-money trading.

## Run & Operate

- `pnpm --filter @workspace/gold-traders-kosova run dev` — run the web mini app
- `pnpm --filter @workspace/api-server run dev` — run the shared API server
- `pnpm run typecheck` — typecheck libraries, artifacts, and scripts
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from OpenAPI
- `pnpm --filter @workspace/db run push` — apply development database schema changes
- Replit provides the PostgreSQL connection and managed Clerk configuration at runtime; never commit or print secret values.

## Stack

- pnpm workspaces, TypeScript, React, Vite, Tailwind CSS
- Express API, PostgreSQL, Drizzle ORM, Zod, OpenAPI/Orval-generated client
- Replit-managed Clerk for registration and login

## Where things live

- `artifacts/gold-traders-kosova/src/App.tsx` — terminal, positions, history, auth routes, and Telegram WebApp bridge
- `artifacts/gold-traders-kosova/src/index.css` — dark gold visual theme and responsive styles
- `lib/api-spec/openapi.yaml` — source of truth for the API contract
- `artifacts/api-server/src/routes/` — market and demo-account endpoints
- `artifacts/api-server/src/lib/market.ts` — public XAU price feed and recent in-process price ticks
- `artifacts/api-server/src/lib/demo-trading.ts` — position close accounting and server-side TP/SL checks
- `lib/db/src/schema/` — demo accounts, trades, and funding transactions
- `lib/api-client-react/src/generated/` — generated frontend API hooks

## Architecture decisions

- Clerk user IDs own each demo account and its trades; clients never submit another user's identity.
- XAU/USD spot quotes come from Gold-API over the server API. The short-term chart uses actual fetched price ticks, not fabricated historical prices.
- Position sizes are denominated in troy ounces. The simulator reserves full position notional from available funds and does not apply leverage.
- Live tick history is in process memory and rebuilds after an API server restart; account balances, positions, and closed trades persist in PostgreSQL.
- TP/SL checks run on the API server, validate against position direction, and close at the first observed quote crossing. Deposit and withdrawal ledger rows commit atomically with their balance changes.

## Product

- Registration and login, a starting $10,000 virtual account, XAU/USD quote and tick chart, virtual BUY/SELL positions, optional automatic TP/SL, open-position and P/L tracking, closed trade history with exit reason, and simulated deposit/withdraw controls with persistent funding history.
- Show a clear DEMO TRADING badge; all account activity is virtual.
- The Telegram WebApp SDK bridge calls `ready()` and `expand()` when opened inside Telegram.

## User preferences

- Mobile-friendly premium black-and-gold presentation.
- Demo trading only; no real deposits or withdrawals yet.

## Gotchas

- Browser API calls use Clerk's same-origin session cookies; do not add bearer-token handling to this web app.
- Demo APIs require a signed-in Clerk session. Public XAU/USD price data does not.
- The Telegram WebApp SDK prepares the interface for Telegram; a published URL still needs to be connected to a Telegram bot to launch there.
