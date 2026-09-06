# Handoff — grok bot / Swarm Conductor

This is the R&D queue after the 1.0 alpha desk design landed (2026-09-05).
Work the public repo as **docs and research**. Implementation goes to a
**private** repository (REQ-P3), same split as UMAssisted.

## Operator intent (locked)

- Source of truth for aircraft is Flightradar24 (Android app + user account
  with custom filters). Web widgets / official API are optional, not the
  identity of the product.
- Capture path is an accessibility overlay + processing layer, inspired by
  UMAssisted (screen classifier, identity selection, kill, arm-before-motion,
  corpus of labeled screenshots, REQ-P3 public docs / private APK).
- Auto-post is required. Operator provides a **dedicated low-trust X
  account**. Main handles are forbidden.
- Anything that does not land on X stays local. A post is not done until
  lookup confirms it exists.

## Do next (in order)

### 1. Private implementation repo

Create `Contrail-private` (or whatever the operator names it). Copy the alpha
plan. Do not put source in this public repo.

Alpha desk already prototyped in the Grok app-builder sandbox:

- Zustand persist: filters, recordings, outbox, settings
- OpenSky bbox + demo injector
- Outbox processor → `publishTweet` server function (OAuth 1.0a, then GET)
- Dedicated-account gate in Settings

Port that behavior; do not paste secrets.

### 2. Research FR24 accessibility (OQ-FR1, OQ-FR2)

On a physical device with FR24 logged in and a custom filter armed:

- Dump the accessibility node tree on the map, on a selected aircraft, and on
  the ident panel.
- Record whether filtered aircraft are TalkBack-exposed markers or a blind
  WebGL globe.
- Capture resource-ids / content-descriptions for: callsign, registration,
  ICAO24, lat, lon, altitude, ground speed, track, squawk, origin, dest.
- Store labeled screenshots under a `screenshots/` tree here (panel chrome
  only). Follow UMAssisted `CAPTURE_GUIDE.md` discipline: PNG + `.labels.txt`.

Deliverable: close or split OQ-FR1 / OQ-FR2 with evidence.

### 3. Companion APK spike (private repo)

Package-scoped AccessibilityService. Overlay chrome only. One follow identity.
Kill. Arm. No tweets from the APK. Fall through below parse confidence.

### 4. X quota (OQ-X1)

Document the operator’s developer-app tier (Free / Basic) and the write
limit. Outbox already holds overflow; do not invent a second poster.

### 5. Path-card media (optional, after text posts work)

Chunked media upload of a generated trail PNG. Not alpha-blocking.

## Issue seeds

Open these on this repo if they do not already exist:

- `OQ-FR1` TalkBack markers vs canvas
- `OQ-FR2` panel resource-ids on current FR24
- `OQ-FR3` `takeScreenshot()` / FLAG_SECURE
- `OQ-FR4` KML / playback close-out from the panel
- `OQ-X1` write quota
- `REQ-F5/F6/F7` dedicated account + outbox + verify-on-land (tracking)

## What not to do

- Do not automate FR24 login chrome.
- Do not store X keys in this repo, in Actions, or in a world-writable DB.
- Do not tweet from a personal handle “just to test”.
- Do not treat POST-without-GET as success.
- Do not re-POST when a tweet id is already on the outbox item.
