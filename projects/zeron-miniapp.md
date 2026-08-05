# Zeron Temp Mail — Mini App (Telegram)

## Live
- URL: https://tmp-gmail-miniapp.vercel.app/
- Project: dev-zeron/tmp-gmail-miniapp (Vercel CLI: `node /data/data/com.termux/files/usr/lib/node_modules/vercel/dist/vc.js`)
- Git: /storage/emulated/0/Download/tmp-gmail-miniapp

## Backend (shared with bot)
- Proxy: `api/proxy.mjs` (Vercel serverless, @vercel/node) -> forwards to backend.
- Backend: https://zeron-gmail.vercel.app/api/{generate/{dot|plus|mixed|dotplus},read/{email},readby/{email}/{q},delete/{uid}}
- Auth: Bearer APP_PASS (Google App Password). Env on Vercel prod: APP_PASS.
- Bot + miniapp DUJE dw ton backend use kore => same data.

## Proxy API contract (frontend calls)
/api/proxy?method=read&email=X
/api/proxy?method=search&email=X&query=Q
/api/proxy?method=generate&type=dot|plus|mixed|dotplus
/api/proxy?method=delete&uid=U
Frontend sends header X-Telegram-InitData = tg.initData.

## Security (hardened 2026-08-05)
- Rate limit per IP (60/60s via map).
- Requires X-Telegram-InitData header. If TELEGRAM_BOT_TOKEN env set -> full HMAC verify (createHmac+timingSafeEqual); else presence check only.
- CORS allowlist origins (evil.org 403). GET-only. Input validation (email regex, uid digits, type whitelist). nosniff, no-store.
- vercel.json: proxy no-store. JS static HTML headers NOT applied by Vercel static builds (known limitation) — server caching is fine (max-age=0 must-revalidate).

## Bug fixes 2026-08-05
- Generate "hocche na" ROOT CAUSE: all fns inside IIFE, inline onclick in HTML runs in global scope -> "gen is not defined". FIX: expose on window: gen,copy,doSearch,backToList,setCustom,useGen,genAndUse.
- Verified runtime OK in Node mock (createTextNode added). Full-chain generate returns 200 real email.

## To Do
- Full initData HMAC: set TELEGRAM_BOT_TOKEN env (ask user for TBC bot token).
- Deploy cmd: node .../vercel/dist/vc.js --prod --yes
