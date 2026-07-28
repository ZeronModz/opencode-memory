# Tmap Gmail Mini App

## Status: Active (Rebuilt v2)

## Last Updated: 2026-07-28 23:44

## Description
Telegram Mini App for Tmap Gmail bot. Brutalism design. Users can view their Gmail accounts, read inbox, generate new email addresses, and manage settings — all inside Telegram.

## Tech Stack
- Single HTML file (inline CSS + JS, zero external dependencies)
- Telegram WebApp SDK v6+ (loaded from CDN)
- Vercel (serverless + static hosting)
- Vercel serverless function: `api/proxy.mjs`
- Backend API: `https://zeron-gmail.vercel.app/api/`

## Files (in `/storage/emulated/0/Download/tmp-gmail-miniapp/`)
- `index.html` — Everything: HTML, CSS (inline `<style>`), JS (inline `<script>`)
- `api/proxy.mjs` — Vercel serverless function (reads APP_PASS from env, proxies to backend)
- `vercel.json` — Vercel deployment config (static + node + CSP)

## Key Architecture Changes (v2)
- **No ES modules** — all JS is inline regular script (no import/export issues in WebView)
- **No external CSS** — CSS inline in `<style>` tag (no path resolution issues)
- **APP_PASS from Vercel env** — API password set as Vercel environment variable, not localStorage
- **API Proxy** — Frontend calls `/api/proxy?method=...`, serverless function adds Bearer token
- **Brutalism v2** — 4px borders, 900 bold everywhere, pure primary colors, no transitions

## Features
- Bottom navigation: Profile, Inbox, Generate, Settings
- Telegram user info (avatar, name, username, premium status)
- Account list with detail (view inbox, copy, delete)
- Inbox with pagination + search
- Generate accounts: Dot Trick, Plus Alias, Mixed, Custom
- API password via Vercel env var (APP_PASS)
- Export accounts, Tutorial, Developer info
- Toast notifications, modal system

## Vercel Environment Variables
- `APP_PASS` — must match the bot's api_pass (set via `/zeron-pass`)

## Deployment
1. Push to GitHub
2. Import to Vercel
3. Set `APP_PASS` env var in Vercel project settings
4. Set Mini App URL in BotFather

## Notes
- Brutalism design: pure black/white, 4px borders, monospace (Courier New), yellow (#FF0) accent, red (#F00) destructive, 0px radius, 0s transitions
- No build step needed — Vercel serves directly
