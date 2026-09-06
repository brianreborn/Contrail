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
  labeled screenshots of FR24 **or X app chrome** (no passwords, no live PII
  / tail numbers of private aircraft if avoidable), and planning notes.

## How to change the product

1. Open or update an issue for the requirement or open question.
2. Edit `REQUIREMENTS.md` first. IDs are stable once assigned.
   Gaps become `OQ-*`, not silent assumptions.
3. Keep `docs/1.0-alpha-plan.md` and `docs/architecture.md` in sync when
   scope or mechanism changes.
4. PRs only. Do not push straight to the default branch unless the operator
   said so.

## Voice of the spec

- Overlay never holds data or secrets.
- Globe is canvas; plate is structured.
- Select by identity, never by screen slot — including the X account switcher.
- Kill always wins. Kill skips posting and aborts login.
- Auto-post requires a dedicated low-trust X account. Main handles are out.
- Primary poster is the X Android app. API is desk fallback only.
- Login only if the dedicated handle is missing. Password is SET_TEXT into
  the OS field, never painted, never logged, never screenshotted.
- 2FA and SSO fall through.
- Landed means verified on that profile. Outbox never drops a draft.

## Research work (allowed here)

- FR24 TalkBack / accessibility node inventories (OQ-FR1, OQ-FR2).
- X app switcher / login / tweet_detail node inventories (OQ-X2, OQ-X3, OQ-X4).
- Screenshot + label corpus of chrome only.
- Architecture and sequence diagrams in `docs/`.

See [`docs/handoff.md`](docs/handoff.md) for the current R&D queue.
