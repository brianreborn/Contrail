# Contrail — Requirements

Living draft. IDs are stable once assigned. Gaps become open questions (REQ-OQ), not silent assumptions. Process copied from UMAssisted.

## 1. Problem

Flightradar24 already has the filters. The work is: notice a match, keep the path, put a factual post on X. Tapping each aircraft by hand does not scale.

## 2. Scope

**1.0 alpha (this desk)**
- FR24-shaped filter model (callsign wildcard, aircraft, squawk, altitude, category)
- Armed watch over a theater (Inland Empire / SoCal)
- Ident plate as the data harvest (select → panel)
- Stitch waypoints by ICAO24
- Dedicated low-trust X account required for auto-post
- Local outbox for anything that does not land
- Lookup-verify before calling a post successful
- Overlay chrome: arm status, follow, kill, X status
- Kill always wins. No arm, no motion.

**1.0 beta**
- Android AccessibilityService companion, package-scoped to FR24
- Sweep mode
- KML / playback close-out
- Optional official FR24 API token

**Out of scope for 1.0**
- Play Store
- Unattended 24/7 worker
- Tweeting from a personal / high-trust X account
- Reimplementing FR24’s globe

## 3. Product

- **REQ-P1 — Name is Contrail.**
- **REQ-P3 — Desk is public; companion APK is private sideload.** Same split as UMAssisted.

## 4. Platform

- **REQ-PL1 — This web desk** is the processing / library / compose / outbox surface.
- **REQ-PL2 — Companion APK** is an AccessibilityService on API 30+, package-scoped to `com.flightradar24free`.

## 5. Mechanism

- **REQ-M1 — Overlay never holds data.** Status, kill, follow only.
- **REQ-M2 — Globe is canvas.** Do not parse pixels for coordinates.
- **REQ-M3 — Panel is structured.** Latitude, longitude, altitude, gs, track, squawk, ICAO24, registration.
- **REQ-M4 — Select by identity** (ICAO24 / registration), never by screen slot.
- **REQ-M5 — Fall through** below parse confidence. Never best-guess.
- **REQ-M6 — Stitch on ICAO24.** Open on first match of an armed filter; close on gone / kill / explicit close.

## 6. Functional

- **REQ-F1 — Default theater** is Inland Empire (KSBD / KONT / KRIV).
- **REQ-F2 — Tweet is factual**, ATC-style, under 280. Filter name + fix count allowed.
- **REQ-F3 — Manual fallback** is the X intent URL + copy. Not the happy path.
- **REQ-F4 — Post-on-close** is per-filter, default on for rare/interesting rules, off for census.
- **REQ-F5 — Auto-post requires a dedicated low-trust X account.** Four OAuth 1.0a user-context keys plus an explicit acknowledgement that the account is not the operator’s main handle. No keys, no ack → posts stay in the local outbox.
- **REQ-F6 — Local outbox.** Anything that cannot be posted, or cannot be verified, is stored on-device and retried. Drafts are never dropped.
- **REQ-F7 — Landed means verified.** POST `/2/tweets` is not enough. Contrail then GET `/2/tweets/:id`. Only a matching id counts as posted. If a tweet id exists, retry must not create a second tweet — lookup only.
- **REQ-F8 — Kill skips posting.** Abort is not publish.

## 7. Safety

- **REQ-SF1 — Kill** disarms every filter and closes open recordings.
- **REQ-SF2 — No login / paywall chrome** is ever tapped by the companion.
- **REQ-SF3 — Foreign UI is a no-op.**
- **REQ-SF4 — X credentials** live only in localStorage. Never in a world-writable database, never in git, never logged.
- **REQ-SF5 — Companion never tweets.** Only the desk outbox talks to X.

## 9. Open questions

- **OQ-FR1** — Are filtered FR24 aircraft TalkBack-exposed markers?
- **OQ-FR2** — Exact Android panel labels / resource-ids on current FR24.
- **OQ-FR3** — `takeScreenshot()` of the FR24 window (FLAG_SECURE?).
- **OQ-FR4** — KML export reachable from the panel flow?
- **OQ-FR5** — Named `*.grok.me` host is assigned by the builder deployer, not this workspace.
- **OQ-X1** — Current X API write quota on the operator’s developer app (Free vs Basic).
