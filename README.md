# Backend Scrapper

A Go backend service for lead discovery and outreach automation. It scrapes
target sources, normalises the results into a lead database, and exposes a REST
API for querying leads and tracking outreach activity.

## What it does

- **Scraping pipeline** — a standalone worker (`cmd/scrapper`) collects data and
  writes normalised records through a database abstraction layer.
- **REST API** — separate handlers for analytics, leads and outreach, mounted
  through a single router with shared middleware.
- **Auth middleware** — request authentication is applied centrally rather than
  per-handler, so new routes inherit it by default.
- **Pluggable storage** — persistence sits behind an interface, with a Supabase
  implementation provided. Swapping backends means implementing the interface,
  not editing handlers.
- **Containerised** — `docker-compose.yml` brings up the service and its
  dependencies together.

## Stack

Go · Supabase (PostgreSQL) · Docker Compose · Makefile-driven tasks

## Layout

```
cmd/scrapper/        worker entrypoint
api/handlers/        analytics, lead, outreach handlers
api/middleware/      auth, CORS, request logging
api/routes/          route registration
internal/config/     configuration loading
internal/database/   storage interface + Supabase implementation
```

## Running

Copy `.env.example` to `.env`, fill in the database credentials, then:

```bash
make run          # start the API
docker compose up # or run the full stack in containers
```

## Notes

Scrapers are inherently brittle: upstream markup changes break selectors. The
database abstraction and handler separation exist so that fixing a source does
not ripple into the API layer.
