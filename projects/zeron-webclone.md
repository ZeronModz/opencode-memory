# 🧬 Zeron Web Clone Engine

> AI-powered website cloner — clone any website to exact ZIP (web app + Telegram bot).
> Status: **BUILT & TESTED locally** (2026-08-03). Vercel deploy pending.

## What It Does
- User gives any website URL → engine downloads full site, preserves EXACT folder structure, rewrites all paths to offline-relative, AI analyzes & auto-fixes code, injects brand footer, packs ZIP, returns download.
- Same engine powers: web app (`/api/clone`) + Telegram bot (webhook or polling).

## Location
- `/data/data/com.termux/files/home/zeron-webclone/` (git repo, main branch, initial commit done)

## Tech Stack
- Node.js (ESM), Vercel serverless (`api/*.js`), cheerio (HTML parse), axios (fetch), archiver (ZIP), native fetch+FormData for Telegram.
- AI: OpenRouter.

## Files
```
api/clone.js        # GET /api/clone?url=&pages=&info=1 → zip / JSON
api/telegram.js     # POST webhook → clone → sendDocument
lib/cloner.js       # core engine (cloneSiteToZip)
lib/telegram.js     # TG API wrapper (sendMessage/sendDocument/etc)
lib/botLogic.js     # shared bot update handling
bot/setup.js        # setWebhook helper
bot/poll.js         # local long-polling bot
public/index.html   # landing page
.env.example        # env template
```

## Env Vars (set in Vercel)
- `OPENROUTER_API_KEY` = sk-or-v1-2792c97c... (user's key)
- `OPENROUTER_MODEL` = nvidia/nemotron-3.5-content-safety:free (user requested)
- `OPENROUTER_FALLBACK_MODELS` = nvidia/nemotron-3-super-120b-a12b:free,nvidia/nemotron-3-nano-30b-a3b:free,openrouter/free
- `TELEGRAM_BOT_TOKEN` = (needed for bot)
- `MAX_PAGES` = 5 (deep-clone limit)

## AI Fallback Chain (IMPORTANT LESSON)
User's requested model `nvidia/nemotron-3.5-content-safety:free` is a **content-safety classifier**, NOT a general chat model → returns "User Safety: safe" instead of JSON. So engine tries it first, detects non-JSON/safety output, then falls back to `nvidia/nemotron-3-super-120b-a12b:free` (verified: returns clean JSON, also nvidia, free). See learned/.

## Tested Results
- example.com → 1 file, 1.7KB zip ✅
- iana.org → 23 files, 8.7MB zip, structure preserved (static/css, js, img, fonts, icons) ✅
- iana.org/domains pages=4 → 45 files, sub-pages as `about/index.html`, `protocols/index.html`, nav `<a href>` rewritten to offline paths ✅
- Brand footer `Clone By @DevZeron` + CSS/JS comment headers + meta generator injected ✅
- AI found real issues (missing meta description, redundant charset) & auto-fixed meta ✅

## Vercel Deploy (todo)
Vercel CLI broken in Termux (`/usr/bin/env: no such file or directory`). Deploy via GitHub:
1. push zeron-webclone to GitHub
2. vercel.com → New Project → import repo
3. set env vars above
4. deploy
5. optional bot: `TELEGRAM_BOT_TOKEN=xxx node bot/setup.js https://APP.vercel.app/api/telegram`

## Limits / Notes
- Vercel Hobby: function timeout ~10-60s, response ~4.5-6MB → heavy sites may exceed. Bot on VPS/PC (`node bot/poll.js`) = unlimited.
- Cloudflare-protected sites get blocked.
- Educational/personal use only.
