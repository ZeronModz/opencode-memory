# ZERON TEMP MAIL — brutal template (real app)

## Status: Active v2 (real temp-mail app, deployed)
## Last Updated: 2026-08-09 23:15
## Location
`/data/data/com.termux/files/home/zeron-temp-mail-brutal-template/`

## What This Is
Working temp-mail web app (temp-mail.org style) — NOT a docs/API-page anymore.
- Visitor load → auto random alias → inbox live → read OTP → search → delete.
- Brutalist design, **mobile-first**, all devices/screens. 0 desktop-centrism.
- API key **server-side**: Vercel env var `ZERON_API_KEY`, never in client/repo.

## Links
- Repo: https://github.com/ZeronModz/zeron-temp-mail-brutal-template
- Live: https://zeron-temp-mail-brutal-template.vercel.app
- Upstream API: https://dev-zeron-temp-gmail-api-v1.vercel.app (v2 key-based)

## Files
- `index.html` — brutalist mobile-first UI (no framework, ~40KB, all CSS inline)
- `app.js` — client logic (proxy-first, direct fallback w/ standalone key, localStorage)
- `api/index.js` — Vercel serverless proxy (env key inject, POST /api JSON `{path,query}`)
- `README.md` — shieldcn badge stats + env var guide + API ref

## Env vars (host only)
- `ZERON_API_KEY` = `key_EnsATw-vmIygEqHRTkvNAMmX7NNwDrI6BBZxl78lF0E` (registered 2026-08-09 for zeronmodz@gmail.com + app pass; PERMANENT in Firebase)
- `ZERON_API_BASE` optional (default upstream)

## Proxy contract (frontend → /api)
POST /api { "path":"generate/mixed" | "read/<email>", "query":{...}, "method", "data" }
Proxy adds `Authorization: Bearer <env key>`. Client sends NO auth.
Frontend tries /api → 404/405 → fallback direct (standalone key from CONFIG).

## Critical gotchas (do NOT re-break)
1. **Email in API path MUST keep plain `@`** (not %40). Minus `%2B` korleo accept, but pathEnc() keeps @ + . _ chars raw karon `+` alias server accept kore. `encodeURIComponent(email)` -> 500 "Invalid mailbox address". Use pathEnc().
2. app.js MUST have exactly one `function $(id){...}` — a 2026-08-09 edit deleted it accidentally and broke the whole site (`$ is not defined`). Smoke #require catches it.
3. `readby/<email>/<text>` NEEDS text — without it → 404 Route not found.
4. `/api/read` returns newest-first, `body` = text/plain only (HTML-only → empty; UI shows fallback note).
5. Vercel CLI on Termux: `node /data/.../usr/lib/node_modules/vercel/dist/vc.js deploy --prod --yes`.
6. Vercel zero-config: public files + `api/` folder function deploy together normally.

## Verified (2026-08-09)
- Live proxy: health/generate/read/readby all 200 via POST /api without client key.
- Browser flow simulation (headless smoke): init→generate→list render→reader OK.
- HTML parser clean, node --check clean on all JS.
- GitHub main pushed (d101b74), Vercel prod aliased to base URL.

## Next ideas (user may ask)
- Auto-refresh interval settings, custom alias input, inbox all-clear button, dark theme, React/Svelte ports.