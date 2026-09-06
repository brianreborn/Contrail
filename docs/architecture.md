# Architecture — Contrail

## Layers

```
┌─────────────────────────────────────────────────────────┐
│ Overlay chrome                                          │
│ arm · follow · kill · X handle / session                │
│ NEVER holds track data or passwords                     │
└─────────────────────────────────────────────────────────┘
            │ select by ICAO24 / X handle (never slot)
            ▼
┌─────────────────────────────────────────────────────────┐
│ Harvest (FR24)                                          │
│ ident plate (structured)                                │
│ globe is canvas — do not OCR coordinates off the map    │
└─────────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────┐
│ Processing + vault                                      │
│ stitch waypoints by ICAO24                              │
│ dedicated handle + login live here, not on the overlay  │
└─────────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────┐
│ Outbox (local, durable)                                 │
│ queued → posting → posted | unverified | failed         │
│ drafts never dropped                                    │
└─────────────────────────────────────────────────────────┘
            │ primary: X Android app
            ▼
┌─────────────────────────────────────────────────────────┐
│ com.twitter.android                                     │
│ switcher by handle → login-if-needed → composer → Post  │
│ verify on dedicated profile                             │
└─────────────────────────────────────────────────────────┘
```

Companion allowlist: `com.flightradar24free` and `com.twitter.android`.
Foreign UI is a no-op (REQ-SF3). Overlay never tweets (REQ-SF5).

## X app sequence (REQ-F9 / F10 / M7)

```
outbox item
    │
    ▼
ensure X app foreground
    │
    ▼
read current handle
    │
    ├─ already dedicated ──────────────────────┐
    │                                          │
    ├─ switcher lists dedicated ── tap handle ─┤
    │     (identity, never slot)               │
    │                                          │
    └─ not listed / logged out                 │
           │                                   │
           ▼                                   │
      add account / login                      │
      SET_TEXT identifier                      │
      SET_TEXT password (OS field only)        │
      tap Log in                               │
      2FA → fall through to operator           │
           │                                   │
           ▼                                   ▼
      composer ← draft from outbox
           │
           ▼
         Post
           │
           ▼
      own profile / tweet_detail
           │
           ├─ text (+ id) match → posted
           └─ no match → unverified, keep outbox
```

## Credential handling (REQ-SF4 / SF5)

| Surface | Handle | Username | Password |
|---|---|---|---|
| Overlay | yes (identity) | no | **never** |
| Logs | yes | no | **never** |
| Screenshots | FR24 panel only | no | **never** (login screens forbidden) |
| Vault | yes | yes | yes (Keystore / localStorage) |
| Accessibility SET_TEXT | — | identifier field | password field |
| TalkBack announcement | handle ok | no | **must not speak** |

## Outbox state machine

Same as before, with `via: app | api` and `targetHandle` on every item.

- Kill skips posting **and** aborts in-flight login.
- Census filters stay silent.
- If a tweet id exists, retry is verify-only.
- API is desk-only fallback when the X-app vault is empty. Companion never uses API keys.

## Traffic sources

| Source   | Alpha                         | Beta                          |
|----------|-------------------------------|-------------------------------|
| OpenSky  | live ADS-B bbox for theater   | still useful as fallback      |
| Demo     | Reach / heavies / 7700 inject | keep for tests                |
| FR24 app | not required                  | AccessibilityService harvest  |
| X app    | sequence simulated on desk    | companion drives real app     |
| FR24 API | optional token, unused        | optional official feed        |
