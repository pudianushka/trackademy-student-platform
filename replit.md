# Trackademy

An AI-powered student study management platform where students can upload notes, scan handwritten notes, organize folders, and prepare smarter using AI tools.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/trackademy run dev` — run the frontend (port 24698)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string, `SESSION_SECRET` — JWT signing key

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite, Tailwind CSS, Framer Motion, Wouter, shadcn/ui
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- Auth: JWT (jsonwebtoken + bcrypt)
- File uploads: Multer (stored in `artifacts/api-server/uploads/`)
- OCR: Tesseract.js
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — API contract (source of truth)
- `lib/db/src/schema/` — Drizzle tables: users, folders, notes
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/api-server/src/middlewares/auth.ts` — JWT middleware
- `artifacts/trackademy/src/` — React frontend
- `artifacts/api-server/uploads/` — Uploaded files (local disk)

## Architecture decisions

- JWT stored in localStorage as `trackademy_token`; injected via custom fetch in api-client-react
- File uploads use raw multipart/form-data fetch (not generated hooks) — binary uploads don't work with Orval-generated hooks
- OCR runs server-side via Tesseract.js (no external API needed)
- AI assistant uses a rule-based responder as a placeholder (can be swapped for OpenAI)
- Files served at `/api/notes/file/:filename` directly from the Express server

## Product

- Landing page with hero section, features, about, and footer
- JWT-based signup/login with protected routes
- Dashboard with stats (total notes, folders, scanned notes, storage)
- Folder management (create, rename, delete) with note counts
- Notes list filterable by folder with delete support
- Drag-and-drop file upload (PDF, DOCX, TXT, images)
- OCR scan of handwritten image notes using Tesseract.js
- AI chat assistant for study help and tips

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Always run `pnpm run typecheck:libs` after editing schema files before typechecking api-server
- After OpenAPI spec changes, run codegen before building frontend
- Upload files are stored locally — they won't persist across Replit container restarts unless object storage is added
- Tesseract.js downloads language models on first scan (may be slow first time)

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
