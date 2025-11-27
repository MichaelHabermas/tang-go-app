# Tang-Go – Complete System Diagrams
Updated for: tang-go-app starter + npm + Mapbox GL JS

## 1. High-Level Architecture (C4 – Level 2)

```mermaid
flowchart TB
    subgraph External [External Systems]
        S1[tango.info]
        S2[hoy-milonga.com]
        S3[tango-argentino.com]
        S4[Facebook Public Events]
        Org[Organizer Browser]
        Dancer[Dancer Browser / Mobile]
    end

    subgraph Cloudflare [Cloudflare Edge]
        subgraph Workers
            Ingest[Ingest Worker<br/>Cron 6h]
            API[API Worker<br/>Hono + D1]
        end
        Pages[Cloudflare Pages<br/>Vite + React App]
    end

    DB[(D1 SQLite<br/>events, sources, moderation_queue)]

    S1 --> Ingest
    S2 --> Ingest
    S3 --> Ingest
    S4 --> Ingest
    Ingest --> DB
    API --> DB
    Pages --> API
    Org --> Pages
    Dancer --> Pages

    style Cloudflare fill:#ffab00,stroke:#333,color:white
    style DB fill:#0066ff,color:white
```

## 2. Updated Package & Module Boundaries (Single Repo – tang-go-app style)

```mermaid
graph TD
    A[src/client] --> UI[shadcn + lucide]
    A --> Router[TanStack Router]
    A --> Maps[react-map-gl]
    A --> Query[TanStack Query]

    B[src/server] --> Hono[Hono Routes]
    B --> IngestWorker[Ingest Cron]
    B --> DBLib[src/db + Drizzle]

    C[src/lib] --> Types[Event Types + Zod]
    C --> Scrapers[Scrapers Registry]
    C --> Utils[dedup + geocoding]
    C --> MapsLib[Mapbox wrapper]

    Hono --> C
    IngestWorker --> C
    DBLib --> C

    classDef layer fill:#e3f2fd,stroke:#1976d2
    class A,B,C layer
```

## 3. Event Ingestion Pipeline (unchanged logic)

```mermaid
sequenceDiagram
    participant Cron as Cloudflare Cron
    participant Worker as Ingest Worker
    participant Registry as Scraper Registry
    participant S1 as TangoInfoScraper
    participant Dedup as Deduplication
    participant DB as D1

    Cron->>Worker: Every 6h
    Worker->>Registry: runAll()
    loop Each scraper
        Registry->>S1: scrape()
        S1-->>Registry: Raw Events
    end
    Registry-->>Worker: All events
    Worker->>Dedup: removeDuplicates()
    Dedup-->>Worker: Clean events
    Worker->>DB: upsertMany()
```

## 4. Frontend → API Flow with Mapbox

```mermaid
sequenceDiagram
    participant Browser
    participant Pages as Cloudflare Pages
    participant API as Hono Worker
    participant D1

    Browser->>Pages: Load Tang-Go.pages.dev
    Pages->>Browser: React + Mapbox GL JS
    Browser->>API: GET /v1/events?bbox=-58.5,-34.7,-58.3,-34.5&date=2025-12
    API->>D1: SQL with bounding box + filters
    D1-->>API: GeoJSON-compatible events
    API-->>Browser: { events[], total }
    Browser->>Browser: Render clusters + pins (Mapbox)
    Note right of Browser: Supercluster + custom pins
```

## 5. Mapbox GL JS Layer Stack

```mermaid
graph LR
    Map[Mapbox GL Map] --> ClusterLayer[GeoJSON Cluster Layer<br/>supercluster]
    ClusterLayer --> ClusterCount[Cluster count labels]
    ClusterLayer --> Unclustered[Unclustered points<br/>Custom tango shoe icon]
    Unclustered --> Popup[Interactive popup<br/>Event card preview]
    Map --> Sidebar[Event List Sidebar]
    Sidebar --> Selected[Selected event highlight]
    Selected --> Map[FlyTo + pulse animation]
```

## 6. MVP User Journey – Dancer (Mapbox focus)

```mermaid
journey
    title Dancer – Discover Milonga Tonight
    section Discover
        Open Tang-Go.pages.dev: 5: Dancer
        Map auto-zooms to current location: 5: Dancer
        See hundreds of clustered tango pins: 5: Dancer
        Click cluster → map drills down: 5: Dancer
        Click pin → popup with milonga details: 5: Dancer
        Apply "Tonight" + "Milonga" filter: 5: Dancer
        Save to favorites (heart): 4: Dancer
        Share direct link with partner: 5: Dancer
```

## 7. Updated Data Model (ERD)

```mermaid
erDiagram
    events ||--o{ moderation_queue : "pending in"
    events ||--o{ event_sources : "scraped from"
    cities ||--o{ events : "contains"

    events {
        string id PK
        string title
        datetime start_at
        datetime end_at
        string venue_name
        string address
        float lat
        float lng
        string city
        string country
        enum type "class, practica, milonga"
        enum level "all, beginner, intermediate, advanced"
        decimal price_amount
        string currency
        text description
        string source_url
        datetime scraped_at
        boolean verified default false
    }

    moderation_queue {
        string event_id PK
        enum status "pending, approved, rejected"
        string admin_note
        datetime created_at
    }
```

## 8. Development Timeline (Realistic with Starter)

```mermaid
gantt
    title Tang-Go MVP – Realistic Timeline (Starter Boost)
    dateFormat  YYYY-MM-DD
    axisFormat %d/%m

    section Foundations
    Task 0 – Starter + npm + Mapbox       :done, 2025-11-26, 1d

    section Core
    Domain + DB + Repository              :active, 2025-11-27, 3d

    section Ingestion
    Scrapers + Scheduler                  : 2025-11-30, 5d

    section API
    Public + Submit Endpoints             : 2025-12-05, 4d

    section Frontend
    Mapbox + Home + Detail                : 2025-12-09, 6d
    Search + Filters                      : 2025-12-15, 4d
    Favorites + Submit Form               : 2025-12-19, 4d

    section Launch
    Deploy + Monitoring                   :milestone, 2025-12-22
```
