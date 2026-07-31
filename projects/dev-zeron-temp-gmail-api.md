# DevZeron Temp Gmail API v1 (Vercel)

## Status: Active (v1.0.0 — Full rebuild + docs website)

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
- `public/index.html` — full documentation website (dark dev theme, 6 language tabs curl/Python/Node/PHP/Go/Java, live Playground tester, marquee, SVG icons). Copy buttons read from sibling `<pre>` (no data-copy attrs — HTML quoting bug)
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
