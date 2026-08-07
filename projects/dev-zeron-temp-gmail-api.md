# DevZeron Temp Gmail API v1 (Vercel)

## Status: Active (v1.1.0 — docs website v3: mobile-first animated redesign)

## Last Updated: 2026-07-31

## Location
`/storage/emulated/0/Download/Temporary-Gmail-Vercel-Simple/`

## What This Is
Temporary Gmail API on Vercel free plan. Dot/plus/mixed/custom/batch alias generate, inbox read, text search, unread filter, count, trash, mark read/unread. No OAuth/Redis/DB.

## Vercel Project Name
`dev-zeron-temp-gmail-api-v1`

## Key Decision (user request) — CHANGED TO OPEN SOURCE (2026-07-31)
- **OPEN SOURCE MODE**: prottek user nijer Gmail + App Password diye use kore. Kono server-side secret lagbe na.
- Auth formats (parse_auth in api/index.py):
  1. `Authorization: Bearer <email>|<app-password>` (pipe separator, primary)
  2. `X-Gmail-Address: <email>` header + `Bearer <app-password>`
  3. `Bearer <app-password>` only → admin mode, needs GMAIL_ADDRESS env set
- Proti response e `"Api_By": "@DevZeron"` o `"Tg_Channel": "t.me/CodeDevZeron"`.

## Vercel Env
- `GMAIL_ADDRESS=zeronmodz@gmail.com`
- `GMAIL_APP_PASSWORD=vqae nhux pqez izqz` (real, in gitignored `.env` for local test)
- Env vars now OPTIONAL (admin mode only). Users send their own creds per request.

## Files
- `api/index.py` — main Flask app, all routes
- `api/aliases.py` — generate()/custom()/batch() logic
- `public/index.html` — docs website **v3**: mobile-first redesign. 43 inline SVG sprite icons (49 symbols), hamburger drawer nav + overlay, scroll progress bar, ambient floating glow blobs, animated gradient hero title, glassmorphism terminal + self-made "How it works" mail-flow SVG diagram (animated dashes + bobbing mails), hero animated counters, language marquee with edge fade, stats band with animated counters, card tilt on desktop hover (pointer:fine + no reduced-motion), FAQ accordion, spinner loading state in playground, safe-area padding for notched phones, touch targets ≥44px, prefers-reduced-motion respected. Copy buttons read sibling `<pre>`. 1583 tags, 107KB, HTMLParser clean
- `vercel.json` — rewrite `/api/(.*)` → `/api/index.py/$1` (new runtime passes destination path; StripRewritePrefix middleware strips `/api/index.py`; `both(*paths)` decorator registers `/path` + `/api/path`)
- `requirements.txt` — Flask
- `tests/test_aliases.py` — 8 unit tests
- `.env` (gitignored) / `.env.example` (placeholder)

## Endpoints
- `/api/generate/{dot|plus|mixed}` — random alias
- `/api/generate/custom/<tag>` — custom plus alias
- `/api/generate/batch/<count>?type=mixed` — 1-25 aliases
- `/api/read/<email>?limit=10&unread=1` — inbox
- `/api/readby/<email>/<text>` — full-text search
- `/api/unread/<email>?limit=10` — unread only
- `/api/count/<email>` — total + unread
- `/api/delete/<uid>` — move to Trash
- `/api/markread/<uid>` / `/api/markunread/<uid>` — flags
- `/api/health` (public) — IMAP + uptime
- `/api/info` (public) — endpoint list

## Verification (2026-07-31)
- 8/8 unit tests pass
- Live IMAP read test: READ 200, HEALTH 200 "IMAP connected, INBOX reachable"
- LIVE DEPLOY (open source auth): docs / 200, health 200, info 200, noauth 401, bad auth 401, generate mixed/custom/batch 200, read 200 (count 2), count 200 (total 306/unread 240), unread 200, format2 X-Gmail-Address 200 — all with `Bearer zeronmodz@gmail.com|vqae nhux pqez izqz`
- LIVE DEPLOY (docs v3): / 200 (107KB), sprite + drawer + burger present, API generate mixed still 200

## Design System
Dark dev theme: bg #020617, accent green #22C55E, Inter + JetBrains Mono. Docs site single HTML, tabs, responsive.

## Live URL
`https://dev-zeron-temp-gmail-api-v1.vercel.app`

## Trash fix (Gmail quirk)
Gmail rejects `\Trash` via FLAGS → `trash_message()` uses `client.uid("store", uid, "+X-GM-LABELS", "\\Trash")`.

## Vercel CLI on Termux
Run as `node /data/data/com.termux/files/usr/lib/node_modules/vercel/dist/vc.js` (symlink broken; Termux lacks /usr/bin/env). Deploy: `node .../vc.js deploy --prod --yes`.

## Next Steps
- DONE: open-source auth deployed + docs site live + README updated to `Bearer <email>|<app-pass>` format
- Optional: GitHub public repo e push korle users ei open-source API nije deploy korte parbe

