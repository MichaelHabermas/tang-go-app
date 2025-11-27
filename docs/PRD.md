# Tang-Go PRD 
**Based on Starter: MichaelHabermas/tang-go-app**  
**Date: November 26, 2025**  
**Changes: pnpm → npm; Maps: Leaflet → Mapbox GL JS (via react-map-gl) for better clustering, customization, global event handling.**  
**Target: Developers, LLMs, Cursor IDE**  

## 1. Overview & Integration with Starter
Tang-Go: Global tango events directory via scraping, search, maps, submissions.  

Fork `MichaelHabermas/tang-go-app`: React + Vite (FE), Hono (BE on CF Workers), D1 DB. Leverage CI/CD (GitHub Actions: lint/test/deploy). Extend structure for modularity.

## 2. Problem Statement
Scattered tango events. Solution: Real-time platform with scraping, map viz, filters.

## 3. Target Audience
- Dancers (search/save).  
- Organizers (submit/list).  

## 4. Success Metrics
- 500 DAU/90 days.  
- 80% city coverage, 90% accuracy.  
- >3min sessions.  

## 5. S.O.L.I.D Principles
Single resp., open/closed (scraper interfaces), Liskov (sources), interface seg., dep. inversion (`/src/lib` abstractions).

## 6. Project Structure (Extend Starter)
Base: `/src` (app), `package.json` (Vite/React/Hono), `wrangler.toml`. Add:  
```
/src  
  /client          ← React components  
  /server          ← Hono routes  
  /lib             ← Types, utils, scrapers, maps  
  /db              ← D1 schema  
/lib (root)  
  /scrapers        ← IEventScraper  
  /maps            ← react-map-gl wrapper  
README.md, .github/workflows (extend CI)  
```

Tech Stack:  
- FE: Vite, React, Tailwind v4, Shadcn, TS, ContextAPI, Lucide-react, react-map-gl (Mapbox GL JS).  
- BE: Hono Workers, D1.  
- Extras: Drizzle ORM, Zod, Vitest.  
- CI/CD: GitHub Actions (lint/test/deploy).  
- Package Mgr: npm (replace pnpm).  

## 7. Assumptions & Constraints
- CI/CD extends for tests.  
- MVP: 24-48h via boilerplate.  
- Sources reliable; initial users: dancers/organizers.  

## 8. Phased Epics (Fork Starter, Sequential, Test-Per-Epic)
Start: `git clone https://github.com/MichaelHabermas/tang-go-app Tang-Go && cd Tang-Go && npm i` (update README). Use `npm run dev`.

#### Task 0 – Foundations (Extend Starter Tooling)
**PR #1:**  
- [ ] Run setup: `npm i`, verify `npm run dev`.  
- [ ] Extend ESLint/Prettier (React/Hono rules).  
- [ ] Add Husky/lint-staged if absent.  
- [ ] Extend workflows: type-check, e2e.  
- [ ] Cursor rules: S.O.L.I.D in `/src/lib`.  
- [ ] Update README: npm commands.  
- [ ] Commit: "refactor: init Tang-Go on starter w/ npm".  
- [ ] Merge; test: Lint/dev runs.

#### Epic 1 – Domain & DB (PR #2–#4)
Extend D1.  
**PR #2 – Types/Zod:**  
- [ ] `/src/lib/types/event.ts`: Event + enums.  
- [ ] Zod schemas.  
- [ ] Commit: "feat: event models".  

**PR #3 – Schema/Migrations:**  
- [ ] `/src/db/schema.ts`: Drizzle (events/sources/queue).  
- [ ] Migrations/seed (10 fakes).  
- [ ] Commit: "feat: D1 schema".  

**PR #4 – Repo Layer:**  
- [ ] IEventRepository.  
- [ ] D1 impl (CRUD/search).  
- [ ] Vitest tests.  
- [ ] Commit: "feat: event repo". Pause: Local DB query.

