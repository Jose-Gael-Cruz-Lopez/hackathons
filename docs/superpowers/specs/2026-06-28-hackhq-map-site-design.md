# HackHQ Interactive Globe Site — Design

Date: 2026-06-28
Status: Approved (design)

## Goal
A website for HackHQ: an interactive 3D globe of hackathons worldwide, fed by
the repo's data, deployed on Cloudflare. Site name matches the repo (`hackhq`).

## Decisions
- **Frontend:** fresh Vite + React + TypeScript SPA (`site/`), Tailwind. → Cloudflare Pages.
- **Map:** 3D interactive globe via `react-globe.gl` (Three.js).
- **Backend:** Supabase Postgres, read-only public via anon key + RLS.
- **Data flow:** `listings.json` (repo = source of truth) → GitHub Action geocodes →
  upserts into Supabase → SPA reads Supabase.
- **Submissions:** site button deep-links to the prefilled GitHub issue template
  (no site-side write backend in v1).

## Architecture
```
listings.json --(GitHub Action: on change + weekly cron)--> geocode + cache
   --> upsert --> Supabase `hackathons` (RLS read-only)
   --> Supabase JS (browser) --> Vite/React 3D globe --> Cloudflare Pages
   "Submit" button --> prefilled GitHub issue
```

## Backend
- **Supabase table `hackathons`** mirrors listings.json fields
  (id, host, title, url, locations, format, prize, state, dates) plus
  `lat`, `lng`, `geo_status` (`geocoded` | `virtual` | `failed`).
- **RLS:** anon can `select` only; writes via the Action's service-role key.
- **Geocoding:** free Nominatim/OSM, results cached in committed
  `.github/scripts/geocode_cache.json` (each unique location geocoded once).
  `Online`/`Virtual` locations → no coords, `geo_status = virtual`.
- **Sync Action:** geocodes new locations, upserts all rows into Supabase using a
  `SUPABASE_SERVICE_KEY` GitHub secret.

## Frontend (Vite + React + TS + Tailwind)
- 3D globe with status-colored markers (open=green, opens_soon=amber), auto-rotate, zoom.
- Hover tooltip; click marker → detail card (name, host, date, prize, Register link).
- Search + filters (status/format); a `Virtual` side panel lists online events
  (no globe location).
- Submit CTA → prefilled GitHub issue. HackHQ branding.

## Deployment
Cloudflare Pages builds `site/`; Supabase anon key as build env var; custom domain later.

## Out of scope (v1, YAGNI)
Site-side auth/accounts; write-to-Supabase form; clustering virtual events on the globe.
