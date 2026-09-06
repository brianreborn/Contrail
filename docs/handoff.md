# Handoff — grok bot / Swarm Conductor

R&D queue after 2026-09-05. Public repo = docs and research. Implementation
goes to a **private** repository (REQ-P3).

## Operator intent (locked)

- Aircraft source: Flightradar24 (Android app + custom filters).
- Capture: accessibility overlay + processing layer (UMAssisted-shaped).
- **Poster: the X Android app**, not a personal handle, not the happy-path API.
- Dedicated low-trust account. Switch to it by handle. Log in only if that
  handle is missing. Credentials never appear on the overlay.
- Outbox holds anything that does not land. Landed means the dedicated
  profile shows the tweet.

## Do next (in order)

### 1. Private implementation repo

Create `Contrail-private`. Port the desk + X session machine
(`src/lib/x-session.ts` in the sandbox). Do not put source here.

### 2. Research X app accessibility (OQ-X2, OQ-X3, OQ-X4)

On a device with the dedicated account **and** a personal account signed into
X:

1. Dump the account switcher node tree. Confirm handles are readable.
2. Dump login identifier + password screens. Confirm password does not echo
   into events Contrail would log.
3. Post one tweet by hand, dump tweet_detail / profile, see if status id is a
   node.
4. Commit labeled screenshots of **chrome only** (no password, no personal
   timeline PII if avoidable).

### 3. Research FR24 (OQ-FR1, FR2)

Unchanged from prior handoff.

### 4. Companion APK spike (private)

Allowlist both packages. Named sequence `x.ensure_session` then
`x.compose_post`. Kill aborts login. 2FA falls through.

## What not to do

- Do not tap a switcher row by index.
- Do not paint the password on the overlay, in logs, or in screenshots.
- Do not automate Google/Apple SSO or 2FA.
- Do not tweet from a personal handle “just to test.”
- Do not treat composer-Post without a profile match as success.
- Do not commit credentials.
