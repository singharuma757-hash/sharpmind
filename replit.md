# Sharp Mind

A mobile-first brain training & science quiz app with 4 subject modes (Math, Physics, Chemistry, Logic), global leaderboards, daily quests, 1v1 duels, and a gamified XP/Elo progression system. Developed by hiru_coder.

## Run & Operate

- `pnpm --filter @workspace/sharp-mind run dev` — run the frontend (port auto-assigned)
- `pnpm --filter @workspace/api-server run dev` — run the API server
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from OpenAPI spec (includes automatic zod.int() → zod.number().int() patch)
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React 19 + Vite 7, Tailwind CSS 4, Framer Motion, Recharts, Wouter
- API: Express 5
- DB: PostgreSQL + Drizzle ORM (DEFAULT_PLAYER_ID = 1)
- Validation: Zod v3, `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — single source of truth for all API contracts
- `lib/db/src/schema/` — Drizzle table definitions (players, sessions, subject_stats, achievements, duels, daily_quests)
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/sharp-mind/src/` — React frontend, pages under `src/pages/`
- `lib/api-client-react/src/generated/` — generated React Query hooks (do not edit)
- `lib/api-zod/src/generated/` — generated Zod schemas (do not edit)

## Architecture decisions

- **Single default player (id=1)**: No auth in v1 — all data is scoped to player id=1. Auth can be layered in later.
- **New accounts start fresh**: Auto-created player always has XP=0, Level=1, Brain Score=0.
- **Orval `zod.int()` patch**: Orval 8.23.0 generates `zod.int()` (Zod v4 API) but workspace uses Zod v3. The codegen script patches both generated files with `sed` after orval runs.
- **Procedural math engine**: Math questions are generated algorithmically (arithmetic, sequences, algebra, missing operator) so they never repeat. Physics/Chemistry/Logic use a curated question bank.
- **Client-managed game loop**: The `/game` screen manages session state locally (combo, lives, score) and syncs to the API at session start/end. `useSubmitAnswer` records individual answers for XP.
- **Mobile-first layout**: Max width 430px, bottom nav, dark mode forced. Design built for phone-screen dimensions at all times.

## Product

- **Dashboard** — radar chart (Recharts) showing Math/Physics/Chemistry/Logic strength, daily streak, quest progress bars, recent activity
- **Play** — 4 subject cards with difficulty selector (Easy/Medium/Hard/Expert), animated start flow
- **Game HUD** — countdown timer, 3-heart lives, combo multiplier, instant green/red answer feedback with explanation overlay
- **Leaderboard** — global Elo rankings, tabbed by subject, my rank highlighted
- **Daily Quests** — 4 daily tasks, streak counter, daily check-in XP reward
- **Achievements** — 12 achievements (unlocked glow vs locked faded)
- **Duels** — large hero Create/Join buttons, futuristic 1v1 battle cards with VS badge, player avatars, subject glow lines; duplicate/empty matches filtered out
- **Profile** — XP bar, level, Elo score, subject mastery breakdown

## Visual Theme

- Ultra-gaming neon cyberpunk dark theme
- Circuit-board grid background, ambient neon glow blobs (cyan, violet)
- Scanline overlay for CRT effect
- Per-subject neon colors: Math=cyan, Physics=violet, Chemistry=neon-green, Logic=amber
- Neon bottom nav bar with glowing active indicator

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- After any OpenAPI spec change, always run codegen: `pnpm --filter @workspace/api-spec run codegen`
- The `zod.int()` → `zod.number().int()` patch is baked into the codegen script in `lib/api-spec/package.json` — do not remove it
- DB push: `pnpm --filter @workspace/db run push` (use `push-force` if there are column conflicts)
- DEFAULT_PLAYER_ID = 1 in all route files (was restored from 11 during project recovery)

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
