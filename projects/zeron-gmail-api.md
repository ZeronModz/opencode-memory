# Zeron Gmail API (zeron-gmail.vercel.app)

## Status: Active
## Live: https://zeron-gmail.vercel.app (Vercel project `dev-zeron/zeron-gmail`)
## Source: `/storage/emulated/0/Download/zeron-gmail-main.zip` (extract → `api/index.py`)
## Auth: `Authorization: Bearer <GMAIL_APP_PASSWORD>` (env: GMAIL_ADDRESS, GMAIL_APP_PASSWORD)

## What This Is
Simple Vercel Flask Gmail IMAP API. Routes: `/api/generate/{dot|dotplus|plus|mixed}`, `/api/read/<email>`, `/api/readby/<email>/<text>`, `/api/delete/<uid>`. One request = one IMAP connection. This is the BACKEND shared by zeron-telebot + tmp-gmail-miniapp (NOT the new V2 key-based API at Temporary-Gmail-Vercel-Simple).

## HTML body fix (2026-08-08)
- Problem: HTML-only mails (TeraBox/OTP) → `body` empty "".
- Fix `api/index.py`: `body()` now falls back text/plain→text/html; `_strip_html()` removes style/script, unescape, collapses space; new `body_html` field with raw HTML.
- ⚠️ DEPLOY MUST: `vercel.json` rewrite destination = `"/api/index.py/$1"` + `StripRewritePrefix` WSGI middleware (new Vercel runtime routes by destination path, without it all `/api/*` = 404). Confirmed live.

## Deploy (Termux)
- Folder: extract of zeron-gmail-main.zip (has `.vercel` linked now)
- Cmd: `node /data/data/com.termux/files/usr/lib/node_modules/vercel/dist/vc.js deploy --prod --yes`
- CLI logged in as `zeronmodz`.