# ZERON TEMP MAIL — brutal template (real app)

## Status: Active v2 (real temp-mail app, deployed)
## Last Updated: 2026-08-10 17:15
## Location
`/data/data/com.termux/files/home/zeron-temp-mail-brutal-template/`

## What This Is
Working temp-mail web app (temp-mail.org style) — NOT a docs/API-page anymore.
- Visitor load → auto random alias → inbox live → read OTP → search → delete.
- Brutalist design, **mobile-first**, all devices/screens. 0 desktop-centrism.
- API key **server-side** (env or file/php), never in client/repo.

## v2.1 — Universal host support (2026-08-10)
Now deploys on **EVERYTHING**, not just Vercel. No secrets in repo. User jig
edit kore nite pare. Files:
- `proxy.js` — ★ universal proxy CORE (env-only: ZERON_API_KEY|API_KEY + ZERON_API_BASE). Export: run(), parseBody(), expressHandler(req,res), eventHandler(body). Zero-dep Node >=14. Config.json deliberate removed from core (serverless esbuild bundler could break) → config.json bokhola Node server-only feature.
- `api/index.js` — Vercel adapter (thin re-export of proxy.expressHandler).
- `server.js` — standalone Node server (serves static + POST /api). Env PORT default 3000. Reads config.json at boot (cp config.example.json→config.json). Config.json/Js/proxy ke kokhon serve kore na → 404 (key leak protection).
- `netlify/functions/api.js` + `netlify.toml` (node_bundler=esbuild) + `_redirects` (`/api /.netlify/functions/api 200`) — Netlify.
- `functions/api.js` — Cloudflare Pages Function, SELF-CONTAINED copy (workerd runtime: no require of core, no node builtins; reads context.env.ZERON_API_KEY). Auto-routes /api.
- `api.php` — PHP 7+ cURL proxy for cPanel/Hostinger/shared hosting. $API_KEY top of file (+ env ZERON_API_KEY fallback). NOTE: PHP 8.5 curl_close() is deprecated → guard `if (PHP_VERSION_ID < 80000) curl_close($ch);` ar navi PHP>=8 emit notice breaks JSON.
- `package.json` (start: node server.js, engines >=14), `Dockerfile` (node:18-alpine, node server.js, PORT 3000), `config.example.json`, `.env.example`.
- `.gitignore` += `.netlify/ node_modules/ .env config.json`.

## Frontend proxy auto-detection (app.js)
TEMPLATE_CONFIG.PROXY_PATHS = ["/api", "/api.php"] → tries in order; a proxy jeta
real JSON bonus ta use hobe. 404/405/501 OR non-JSON body (static host serving
index.html) = "noproxy" → next candidate → final fallback: direct API with
standalone key (CONFIG drawer). 501 add kora (python http.server static dev).

## Links
- Repo: https://github.com/ZeronModz/zeron-temp-mail-brutal-template
- Live: https://zeron-temp-mail-brutal-template.vercel.app
- Upstream API: https://dev-zeron-temp-gmail-api-v1.vercel.app (v2 key-based)

## Env vars (host only)
- `ZERON_API_KEY` = `key_EnsATw-vmIygEqHRTkvNAMmX7NNwDrI6BBZxl78lF0E` (registered 2026-08-09 for zeronmodz@gmail.com + app pass; PERMANENT in Firebase)
- `ZERON_API_BASE` optional (default upstream)

## Verified (2026-08-10)
- server.js live test: GET / 200, /app.js 200; /server.js & /config.json 404 (protected); POST /api health+generate 200; no-key → 500 clear msg; OPTIONS 204.
- config.json fallback: key without env var works.
- api.php live test (PHP 8.5 built-in server): health + readby raw @ 200 after curl_close guard fix. No-key hardcoded empty → 500 msg.
- python http.server static sim: /api POST → 501 (handled as noproxy).

## Proxy contract (frontend → /api)
POST /api { "path":"generate/mixed" | "read/<email>", "query":{...}, "method", "data" }
Proxy adds `Authorization: Bearer <key>`. Client sends NO auth.

## Critical gotchas (do NOT re-break)
1. **Email in API path MUST keep plain `@`** (not %40). Minus `%2B` korleo accept, but pathEnc() keeps @ + . _ chars raw karon `+` alias server accept kore. `encodeURIComponent(email)` -> 500 "Invalid mailbox address". Use pathEnc().
2. app.js MUST have exactly one `function $(id){...}` — a 2026-08-09 edit deleted it accidentally and broke the whole site (`$ is not defined`). Smoke #require catches it.
3. `readby/<email>/<text>` NEEDS text — without it → 404 Route not found.
4. `/api/read` returns newest-first, `body` = text/plain only (HTML-only → empty; UI shows fallback note).
5. Vercel CLI on Termux: `node /data/.../usr/lib/node_modules/vercel/dist/vc.js deploy --prod --yes`.
6. **proxy.js must stay ENV-only** — don't re-add require("./config.json"): missing file → esbuild bundler error breaks Vercel/Netlify/CF builds. config.json load ONLY in server.js via fs.
7. **CF functions/api.js self-contained** — no require(proxy)/node builtin. Check parity when editing core.
8. Python http.server static dev → POST returns 501; frontend treats 404/405/501/non-JSON as "no proxy".

## Next ideas
- Auto-refresh interval settings, custom alias input, inbox all-clear button, dark theme, React/Svelte ports, Deno server variant.