# Contrail

Record Flightradar24 paths that match armed filters, then auto-post a factual
track card from a **dedicated low-trust X account**.

This repository is the **public requirements and planning** side of the project
(same split as [UMAssisted](https://github.com/brianreborn/UMAssisted) REQ-P3).
Application source, the web desk, and any companion APK live elsewhere and are
**not** published here.

## What's in this repo

- [`REQUIREMENTS.md`](REQUIREMENTS.md) — source of truth: every requirement, open question, and the reasoning behind each.
- [`docs/1.0-alpha-plan.md`](docs/1.0-alpha-plan.md) — locked alpha slice.
- [`docs/architecture.md`](docs/architecture.md) — layers, outbox, X verify-on-land.
- [`docs/handoff.md`](docs/handoff.md) — next R&D for the grok bot / Swarm Conductor.
- [`AGENTS.md`](AGENTS.md) — rules for any agent working this repo.

## What Contrail is

1. **Watch** — FR24-shaped filters (callsign wildcard, aircraft, squawk, altitude, category) over a theater (default: Inland Empire / KSBD · KONT · KRIV).
2. **Harvest** — the ident plate is the data, not the globe. Globe is a canvas. Select by ICAO24 / registration, never by screen slot.
3. **Stitch** — waypoints keyed by ICAO24. Open on first match of an armed filter; close on gone / kill / explicit close.
4. **Outbox** — anything that cannot be posted, or cannot be verified, stays local. Drafts are never dropped.
5. **Post** — only from a dedicated throwaway / low-trust X account (OAuth 1.0a user tokens + explicit acknowledgement). Kill skips posting.

Landed means **verified**: `POST /2/tweets` is not enough. Contrail then `GET /2/tweets/:id`. If a tweet id already exists, retry must not create a second tweet.

## What this repo is not

- Not the Android overlay APK.
- Not the web desk source.
- Not a place to store X API keys, FR24 tokens, or recorded tracks.
- Not a 24/7 unattended worker.

## License

See [`LICENSE`](LICENSE).
