# Architecture — Contrail

## Layers

```
┌─────────────────────────────────────────────────────────┐
│ Overlay chrome                                          │
│ arm · follow · kill · X ready / outbox count            │
│ NEVER holds track data                                  │
└─────────────────────────────────────────────────────────┘
            │ select by ICAO24 / registration
            ▼
┌─────────────────────────────────────────────────────────┐
│ Harvest                                                 │
│ ident plate (structured)                                │
│ lat lon alt gs track squawk ICAO24 registration         │
│ globe is canvas — do not OCR coordinates off the map    │
└─────────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────┐
│ Processing                                              │
│ stitch waypoints by ICAO24                              │
│ open on armed-filter match · close on gone/kill/close   │
└─────────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────┐
│ Outbox (local, durable)                                 │
│ queued → posting → posted | unverified | failed         │
│ drafts never dropped                                    │
└─────────────────────────────────────────────────────────┘
            │ POST /2/tweets then GET /2/tweets/:id
            ▼
┌─────────────────────────────────────────────────────────┐
│ Dedicated low-trust X account                           │
│ OAuth 1.0a user context · not the operator’s main       │
└─────────────────────────────────────────────────────────┘
```

The Android companion (beta) implements overlay + harvest against FR24.
The desk implements processing + outbox + X. The companion **never tweets**
(REQ-SF5).

## Outbox state machine

```
                 postOnClose && close (not kill)
                                 │
                                 ▼
                              queued
                                 │
                    dedicated account ready?
                      no │            │ yes
                         ▼            ▼
                   hold locally     posting
                         │            │
                         │     POST /2/tweets
                         │      ok │     │ fail
                         │         ▼     ▼
                         │    unverified failed
                         │         │
                         │   GET /2/tweets/:id
                         │    match │   no match
                         │         ▼       │
                         │      posted     │
                         │                 ▼
                         └──────────── retry
                              (lookup only if tweet id exists)
```

Rules:

- **Kill skips posting.** Abort is not publish (REQ-F8).
- **Census filters** (`postOnClose: false`) stay silent.
- **If tweet id exists, never POST again.** Retry is lookup-only (REQ-F7).
- **Unverified** is not success. The operator sees it as still in the outbox.
- After max attempts, hold for a human Retry (resets the attempt counter).

## Dedicated account gate (REQ-F5)

Required before anything leaves the device:

1. Explicit acknowledgement: “this is a dedicated throwaway / low-trust bot
   account, not my main.”
2. API key
3. API secret
4. Access token
5. Access token secret

Handle is optional and only used to build `https://x.com/{handle}/status/{id}`.

Credentials live in localStorage on the desk. Never in git, never in a
world-writable database, never in server logs (REQ-SF4).

## Tweet shape (REQ-F2)

```
{ident} · {type}
{origin} → {dest}
FL{nnn} · {gs}kt · {duration}
filter {name} · {n} fixes
{lat0}, {lon0} → {lat1}, {lon1}
```

Factual. No hype. Truncate under 280.

## Traffic sources

| Source   | Alpha                         | Beta                          |
|----------|-------------------------------|-------------------------------|
| OpenSky  | live ADS-B bbox for theater   | still useful as fallback      |
| Demo     | Reach / heavies / 7700 inject | keep for tests                |
| FR24 app | not required                  | AccessibilityService harvest  |
| FR24 API | optional token, unused        | optional official feed        |

## Companion APK (beta only)

- Package-scoped to `com.flightradar24free`.
- API 30+.
- Overlay is chrome (REQ-M1).
- Foreign UI is a no-op (REQ-SF3).
- Never taps login / paywall chrome (REQ-SF2).
- Select → panel. If the marker is not a TalkBack node, screenshot+OCR of the
  **panel** is the fallback, not of the globe (REQ-M2, M5).