## v4 — Material Design 3 rebuild (2026-07-31, Session 5)
- `public/index.html` fully rewritten with @material/web@2.5.0 Web Components (~29 component types), CDN via esm.run import map, M3 dynamic theming (seed #22C55E) + light/dark toggle + localStorage.
- Mobile-first single column (fixes mobile "one side empty"), bottom md-navigation-bar, desktop persistent md-navigation-drawer, modal drawer on mobile.
- Playground upgraded (slider/checkbox/radio/switch/select/text-fields/progress), endpoint filter chips + search, reference md-tabs + language md-menu, App Password md-dialog, back-to-top md-fab.
- Old custom 49-icon SVG sprite removed (Material Symbols icons used instead).
- Live: https://dev-zeron-temp-gmail-api-v1.vercel.app (verified 200, API intact, tests 8/8).
- Deploy cmd: `node /data/data/com.termux/files/usr/lib/node_modules/vercel/dist/vc.js deploy --prod --yes`

## v2 — KEY-BASED AUTH + FIREBASE + LIGHT-THEME REDESIGN (2026-08-07, Session 6)
- **Massive auth change (user request)**: `Bearer <email>|<app-password>` per-request is GONE. Now register once → permanent API key. Every call uses `Authorization: Bearer key_xxxx`.
- **New flow**: `/api/register?email=..&pass=..` (POST/GET, JSON/form) → REAL IMAP verify (imap.gmail.com:993) → issue `key_<32B urlsafe>` → store in Firebase → all endpoints resolve key→email→decrypted pw → IMAP for that request only.
- **Firebase storage** (project `zeron-a4f63`, RTDB REST via urllib, no service-account): paths `devzeron/keys_v1/users/<sha1hex-uid>` + `devzeron/keys_v1/keys/<key>`. Email path segment NOT allowed (`.` invalid) → uid = sha1hex(email). Auth via `?auth=<FIREBASE_DATABASE_SECRET or FIREBASE_API_KEY>`; DB rules currently open so apiKey works.
- **Encryption**: `crypto.py` Fernet(AES-GCM) encrypts stored App Passwords, key derived from `API_KEY_SECRET` + `FIREBASE_APP_ID` + per-email salt. Falls back to base64-obfuscation if `cryptography` missing (Termux build fail — Vercel installs fine).
- **Security hardening**: rate-limit register (6/120s per-IP sliding window, in-memory), safe_address input validation, harden headers middleware (nosniff, frame-deny, referrer, permissions-policy), password never echoed, /revoke deletes user+key, uid-based lookups.
- **New endpoints**: `/api/register`, `/api/key` (status), `/api/revoke`. delete/markread/markunread now support both `/delete/<uid>` and `?uid=`.
- **Frontend v5 — light theme, Instagram-trending**: pure-white canvas, hairlines (no heavy shadows), Instagram gradient #fcb045→#fd1d1d→#833ab4 reserved for brand/CTAs/story-rings, grotesk system font, product-led hero with live curl card, bento feature grid, animated counters, register→get-key panel, key-based playground, endpoint filter chips, code snippets (curl/py/node/php), security section, FAQ accordion, marquee, scroll reveal, prefers-reduced-motion, mobile drawer nav. No CDN deps (self-contained, ~59KB). Node --check + HTMLParser clean.
- **Vercel env (production) set via CLI**: FIREBASE_DATABASE_URL, FIREBASE_APP_ID, FIREBASE_API_KEY, API_KEY_SECRET (auto-generated random). FIREBASE_DATABASE_SECRET NOT set (rules open currently).
- **Tests**: 18 tests (8 alias + 5 crypto + 5 key-flow with mocked FB/IMAP) all pass (1 skip when cryptography absent).
- **Live verified (2026-08-07)**: register issued real key with zeronmodz@gmail.com real app-password → /api/key ok → /api/generate/mixed ok → /api/count total 340/unread 248 → revoke ok → reuse 401. Credit `Api_By`/`Tg_Channel` on every response incl 404.
- **Gotchas**: (1) `request.json` on form POST → 415 (use request.is_json guard). (2) Firebase path can't contain `.` → sha1hex uid. (3) `%40` in path keeps literal (safe_address rejects without @) — email in path must be plain `@`. (4) Flask `both()` dropped `/api` rule in rewrite → re-added. (5) index imports top-level `firebase`/`crypto` (sys.path=api/) ≠ `api.firebase` — patch module for tests via `module.firebase`.
- **⚠️ SECURITY TODO for Hasan**: Firebase RTDB rules currently open (anyone with public apiKey could write). Set `firebase.rules.json` (deny users/keys) + add `FIREBASE_DATABASE_SECRET` env on Vercel, or restrict rules to the server. README documents it.
- Deploy cmd: `node /data/data/com.termux/files/usr/lib/node_modules/vercel/dist/vc.js deploy --prod --yes`
