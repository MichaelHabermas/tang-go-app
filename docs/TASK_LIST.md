# TANG-GO MASTER TASKLIST – npm + Mapbox GL JS Edition
Based on: https://github.com/MichaelHabermas/tang-go-app
All commands now use npm

## TASK 0 – Project Setup & Tooling (Start Here)
- [ ] `git clone https://github.com/MichaelHabermas/tang-go-app TANG-GO && cd TANG-GO`
- [ ] Delete pnpm-lock.yaml + any pnpm references
- [ ] `npm install`
- [ ] Verify starter works: `npm run dev` (Vite + Worker running locally)
- [ ] Add ESLint + Prettier extensions if missing (use existing config as base)
- [ ] Add Husky + lint-staged (pre-commit: lint + format + typecheck)
- [ ] Extend .github/workflows (add tsc --noEmit + vitest if missing)
- [ ] Add .env.example with MAPBOX_TOKEN placeholder
- [ ] Update README: replace all pnpm → npm commands
- [ ] Commit: "chore: initialize TANG-GO from tang-go-app starter (npm + Mapbox ready)"
- [ ] PR #1 → merge when dev server + lint passes

## EPIC 1 – DOMAIN & DATABASE
### PR #2 – Event Types & Zod Schemas
- [ ] Create src/lib/types/event.ts (full Event interface + enums)
- [ ] Add Zod schemas: createEventSchema, publicEventSchema
- [ ] Add utils: generateId(), slugify(), normalizeAddress()
- [ ] Commit: "feat: event domain model + zod validation"

### PR #3 – D1 Schema & Migrations (Drizzle)
- [ ] Install drizzle-orm + drizzle-kit
- [ ] Create src/db/schema.ts (events, sources, moderation_queue, cities)
- [ ] Add drizzle.config.ts + migrations folder
- [ ] Generate + run migration locally
- [ ] Add seed script (10 fake events)
- [ ] Commit: "feat: D1 schema + migrations + seed"

### PR #4 – Repository Layer
- [ ] src/lib/repositories/IEventRepository.ts
- [ ] src/lib/repositories/D1EventRepository.ts (CRUD + search methods)
- [ ] Add Vitest tests (in-memory D1)
- [ ] Commit: "feat: event repository abstraction"

## EPIC 2 – EVENT INGESTION ENGINE
### PR #5 – Scraper Framework
- [ ] src/lib/scrapers/IEventScraper.ts + BaseScraper abstract class
- [ ] src/lib/scrapers/registry.ts (auto-register all scrapers)
- [ ] Commit: "feat: pluggable scraper architecture"

### PR #6 – First Two Scrapers
- [ ] Implement TangoInfoScraper + HoyMilongaScraper
- [ ] Mock HTML tests (vitest + happy-dom)
- [ ] Commit: "feat: tango.info + hoy-milonga scrapers"

### PR #7 – Additional Scrapers
- [ ] tango-argentino.com scraper
- [ ] Facebook public events (RSS/unofficial)
- [ ] Commit: "feat: +2 additional scrapers"

### PR #8 – Scheduler + Deduplication Worker
- [ ] Extend src/server/ingest.ts (cron-triggered)
- [ ] Add fuzzy deduplication (Jaro-Winkler + venue + date)
- [ ] Error handling + retry logic
- [ ] Commit: "feat: ingestion worker with deduplication"
- [ ] Manual test: trigger worker, verify new events in DB

## EPIC 3 – API LAYER (Hono)
### PR #9 – API Skeleton
- [ ] Extend src/server/index.ts: CORS, rate limiting, error handler
- [ ] Health endpoint /health
- [ ] Commit: "feat: enhanced Hono API base"

### PR #10 – Public Search Endpoints
- [ ] GET /v1/events (date range, bbox, type, level, city, pagination)
- [ ] GET /v1/events/:id
- [ ] GET /v1/cities (autocomplete)
- [ ] Add geo bounding box queries in D1
- [ ] Commit: "feat: public event search API"

### PR #11 – Organizer Submission
- [ ] POST /v1/events/submit (public, Zod-validated)
- [ ] Insert into moderation_queue table
- [ ] Optional: Resend email notification
- [ ] Commit: "feat: organizer event submission"

## EPIC 4 – FRONTEND + MAPBOX GL JS
### PR #12 – React App Foundation
- [ ] Install shadcn-ui, @radix-ui, tailwind-animate
- [ ] Install react-map-gl + mapbox-gl
- [ ] Setup TanStack Router + dark mode
- [ ] Global layout + mobile-responsive shell
- [ ] Commit: "feat: enhanced React app with shadcn + router"

### PR #13 – Mapbox GL Integration
- [ ] Create src/lib/maps/Map.tsx (react-map-gl wrapper)
- [ ] Add clustering (supercluster + Mapbox)
- [ ] Custom pin component + popup
- [ ] Viewport sync with URL
- [ ] Commit: "feat: Mapbox GL map with clustering"

### PR #14 – Home Page + Event List
- [ ] Search bar + filter button
- [ ] Infinite scroll event list (TanStack Query)
- [ ] Responsive grid: map + list side-by-side (mobile: tabs)
- [ ] Commit: "feat: home page with map + list"

### PR #15 – Event Detail Modal
- [ ] Full detail view (all fields, source link, report button)
- [ ] Share + copy link
- [ ] Commit: "feat: event detail modal"

## EPIC 5 – SEARCH & FILTERS
### PR #16 – Filter UI
- [ ] Filter sidebar/drawer (date range, type, level, price)
- [ ] react-date-range picker
- [ ] URL state sync (TanStack Router)
- [ ] Commit: "feat: advanced filter UI"

### PR #17 – Backend Search Optimization
- [ ] Add full-text search index on title/venue/city
- [ ] Optimize combined queries (filters + bbox + FTS)
- [ ] Commit: "feat: optimized search backend"

## EPIC 6 – USER TOOLS MVP
### PR #18 – Favorites (LocalStorage)
- [ ] Heart button + favorites page (/my/favorites)
- [ ] LocalStorage persistence
- [ ] Commit: "feat: local favorites"

### PR #19 – Organizer Submission Form
- [ ] /submit-event multi-step form
- [ ] Address → Nominatim geocoding
- [ ] Live preview card
- [ ] Success + copy
- [ ] Commit: "feat: organizer submission form"

### PR #20 – UI Feedback
- [ ] Install sonner (toast)
- [ ] Error boundaries + loading states
- [ ] Commit: "feat: toast notifications + polish"

## EPIC 7 – DEPLOYMENT & MONITORING
### PR #21 – Production Deploy
- [ ] Update wrangler.toml (D1 binding + cron triggers)
- [ ] Extend GitHub Actions for full deploy (Pages + Workers)
- [ ] Add MAPBOX_TOKEN to CF secrets
- [ ] Commit: "chore: production-ready deploy"

### PR #22 – Monitoring
- [ ] Add Sentry (frontend + worker)
- [ ] Add Umami or Plausible analytics
- [ ] Commit: "feat: error monitoring + analytics"

## MVP COMPLETE WHEN ALL BOXES ARE TICKED
Deploy → https://TANG-GO.pages.dev (or your domain)
