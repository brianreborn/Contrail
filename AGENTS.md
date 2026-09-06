# Agent rules — Contrail (public docs)

You are working the **documentation-only** repository. Treat this file as
binding, same priority as `REQUIREMENTS.md`.

## Hard boundaries (REQ-P3)

- Do **not** add application source: no `src/`, no Android project, no
  `AndroidManifest.xml`, no `AccessibilityService`, no APK, no Vite/TanStack
  app, no server functions that post to X.
- Do **not** commit credentials, tokens, cookies, recorded tracks, or outbox
  payloads.
- The private implementation (web desk + companion APK) lives in a separate
  non-public repository. Scaffold it there, never here.
- Public repo may hold: `REQUIREMENTS.md`, `docs/`, this file, LICENSE,
  labeled screenshots of FR24 **UI chrome** (no live PII / tail numbers of
  private aircraft if avoidable), and planning notes.

## How to change the product

1. Open or update an issue for the requirement or open question.
2. Edit `REQUIREMENTS.md` first. IDs are stable once assigned.
   Gaps become `OQ-*`, not silent assumptions.
3. Keep `docs/1.0-alpha-plan.md` and `docs/architecture.md` in sync when
   scope or mechanism changes.
4. PRs only. Do not push straight to the default branch unless the operator
   said so.

## Voice of the spec

- Overlay never holds data.
- Globe is canvas; plate is structured.
- Select by identity, never by screen slot.
- Kill always wins. Kill skips posting.
- Auto-post requires a dedicated low-trust X account. Main handles are out.
- Landed means verified by lookup. Outbox never drops a draft.
- Companion APK never tweets. Only the desk outbox talks to X.

## Research work (allowed here)

- FR24 TalkBack / accessibility node inventories (OQ-FR1, OQ-FR2).
- Screenshot + label corpus of the FR24 **panel** fields (lat, lon, alt, gs,
  track, squawk, ICAO24, registration).
- X API write-quota notes (OQ-X1). Keep them factual; no keys.
- Architecture and sequence diagrams in `docs/`.

See [`docs/handoff.md`](docs/handoff.md) for the current R&D queue.
