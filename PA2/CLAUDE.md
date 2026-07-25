# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repo layout: three independent git repositories

This directory is a **docs-only umbrella repo** (`Domitory_Management_Docs` on GitHub). Its `.gitignore` excludes `src/` entirely, so `src/backend` and `src/frontend` are *not* tracked here — each is its own independent git repository with its own GitHub remote:

- `docs/` — tracked by this repo (proposal, requirements, spec-kit scaffolding under `docs/requirements/.specify`)
- `src/backend/` — separate repo, remote `Domitory_Management_Backend`
- `src/frontend/` — separate repo, remote `Domitory_Management_Frontend`

When committing, always check which of the three repos you're in (`git remote -v`) — a change touching both frontend and backend needs two separate commits/pushes in two separate repos, not one.

## Architecture

NestJS + MongoDB backend (`src/backend`) paired with a Next.js App Router frontend (`src/frontend`). Backend serves the REST/WebSocket API on port `3001`; frontend runs the normal Next.js dev server.

**Backend** (`src/backend/src`): one NestJS module per domain — `auth`, `users`, `rooms`, `bookings`, `contracts`, `invoices`, `maintenance`, `notifications` — each with `*.module.ts` / `*.controller.ts` / `*.service.ts` / Mongoose `schemas/`. Notable pieces:

- Auth is custom JWT, not Passport-driven per-route: `JwtAuthGuard` (`src/auth/jwt-auth.guard.ts`) manually verifies the bearer token, loads the user from Mongo to check `accessStatus` (rejects `LOCKED` accounts), and attaches `role`/`accessStatus` to `request.user`. Role checks are a separate `RolesGuard` + `@Roles(...)` decorator (`src/auth/roles.decorator.ts`) reading metadata key `'roles'`.
- Realtime notifications go through `NotificationsGateway` (Socket.IO), which authenticates each socket handshake via JWT and joins the client to a `user_<id>` room; `sendToUser`/`sendToAll` push `newNotification` events.
- `MONGO_URI` and Cloudinary credentials (used for maintenance-request photo uploads) live in `src/backend/.env`.

**Frontend** (`src/frontend/app`): Next.js App Router with route groups per role area — `admin/*` and `student/*` — plus `(auth)` for login/forgot-password. Client-side route protection is done with `RoleGuard` (`app/components/RoleGuard.tsx`), which decodes the JWT from `localStorage` via `app/utils/auth.ts` and redirects based on role; there is no server-side auth check on these pages, so treat `RoleGuard` as UX-only, not a security boundary.

- `app/utils/apiClient.ts` is the client for the real NestJS backend, auto-appending `/api` to `NEXT_PUBLIC_API_URL` and attaching the bearer token from `localStorage`.
- `app/api/**` (e.g. `app/api/rooms`) are separate Next.js Route Handlers backed by in-memory mock data (`app/api/rooms/data.ts`) with their own lightweight auth (`_auth.ts` decodes the JWT directly) and access-logging (`_audit.ts`). These are a self-contained mock/demo layer distinct from the real backend — don't assume writes here reach MongoDB, and don't assume the real backend's business rules apply.
- `app/context/SocketContext.tsx` connects to the Socket.IO server; note its fallback URL defaults to port `3000` while `apiClient.ts` defaults to `3001` — when `NEXT_PUBLIC_API_URL` isn't set in `.env.local`, these two clients point at different ports.

## Commands

Backend (`src/backend`):

```bash
npm run start:dev       # nest start --watch, port 3001
npm run build           # nest build
npm run lint            # eslint --fix
npm run test            # jest, unit specs (*.spec.ts) colocated with source
npm run test -- rooms.service.spec.ts   # run a single spec file
npm run test:e2e        # jest against test/jest-e2e.json
npm run format          # prettier --write
```

Frontend (`src/frontend`):
```bash
npm run dev             # next dev
npm run build           # next build
npm run lint            # eslint
```
No test runner is configured in the frontend.

## Language note

Backend/frontend source comments, service error messages (e.g. `ForbiddenException`, `UnauthorizedException` text), and UI copy are frequently written in Vietnamese — this is intentional and consistent with the project's user base, not an inconsistency to "fix".
