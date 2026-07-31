# DevZeron Temp Gmail API v1 (Vercel)

## Status: Active (v1.0.0 — Full rebuild + docs website)

## Last Updated: 2026-07-31

## Location
`/storage/emulated/0/Download/Temporary-Gmail-Vercel-Simple/`

## What This Is
Temporary Gmail API on Vercel free plan. Dot/plus/mixed/custom/batch alias generate, inbox read, text search, unread filter, count, trash, mark read/unread. No OAuth/Redis/DB.

## Vercel Project Name
`dev-zeron-temp-gmail-api-v1`

## Key Decision (user request)
- **GMAIL_ADDRESS + GMAIL_APP_PASSWORD** shudhu Vercel env vars theke. Code e kothao hardcoded nei.
- App Password = API secret. Same `Authorization: Bearer <16-char>` header for all protected endpoints.
- Proti response e `"Api_By": "@DevZeron"` o `"Tg_Channel": "t.me/CodeDevZeron"`.

## Vercel Env
- `GMAIL_ADDRESS=zeronmodz@gmail.com`
- `GMAIL_APP_PASSWORD=vqae nhux pqez izqz` (real, in gitignored `.env` for local test)

## Files
- `api/index.py` — main Flask app, all routes
- `api/aliases.py` — generate()/custom()/batch() logic
- `public/index.html` — full documentation website (dark dev theme, tabs curl/Python/Node/PHP)
- `vercel.json` — rewrite `/api/(.*)` → `/api/index.py`
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

## Design System
Dark dev theme: bg #020617, accent green #22C55E, Inter + JetBrains Mono. Docs site single HTML, tabs, responsive.

## Next Steps
- User github repo e push + Vercel deploy korbe. Project name: dev-zeron-temp-gmail-api-v1
