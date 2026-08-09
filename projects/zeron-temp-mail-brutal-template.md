# Zeron Temp Mail — Brutalist Template (Website)

## Status: Active · Complete v1.0
## Created: 2026-08-09 (Session 2026-08-09)
## Location: `/data/data/com.termux/files/home/zeron-temp-mail-brutal-template/`

## What This Is
DevZeron Temp Gmail API v2 (Vercel) er sob endpoint nia **single-file brutalist template website**. User shudhu app.js er TEMPLATE_CONFIG e API_KEY change kore je kono static host e deploy korlei hobe.

## Design System (applied)
- Palette: bg #F2EDE4 · ink #0b0b0b · red #ff2a00 · yellow #ffd60a · green #12b84a
- Type: Archivo Black / Inter / JetBrains Mono (Google Fonts CDN)
- Rules: radius 0 suffix, 2-3px black borders, hard offset shadows (6px 6px 0), uppercase mono meta, marquee, asymmetric hero, exposed grid (border-collapse).
- Trends 2026 (researched): tactile brutalism, typography-as-interface, bento grid, chromatic extremes, prefers-reduced-motion.

## Files
- `index.html` — full site (~35KB): nav, marquee, hero (live health dot + terminal + stats), how-it-works bento, endpoint matrix (15 endpoints: auth 3 / generate 5 / read 4 / manage 3 / system 2), dark playground (request builder + response panel), register panel, response-format sample, HTTP status table, security cards, FAQ accordion, footer, config drawer (API_BASE+API_KEY, localStorage), toast, back-to-top.
- `app.js` — logic + `TEMPLATE_CONFIG = { API_BASE, API_KEY }` (publish box). Complete ENDPOINTS registry (params/query per endpoint + ex values, desc, resp fields). Functions: renderMatrix, renderPlaygroundSelects (group filter chips), fieldBox (dynamic inputs, select for type), buildUrl (path <param> + query), run (fetch + Bearer key), highlightJson (escaped-text regex), healthCheck, register POST, FAQ, config drawer save.
- `README.md` — publish guide + endpoint ref table.

## Auth
`Authorization: Bearer key_xxxx` (alt: `X-API-Key`). Register: POST /api/register JSON {email, pass} → data.key.

## Verification (2026-08-09)
- node --check app.js OK; HTMLParser 0 errors; JS ids all matched.
- VM e2e: config-from-localStorage → run generate/mixed with key → fetch → status pill 200 OK + highlighted JSON PASS.
- Live API probes: health 200, info groups match, no-auth/bad-key → 401 JSON.

## Gotchas / Known
- Live server `/api/register` → 500 (HTML) for NON-@gmail.com emails (clean_email PermissionError unhandled). Valid @gmail.com → proper 401 JSON (covered by template).
- Playground mixed endpoint has no params → paramBox intentionally empty.
- `S` is module-scoped var (stateNow()); config change → refreshKeyUI + renderParams + updateReq + healthCheck.

## Dev Notes (for future edits)
- CONFIG drawer localStorage key: `zzer-config` ({base, key}).
- To change default key: edit TEMPLATE_CONFIG at top of app.js (2 lines) — that's the only "template-must-edit" spot.
- Deploy: any static host. No build step.