#### Epic 2 – Ingestion (PR #5–#8)
Workers for cron.  
**PR #5 – Scraper Arch:**  
- [ ] `/src/lib/scrapers/interfaces.ts`: IEventScraper + Base.  
- [ ] Registry.  
- [ ] Commit: "feat: scraper framework".  

**PR #6 – Core Scrapers:**  
- [ ] tango.info, hoy-milonga.com.  
- [ ] Mock tests.  
- [ ] Commit: "feat: initial scrapers".  

**PR #7 – More Sources:**  
- [ ] tango-argentino.com, FB RSS.  
- [ ] Commit: "feat: +2 scrapers".  

**PR #8 – Scheduler/Dedup:**  
- [ ] Ingest worker: Cron 6h, fuzzy dedup.  
- [ ] DB upsert.  
- [ ] Commit: "feat: ingestion". Pause: Manual trigger/DB check.

#### Epic 3 – API (PR #9–#11)
Extend Hono.  
**PR #9 – Base API:**  
- [ ] `/src/server/index.ts`: CORS/rate-limit.  
- [ ] Health /v1/events.  
- [ ] Commit: "feat: Hono skeleton".  

**PR #10 – Search Endpoints:**  
- [ ] GET /v1/events (filters/paginate).  
- [ ] /:id, /cities; geo bbox.  
- [ ] Commit: "feat: search API".  

**PR #11 – Submission:**  
- [ ] POST /v1/events (Zod/queue).  
- [ ] Moderation.  
- [ ] Commit: "feat: submit". Pause: Curl tests.

#### Epic 4 – Frontend + Map (PR #12–#15)
Build on React/Vite; use react-map-gl for Mapbox.  
**PR #12 – Skeleton:**  
- [ ] `/src/client`: Shadcn, TanStack Router, dark mode.  
- [ ] Layout.  
- [ ] Commit: "feat: React base".  

**PR #13 – Map:**  
- [ ] `/src/lib/maps`: react-map-gl + clustering.  
- [ ] Global pins (Mapbox token env).  
- [ ] Commit: "feat: Mapbox map".  

**PR #14 – Home/List:**  
- [ ] Search bar, infinite list, cards.  
- [ ] API fetch.  
- [ ] Commit: "feat: home page".  

**PR #15 – Details:**  
- [ ] Modal (info/report/share).  
- [ ] Commit: "feat: event detail". Pause: CI deploy/map view.

#### Epic 5 – Search/Filters (PR #16–#17)
**PR #16 – UI:**  
- [ ] Date picker, sidebar, URL sync.  
- [ ] Commit: "feat: search UI".  

**PR #17 – Backend Enh:**  
- [ ] D1 FTS/combined queries.  
- [ ] Commit: "feat: advanced search". Pause: Filter tests.

#### Epic 6 – User Tools (PR #18–#20)
**PR #18 – Favorites:**  
- [ ] LocalStorage, /favorites.  
- [ ] Commit: "feat: favorites".  

**PR #19 – Submit Form:**  
- [ ] Multi-step, geocoding, preview.  
- [ ] Commit: "feat: submit form".  

**PR #20 – Feedback:**  
- [ ] Sonner toasts/errors.  
- [ ] Commit: "feat: notifications". Pause: E2E.

#### Epic 7 – Deploy/Monitor (PR #21–#22)
**PR #21 – CI/CD Extend:**  
- [ ] Workflows: Scrape tests, CF deploy.  
- [ ] Bindings.  
- [ ] Commit: "chore: deploy".  

**PR #22 – Monitoring:**  
- [ ] Sentry/Umami.  
- [ ] Commit: "feat: analytics".  

## 9. MVP Criteria
- 5+ scrapers, 1k events.  
- Search/map/submit.  
- CI green, CF deploy.  

## 10. User Flows (Mermaid – See DIAGRAMS.md)
Retain; update map refs to Mapbox.

## 11. Workflow
Per-epic: Test/merge via CI, pause. Use `npm run build`. Begin Task 0.
