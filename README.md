# Contrail

Record Flightradar24 paths that match armed filters, then auto-post a factual
track card from a **dedicated low-trust X account** using the **X Android app**.

This repository is the **public requirements and planning** side of the project
(same split as [UMAssisted](https://github.com/brianreborn/UMAssisted) REQ-P3).
Application source, the web desk, and any companion APK live elsewhere and are
**not** published here.

## What's in this repo

- [`REQUIREMENTS.md`](REQUIREMENTS.md) — source of truth.
- [`docs/1.0-alpha-plan.md`](docs/1.0-alpha-plan.md) — locked alpha slice.
- [`docs/architecture.md`](docs/architecture.md) — layers, X-app sequence, vault.
- [`docs/handoff.md`](docs/handoff.md) — next R&D for the grok bot / Swarm Conductor.
- [`AGENTS.md`](AGENTS.md) — rules for any agent working this repo.

## What Contrail is

1. **Watch** — FR24-shaped filters over a theater (default: Inland Empire).
2. **Harvest** — ident plate, not the globe.
3. **Stitch** — waypoints keyed by ICAO24.
4. **Switch** — X app account list, by dedicated handle, never by slot.
5. **Login only if needed** — identifier then password into OS fields. Overlay
   never shows the password. 2FA falls through.
6. **Outbox** — anything that does not land stays local. Drafts are never dropped.

Landed means the dedicated profile shows the tweet. A composer tap is not enough.

## What this repo is not

- Not the Android overlay APK.
- Not the web desk source.
- Not a place to store X passwords, API keys, FR24 tokens, or recorded tracks.
- Not a 24/7 unattended worker.

## License

See [`LICENSE`](LICENSE).
