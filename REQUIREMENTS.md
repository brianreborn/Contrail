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
- **Primary poster is the X Android app** (`com.twitter.android`): switch to the dedicated handle, log in only if that handle is missing, compose, post, verify on that profile
- Local outbox for anything that does not land
- Overlay chrome: arm status, follow, kill, X session (handle only — never the password)
- Kill always wins. No arm, no motion.

**1.0 beta**
- Android AccessibilityService companion, allowlisted to FR24 **and** X
- Sweep mode
- KML / playback close-out
- Optional official FR24 API token

**Out of scope for 1.0**
- Play Store
- Unattended 24/7 worker
- Tweeting from a personal / high-trust X account
- Reimplementing FR24’s globe
- Automating X 2FA / SSO (Google, Apple). Those fall through to the operator.

## 3. Product

- **REQ-P1 — Name is Contrail.**
- **REQ-P3 — Desk is public docs + preview; companion APK is private sideload.** Same split as UMAssisted.

## 4. Platform

- **REQ-PL1 — This web desk** is the processing / library / compose / outbox / vault surface.
- **REQ-PL2 — Companion APK** is an AccessibilityService on API 30+. Package allowlist: `com.flightradar24free` and `com.twitter.android` only.

## 5. Mechanism

- **REQ-M1 — Overlay never holds data or secrets.** Status, kill, follow, current X handle only.
- **REQ-M2 — Globe is canvas.** Do not parse pixels for coordinates.
- **REQ-M3 — Panel is structured.** Latitude, longitude, altitude, gs, track, squawk, ICAO24, registration.
- **REQ-M4 — Select by identity** (ICAO24 / registration / X handle), never by screen slot.
- **REQ-M5 — Fall through** below parse confidence. Never best-guess.
- **REQ-M6 — Stitch on ICAO24.** Open on first match of an armed filter; close on gone / kill / explicit close.
- **REQ-M7 — X account switcher is identity-based.** Match the dedicated handle in the in-app account list. Never tap “the second row.” If the handle is not listed, add-account then login — do not pick a neighbor.

## 6. Functional

- **REQ-F1 — Default theater** is Inland Empire (KSBD / KONT / KRIV).
- **REQ-F2 — Tweet is factual**, ATC-style, under 280. Filter name + fix count allowed.
- **REQ-F3 — Manual fallback** is the X intent URL + copy. Not the happy path.
- **REQ-F4 — Post-on-close** is per-filter, default on for rare/interesting rules, off for census.
- **REQ-F5 — Auto-post requires a dedicated low-trust X account.** Handle + login identifier + password, plus an explicit acknowledgement that the account is not the operator’s main handle. No vault, no ack → posts stay in the local outbox.
- **REQ-F6 — Local outbox.** Anything that cannot be posted, or cannot be verified, is stored on-device and retried. Drafts are never dropped.
- **REQ-F7 — Landed means verified on that account.** After the X app posts, Contrail reads the dedicated profile / latest status. Matching text (and status id when available) is required. A composer tap without a profile match is `unverified`. If a tweet id exists, retry must not create a second tweet.
- **REQ-F8 — Kill skips posting** and aborts an in-flight X login / switch.
- **REQ-F9 — Primary post path is the X application.** Sequence: ensure session → open switcher → tap dedicated handle → else add-account / login → open composer → set draft → Post → open own profile → confirm. API keys are desk-only fallback when the X-app vault is empty, never the preferred path, never used by the companion.
- **REQ-F10 — Login only if needed.** If the dedicated handle is already in the X app account list, only switch. If the X app is already on that handle, skip switch. Inject credentials only on the login screens, and only for the dedicated account.

## 7. Safety

- **REQ-SF1 — Kill** disarms every filter, closes open recordings, aborts the X sequence.
- **REQ-SF2 — FR24 login / paywall chrome is never tapped.** X login is an allowlisted named sequence for the dedicated account only. SSO buttons (Google, Apple) are a no-op.
- **REQ-SF3 — Foreign UI is a no-op.** Packages outside the allowlist are ignored.
- **REQ-SF4 — X credentials** live in the processing-layer vault (localStorage on the desk; Android Keystore / EncryptedSharedPreferences on the APK). Never in a world-writable database, never in git, never logged, never copied to the overlay, never included in screenshot captures.
- **REQ-SF5 — Overlay never tweets.** Companion may drive the X app composer as the dedicated account. The vault injects identifier then password via `ACTION_SET_TEXT` on the OS password field. TalkBack custom announcements must not speak the password. Login screens are screenshot-forbidden.
- **REQ-SF6 — 2FA falls through.** Contrail does not store or type one-time codes. The operator completes 2FA; the sequence resumes once the dedicated handle is current.

## 9. Open questions

- **OQ-FR1** — Are filtered FR24 aircraft TalkBack-exposed markers?
- **OQ-FR2** — Exact Android panel labels / resource-ids on current FR24.
- **OQ-FR3** — `takeScreenshot()` of the FR24 window (FLAG_SECURE?).
- **OQ-FR4** — KML export reachable from the panel flow?
- **OQ-FR5** — Named `*.grok.me` host is assigned by the builder deployer, not this workspace.
- **OQ-X1** — Current X API write quota on the operator’s developer app (Free vs Basic). Desk fallback only.
- **OQ-X2** — Exact X Android account-switcher nodes (handle text, resource-ids) on current `com.twitter.android`.
- **OQ-X3** — Exact X Android login field ids (identifier, password, Log in). Confirm password field does not echo into accessibility events Contrail logs.
- **OQ-X4** — After Post, is the new status id exposed on tweet_detail / profile nodes for verify-on-land?
